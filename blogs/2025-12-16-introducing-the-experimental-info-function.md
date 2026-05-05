---
title: "Introducing the Experimental info() Function"
url: "https://prometheus.io/blog/2025/12/16/introducing-info-function/"
date: "2025-12-16T00:00:00.000Z"
author: "Arve Knudsen"
feed_url: "https://prometheus.io/blog/feed.xml"
---
<p>Enriching metrics with metadata labels can be surprisingly tricky in Prometheus, even if you're a PromQL wiz!
The PromQL join query traditionally used for this is inherently quite complex because it has to specify the labels to join on, the info metric to join with, and the labels to enrich with.
The new, still experimental <code>info()</code> function, promises a simpler way, making label enrichment as simple as wrapping your query in a single function call.</p>
<p>In Prometheus 3.0, we introduced the <a href="https://prometheus.io/docs/prometheus/latest/querying/functions/#info"><code>info()</code></a> function, a powerful new way to enrich your time series with labels from info metrics.
What's special about <code>info()</code> versus the traditional join query technique is that it relieves you from having to specify <em>identifying labels</em>, which info metric(s) to join with, and the ("data" or "non-identifying") labels to enrich with.
Note that "identifying labels" in this particular context refers to the set of labels that identify the info metrics in question, and are shared with associated non-info metrics.
They are the labels you would join on in a Prometheus <a href="https://grafana.com/blog/2021/08/04/how-to-use-promql-joins-for-more-effective-queries-of-prometheus-metrics-at-scale">join query</a>.
Conceptually, they can be compared to <a href="https://en.wikipedia.org/wiki/Foreign_key">foreign keys</a> in relational databases.</p>
<p>Beyond the main functionality, <code>info()</code> also solves a subtle yet critical problem that has plagued join queries for years: The "churn problem" that causes queries to fail when non-identifying info metric labels change, combined with missing staleness marking (as is the case with OTLP ingestion).</p>
<p>Whether you're working with OpenTelemetry resource attributes, Kubernetes labels, or any other metadata, the <code>info()</code> function makes your PromQL queries cleaner, more reliable, and easier to understand.</p>
<h2>The Problem: Complex Joins and The Churn Problem</h2>
<p>Let us start by looking at what we have had to do until now.
Imagine you're monitoring HTTP request durations via OpenTelemetry and want to break them down by Kubernetes cluster.
You push your metrics to Prometheus' OTLP endpoint.
Your metrics have <code>job</code> and <code>instance</code> labels, but the cluster name lives in a separate <code>target_info</code> metric, as the <code>k8s_cluster_name</code> label.
Here's what the traditional approach looks like:</p>
<pre><code class="language-promql">sum by (http_status_code, k8s_cluster_name) (
    rate(http_server_request_duration_seconds_count[2m])
  * on (job, instance) group_left (k8s_cluster_name)
    target_info
)
</code></pre>
<p>While this works, there are several issues:</p>
<p><strong>1. Complexity:</strong> You need to know:</p>
<ul>
<li>Which info metric contains your labels (<code>target_info</code>)</li>
<li>Which labels are the "identifying" labels to join on (<code>job</code>, <code>instance</code>)</li>
<li>Which data labels you want to add (<code>k8s_cluster_name</code>)</li>
<li>The proper PromQL join syntax (<code>on</code>, <code>group_left</code>)</li>
</ul>
<p>This requires expert-level PromQL knowledge and makes queries harder to read and maintain.</p>
<p><strong>2. The Churn Problem (The Critical Issue):</strong></p>
<p>Here's the subtle but serious problem: What happens when an OTel resource attribute changes in a Kubernetes container, while the identifying resource attributes stay the same?
An example could be the resource attribute <code>k8s.pod.labels.app.kubernetes.io/version</code>.
Then the corresponding <code>target_info</code> label <code>k8s_pod_labels_app_kubernetes_io_version</code> changes, and Prometheus sees a completely new <code>target_info</code> time series.</p>
<p>As the OTLP endpoint doesn't mark the old <code>target_info</code> series as stale, both the old and new series can exist simultaneously for up to 5 minutes (the default lookback delta).
During this overlap period, your join query finds <strong>two distinct matching <code>target_info</code> time series</strong> and fails with a "many-to-many matching" error.</p>
<p>This could in practice mean your dashboards break and your alerts stop firing when infrastructure changes are happening, perhaps precisely when you would need visibility the most.</p>
<h3>The Info Function Presents a Solution</h3>
<p>The previous join query can be converted to use the <code>info</code> function as follows:</p>
<pre><code class="language-promql">sum by (http_status_code, k8s_cluster_name) (
  info(rate(http_server_request_duration_seconds_count[2m]))
)
</code></pre>
<p>Much more comprehensible, isn't it?
As regards solving the churn problem, the real magic happens under the hood: <strong><code>info()</code> automatically selects the time series with the latest sample</strong>, eliminating churn-related join failures entirely.
Note that this call to <code>info()</code> returns all data labels from <code>target_info</code>, but it doesn't matter because we aggregate them away with <code>sum</code>.</p>
<h2>Basic Syntax</h2>
<pre><code class="language-promql">info(v instant-vector, [data-label-selector instant-vector])
</code></pre>
<ul>
<li><strong><code>v</code></strong>: The instant vector to enrich with metadata labels</li>
<li><strong><code>data-label-selector</code></strong> (optional): Label matchers in curly braces to filter which labels to include</li>
</ul>
<p>In its most basic form, omitting the second parameter, <code>info()</code> adds <strong>all</strong> data labels from <code>target_info</code>:</p>
<pre><code class="language-promql">info(rate(http_server_request_duration_seconds_count[2m]))
</code></pre>
<p>Through the second parameter on the other hand, you can control which data labels to include from <code>target_info</code>:</p>
<pre><code class="language-promql">info(
  rate(http_server_request_duration_seconds_count[2m]),
  {k8s_cluster_name=~".+"}
)
</code></pre>
<p>In the example above, <code>info()</code> includes the <code>k8s_cluster_name</code> data label from <code>target_info</code>.
Because the selector matches any non-empty string, it will include any <code>k8s_cluster_name</code> label value.</p>
<p>It's also possible to filter which <code>k8s_cluster_name</code> label values to include:</p>
<pre><code class="language-promql">info(
  rate(http_server_request_duration_seconds_count[2m]),
  {k8s_cluster_name="us-east-0"}
)
</code></pre>
<h2>Selecting Different Info Metrics</h2>
<p>By default, <code>info()</code> uses the <code>target_info</code> metric.
However, you can select different info metrics (like <code>build_info</code> or <code>node_uname_info</code>) by including a <code>__name__</code> matcher in the data-label-selector:</p>
<pre><code class="language-promql"># Use build_info instead of target_info
info(up, {__name__="build_info"})

# Use multiple info metrics (combines labels from both)
info(up, {__name__=~"(target|build)_info"})

# Select build_info and only include the version label
info(up, {__name__="build_info", version=~".+"})
</code></pre>
<p><strong>Note:</strong> The current implementation always uses <code>job</code> and <code>instance</code> as the identifying labels for joining, regardless of which info metric you select.
This works well for most standard info metrics but may have limitations with custom info metrics that use different identifying labels.
An example of an info metric that has different identifying labels than <code>job</code> and <code>instance</code> is <code>kube_pod_labels</code>, its identifying labels are instead: <code>namespace</code> and <code>pod</code>.
The intention is that <code>info()</code> in the future knows which metrics in the TSDB are info metrics and automatically uses all of them, unless the selection is explicitly restricted by a name matcher like the above, and which are the identifying labels for each info metric.</p>
<h2>Real-World Use Cases</h2>
<h3>OpenTelemetry Integration</h3>
<p>The primary driver for the <code>info()</code> function is <a href="https://prometheus.io/blog/2024/03/14/commitment-to-opentelemetry/">OpenTelemetry</a> (OTel) integration.
When using Prometheus as an OTel backend, resource attributes (metadata about the metrics producer) are automatically converted to the <code>target_info</code> metric:</p>
<ul>
<li><code>service.instance.id</code> → <code>instance</code> label</li>
<li><code>service.name</code> → <code>job</code> label</li>
<li><code>service.namespace</code> → prefixed to <code>job</code> (i.e., <code>&#x3c;namespace>/&#x3c;service.name></code>)</li>
<li>All other resource attributes → data labels on <code>target_info</code></li>
</ul>
<p>This means that, so long as at least either the <code>service.instance.id</code> or the <code>service.name</code> resource attribute is included, every OTel metric you send to Prometheus over OTLP can be enriched with resource attributes using <code>info()</code>:</p>
<pre><code class="language-promql"># Add all OTel resource attributes
info(rate(http_server_request_duration_seconds_sum[5m]))

# Add only specific attributes
info(
  rate(http_server_request_duration_seconds_sum[5m]),
  {k8s_cluster_name=~".+", k8s_namespace_name=~".+", k8s_pod_name=~".+"}
)
</code></pre>
<h3>Build Information</h3>
<p>Enrich your metrics with build-time information:</p>
<pre><code class="language-promql"># Add version and branch information to request rates
sum by (job, http_status_code, version, branch) (
  info(
    rate(http_server_request_duration_seconds_count[2m]),
    {__name__="build_info"}
  )
)
</code></pre>
<h3>Filter on Producer Version</h3>
<p>Pick only metrics from certain producer versions:</p>
<pre><code class="language-promql">sum by (job, http_status_code, version) (
  info(
    rate(http_server_request_duration_seconds_count[2m]),
    {__name__="build_info", version=~"2\\..+"}
  )
)
</code></pre>
<h2>Before and After: Side-by-Side Comparison</h2>
<p>Let's see how the <code>info()</code> function simplifies real queries:</p>
<h3>Example 1: OpenTelemetry Resource Attribute Enrichment</h3>
<p><strong>Traditional approach:</strong></p>
<pre><code class="language-promql">sum by (http_status_code, k8s_cluster_name, k8s_namespace_name, k8s_container_name) (
    rate(http_server_request_duration_seconds_count[2m])
  * on (job, instance) group_left (k8s_cluster_name, k8s_namespace_name, k8s_container_name)
    target_info
)
</code></pre>
<p><strong>With info():</strong></p>
<pre><code class="language-promql">sum by (http_status_code, k8s_cluster_name, k8s_namespace_name, k8s_container_name) (
  info(rate(http_server_request_duration_seconds_count[2m]))
)
</code></pre>
<p>The intent is much clearer with <code>info</code>: We're enriching <code>http_server_request_duration_seconds_count</code> with Kubernetes related OpenTelemetry resource attributes.</p>
<h3>Example 2: Filtering by Label Value</h3>
<p><strong>Traditional approach:</strong></p>
<pre><code class="language-promql">sum by (http_status_code, k8s_cluster_name) (
    rate(http_server_request_duration_seconds_count[2m])
  * on (job, instance) group_left (k8s_cluster_name)
    target_info{k8s_cluster_name=~"us-.*"}
)
</code></pre>
<p><strong>With info():</strong></p>
<pre><code class="language-promql">sum by (http_status_code, k8s_cluster_name) (
  info(
    rate(http_server_request_duration_seconds_count[2m]),
    {k8s_cluster_name=~"us-.*"}
  )
)
</code></pre>
<p>Here we filter to only include metrics from clusters in the US (which names start with <code>us-</code>). The <code>info()</code> version integrates the filter naturally into the data-label-selector.</p>
<h2>Technical Benefits</h2>
<p>Beyond the fundamental UX benefits, the <code>info()</code> function provides several technical advantages:</p>
<h3>1. Automatic Churn Handling</h3>
<p>As previously mentioned, <code>info()</code> automatically picks the matching info time series with the latest sample when multiple versions exist.
This eliminates the "many-to-many matching" errors that plague traditional join queries during churn.</p>
<p><strong>How it works:</strong> When non-identifying info metric labels change (e.g., a pod is re-created), there's a brief period where both old and new series might exist.
The <code>info()</code> function simply selects whichever has the most recent sample, ensuring your queries keep working.</p>
<h3>2. Better Performance</h3>
<p>The <code>info()</code> function is more efficient than traditional joins:</p>
<ul>
<li>Only selects matching info series</li>
<li>Avoids unnecessary label matching operations</li>
<li>Optimized query execution path</li>
</ul>
<h2>Getting Started</h2>
<p>The <code>info()</code> function is experimental and must be enabled via a feature flag:</p>
<pre><code class="language-bash">prometheus --enable-feature=promql-experimental-functions
</code></pre>
<p>Once enabled, you can start using it immediately.</p>
<h2>Current Limitations and Future Plans</h2>
<p>The current implementation is an <strong>MVP (Minimum Viable Product)</strong> designed to validate the approach and gather user feedback.
The implementation has some intentional limitations:</p>
<h3>Current Constraints</h3>
<ol>
<li>
<p><strong>Default info metric:</strong> Only considers <code>target_info</code> by default</p>
<ul>
<li>Workaround: You can use <code>__name__</code> matchers like <code>{__name__=~"(target|build)_info"}</code> in the data-label-selector, though this still assumes <code>job</code> and <code>instance</code> as identifying labels</li>
</ul>
</li>
<li>
<p><strong>Fixed identifying labels:</strong> Always assumes <code>job</code> and <code>instance</code> are the identifying labels for joining</p>
<ul>
<li>This unfortunately makes <code>info()</code> unsuitable for certain scenarios, e.g. including data labels from <code>kube_pod_labels</code>, but it's a problem we want to solve in the future</li>
</ul>
</li>
</ol>
<h3>Future Development</h3>
<p>These limitations are meant to be temporary.
The experimental status allows us to:</p>
<ul>
<li>Gather real-world usage feedback</li>
<li>Understand which use cases matter the most</li>
<li>Iterate on the design before committing to a final API</li>
</ul>
<p>A future version of the <code>info()</code> function should:</p>
<ul>
<li>Consider all info metrics by default (not just <code>target_info</code>)</li>
<li>Automatically understand identifying labels based on info metric metadata</li>
</ul>
<p><strong>Important:</strong> Because this is an experimental feature, the behavior may change in future Prometheus versions, or the function could potentially be removed from PromQL entirely based on user feedback.</p>
<h2>Giving Feedback</h2>
<p>Your feedback will directly shape the future of this feature and help us determine whether it should become a permanent part of PromQL.
Feedback may be provided e.g. through our <a href="https://prometheus.io/community/#community-connections">community connections</a> or by opening a <a href="https://github.com/prometheus/prometheus/issues">Prometheus issue</a>.</p>
<p>We encourage you to try the <code>info()</code> function and share your feedback:</p>
<ul>
<li>What use cases does it solve for you?</li>
<li>What additional functionality would you like to see?</li>
<li>How could the API be improved?</li>
<li>Do you see improved performance?</li>
</ul>
<h2>Conclusion</h2>
<p>The experimental <code>info()</code> function represents a significant step forward in making PromQL more accessible and reliable.
By simplifying metadata label enrichment and automatically handling the churn problem, it removes two major pain points for Prometheus users, especially those adopting OpenTelemetry.</p>
<p>To learn more:</p>
<ul>
<li><a href="https://prometheus.io/docs/prometheus/latest/querying/functions/#info">PromQL functions documentation</a></li>
<li><a href="https://prometheus.io/docs/guides/opentelemetry/">OpenTelemetry guide (includes detailed info() usage)</a></li>
<li><a href="https://github.com/prometheus/proposals/blob/main/proposals/0037-native-support-for-info-metrics-metadata.md">Feature proposal</a></li>
</ul>
<p>Please feel welcome to share your thoughts with the Prometheus community on <a href="https://github.com/prometheus/prometheus/discussions">GitHub Discussions</a> or get in touch with us on the <a href="https://cloud-native.slack.com/">CNCF Slack #prometheus channel</a>.</p>
<p>Happy querying!</p>
