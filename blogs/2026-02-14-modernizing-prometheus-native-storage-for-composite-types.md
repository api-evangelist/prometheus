---
title: "Modernizing Prometheus: Native Storage for Composite Types"
url: "https://prometheus.io/blog/2026/02/14/modernizing-prometheus-composite-samples/"
date: "2026-02-14T00:00:00.000Z"
author: "Bartłomiej Płotka (@bwplotka)"
feed_url: "https://prometheus.io/blog/feed.xml"
---
<p>Over the last year, the Prometheus community has been working hard on several interesting and ambitious changes that previously would have been seen as controversial or not feasible. While there might be little visibility about those from the outside (e.g., it's not an OpenClaw Prometheus plugin, sorry 🙃), Prometheus developers are, <strong>organically</strong>, steering Prometheus into a certain, coherent future. Piece by piece, we unexpectedly get closer to goals we never dreamed we would achieve as an open-source project!</p>
<p>This post starts (hopefully!) as a series of blog posts that share a few ambitious shifts that might be exciting to new and existing Prometheus users and developers. In this post, I'd love to focus on the idea of <strong>native storage for the composite types</strong> which is tidying up a lot of challenges that piled up over time. Make sure to check the provided inlined links on how you can adopt some of those changes early or contribute!</p>
<blockquote>
<p>CAUTION: Disclaimer: This post is intended as a fun overview, from my own personal point of view as a Prometheus maintainer. Some of the mentioned changes haven't been (yet) officially approved by the Prometheus Team; some of them were not proved in production.</p>
</blockquote>
<blockquote>
<p>NOTE: This post was written by humans; AI was used only for cosmetic and grammar fixes.</p>
</blockquote>
<h2>Classic Representation: Primitive Samples</h2>
<p>As you might know, the Prometheus data model (so server, PromQL, protocols) supports <a href="https://prometheus.io/docs/concepts/metric_types/#gauge"><code>gauges</code></a>, <a href="https://prometheus.io/docs/concepts/metric_types/#counter"><code>counters</code></a>, <a href="https://prometheus.io/docs/concepts/metric_types/#histogram"><code>histograms</code></a> and <a href="https://prometheus.io/docs/concepts/metric_types/#summary"><code>summaries</code></a>. <a href="https://prometheus.io/docs/specs/om/open_metrics_spec/">OpenMetrics 1.0</a> extended this with <a href="https://prometheus.io/docs/specs/om/open_metrics_spec/#gaugehistogram-1"><code>gaugehistogram</code></a>, <a href="https://prometheus.io/docs/specs/om/open_metrics_spec/#info-1"><code>info</code></a> and <a href="https://prometheus.io/docs/specs/om/open_metrics_spec/#stateset-1"><code>stateset</code></a> types.</p>
<p>Impressively, for a long time Prometheus' TSDB storage implementation had an explicitly clean and simple data model. The TSDB allowed the storage and retrieval of string-labelled <strong>primitive</strong> samples containing only <code>float64</code> values and <code>int64</code> timestamps. It was completely metric-type-agnostic.</p>
<p>The metric types were implied on top of the TSDB, for humans and best effort tooling for PromQL. For simplicity, let's call this way of storing types a <strong>classic</strong> model or representation. In this model:</p>
<p>We have <strong>primitive</strong> types:</p>
<ul>
<li>
<p><code>gauge</code> is a "default" type with no special rules, just a float sample with labels.</p>
</li>
<li>
<p><code>counter</code> that should have a <code>_total</code> suffix in the name for humans to understand its semantics.</p>
<pre><code>foo_total 17.0
</code></pre>
</li>
<li>
<p><code>info</code> that needs an <code>_info</code> suffix in the metric name and always has a value of <code>1</code>.</p>
</li>
</ul>
<p>We have <strong>composite</strong> types. This is where the fun begins. In the classic representation, composite metrics are represented as a set of primitive float samples:</p>
<ul>
<li>
<p><code>histogram</code> is a group of <code>counters</code> with certain mandatory suffixes and <code>le</code> labels:</p>
<pre><code>foo_bucket{le="0.0"} 0
foo_bucket{le="1e-05"} 0
foo_bucket{le="0.0001"} 5
foo_bucket{le="0.1"} 8
foo_bucket{le="1.0"} 10
foo_bucket{le="10.0"} 11
foo_bucket{le="100000.0"} 11
foo_bucket{le="1e+06"} 15
foo_bucket{le="1e+23"} 16
foo_bucket{le="1.1e+23"} 17
foo_bucket{le="+Inf"} 17
foo_count 17
foo_sum 324789.3
</code></pre>
</li>
<li>
<p><code>gaugehistogram</code>, <code>summary</code>, and <code>stateset</code> types follow the same logic – a group of special <code>gauges</code> or <code>counters</code> that compose a single metric.</p>
</li>
</ul>
<p>The classic model served the Prometheus project well. It significantly simplified the storage implementation, enabling Prometheus to be one of the most optimized, open-source time-series databases, with distributed versions based on the same data model available in projects like Cortex, Thanos, and Mimir, etc.</p>
<p>Unfortunately, there are always tradeoffs. This classic model has a few limitations:</p>
<ul>
<li><strong>Efficiency:</strong> It tends to yield overhead for composite types because every new piece of data (e.g., new bucket) takes precious index space (it's a new unique series), whereas samples are significantly more compressible (rarely change, time-oriented).</li>
<li><strong>Functionality:</strong> It poses limitations to the shape and flexibility of the data you store (unless we'd go into some JSON-encoded labels, which have massive downsides).</li>
<li><strong>Transactionality:</strong> Primitive pieces of composite types (separate counters) are processed independently. While we did a lot of work to ensure write isolation and transactionality for scrapes, transactionality completely breaks apart when data is received or sent via remote write, OTLP protocols. For example, a classic histogram <code>foo</code> might have been partially sent, its <code>foo_bucket{le="1.1e+23"} 17</code> counter series be delayed or dropped accidentally, which risks triggering false positive alerts.</li>
<li><strong>Reliability:</strong> Consumers of the TSDB data have to essentially guess the type semantics. There's nothing stopping users from writing a <code>foo_bucket</code> gauge or <code>foo_total</code> histogram.</li>
</ul>
<h3>A Glimpse of Native Storage for Composite Types</h3>
<p>The classic model was challenged by the introduction of <a href="https://prometheus.io/docs/specs/native_histograms/">native histograms</a>. The TSDB was extended to store <a href="https://github.com/prometheus/prometheus/blob/19fd0b0b1dbfe01a5e49f5d04544a7c5853c12bb/model/histogram/histogram.go#L50">composite histogram samples</a> other than float. We tend to call this a <strong>native</strong> histogram, because TSDB can now "natively" store a full (with sparse and exponential buckets) histogram as an atomic, composite sample.</p>
<p>At that point, the common wisdom was to stop there. The special advanced histogram that's generally meant to replace the "classic" histograms uses a composite sample, while the rest of the metrics use the classic model. Making other composite types consistent with the new native model felt extremely disruptive to users, with too much work and risks. A common counter-argument was that users will eventually migrate their classic histograms naturally, and summaries are also less useful, given the more powerful bucketing and lower cost of native histograms.</p>
<p>Unfortunately, the migration to native histograms was known to take time, given the slight PromQL change required to use them, and the new bucketing and client changes needed (applications have to define new or edit existing metrics to new histograms). There will also be old software used for a long time that never migrates. Eventually, it leaves Prometheus with no chance of deprecating classic histograms, with all the software solutions required to support the classic model, likely for decades.</p>
<p>However, native histograms did push TSDB and the ecosystem into that new composite sample pattern. Some of those changes could be easily adapted to all composite types. Native histograms also gave us a glimpse of the many benefits of that native support. It was tempting to ask ourselves: <strong>would it be possible to add native counterparts of the existing composite metrics to replace them, ideally transparently?</strong></p>
<p>Organically, in 2024, for transactionality and efficiency, we introduced a <a href="https://github.com/prometheus/proposals/blob/main/proposals/0031-classic-histograms-stored-as-native-histograms.md">native histogram custom buckets(NHCB)</a> concept that essentially allows storing classic histograms with explicit buckets natively, reusing native histogram composite sample data structures.</p>
<p>NHCB has proven to be at least 30% more efficient than the classic representation, while offering functional parity with
classic histograms. However, two practical challenges emerged that slowed down the adoption:</p>
<ol>
<li>
<p><strong>Expanding</strong>, that is converting from NHCB to classic histogram, is relatively trivial, but <strong>combining</strong>, which is turning a classic histogram into NHCB, is often not feasible. We don't want to wait for client ecosystem adoption, and also being mindful of legacy, hard to change software, we envisioned NHCB being converted (so combined) on scrape from the classic representation. That has proven to be somewhat expensive on scrape. Additionally, combination logic is practically impossible when receiving "pushes" (e.g., remote write with classic histograms), as you could end up having different parts of the same histogram sample (e.g., buckets and count) sent via different remote write shards or sequential messages. This combination challenge is also why OpenTelemetry collector users see an extra overhead on <code>prometheusreceiver</code> as the OpenTelemetry model strictly follows the composite sample model.</p>
</li>
<li>
<p><strong>Consumption</strong> is slightly different, especially in the PromQL query syntax. Our initial decision was to surface NHCB histograms using a native-histogram-like PromQL syntax. For example the following classic histogram:</p>
<pre><code>foo_bucket{le="0.0"} 0
# ...
foo_bucket{le="1.1e+23"} 17
foo_bucket{le="+Inf"} 17
foo_count 17
foo_sum 324789.3
</code></pre>
<p>When we convert this to NHCB, you can no longer use <code>foo_bucket</code> as your metric name selector. Since NHCB is now stored as a <code>foo</code> metric, you need to use:</p>
<pre><code>histogram_quantile(0.9, sum(foo{job="a"}))

# Old syntax: histogram_quantile(0.9, sum(foo_bucket{job="a"}) by (le))
</code></pre>
<p>This has also another effect. It violates our <a href="https://youtu.be/KTdhXHY-Hqo?t=426">"what you see is what you query"</a> rule for the text formats, at least until <a href="https://github.com/prometheus/OpenMetrics/issues/276">OpenMetrics 2</a>.</p>
<p>On top of that, similar problems occur on other Prometheus outputs (federation, remote read, and remote write).</p>
</li>
</ol>
<blockquote>
<p>NOTE: Fun fact: Prometheus <a href="https://github.com/prometheus/client_model">client data model (SDKs)</a> and <code>PrometheusProto</code>
scrape protocol use the composite sample model already!</p>
</blockquote>
<h3>Transparent Native Representation</h3>
<p>Let's get straight to the point. Organically, the Prometheus community seems to align with the following two ideas:</p>
<ul>
<li>We want to <strong>eventually move to a fully composite sample model on the storage layer</strong>, given all the benefits.</li>
<li>Users needs to be able to <strong>switch (e.g., <a href="https://prometheus.io/docs/prometheus/latest/configuration/configuration/#:~:text=custom%20buckets.%0A%20%20%5B-,convert_classic_histograms_to_nhcb,-%3A%20%3Cbool%3E%20%7C%20default">on scrape</a>) from classic to native form in storage without breaking consumption layer</strong>. Essentially to help with non-trivial migration pains (finding who use what, double-writing, synchronizing), avoiding <a href="https://github.com/prometheus/OpenMetrics/issues/312#issuecomment-3818547465">tricky, dual mode, protocol changes</a> and to deprecate the classic model ASAP for the sustainability of the Prometheus codebase, we need to ensure <strong>eventual consumption migration</strong> e.g., PromQL queries -- independently to the storage layer.</li>
</ul>
<p>Let's go through evidence of this direction, which also represents efforts you can contribute to or adopt early!</p>
<ol>
<li>
<p>We are discussing the "native" <a href="https://github.com/prometheus/prometheus/issues/16949">summary</a> and <a href="https://github.com/prometheus/prometheus/issues/17914">stateset</a> to fully eliminate classic model for all composite types. Feel free to join and help on that work!</p>
</li>
<li>
<p>We are working on <a href="https://docs.google.com/document/d/1FCD-38Xz1-9b3ExgHOeDTQUKUatzgj5KbCND9t-abZY/edit?tab=t.lvx6fags1fga#heading=h.uaaplxxbz60u">the OpenMetrics 2.0</a> to consolidate and improve the pull protocol scene and apply the new learnings. One of the core changes will be the move to <a href="https://github.com/prometheus/docs/pull/2679">composite values in text</a>, which makes the text format trivial to parse for storages that support composite types natively. This solves the <strong>combining</strong> challenge. Note that, by default, for now, all composite types will be still "expanded" to classic format on scrape, so there's no breaking change for users. Feel free to join our WG to help or give feedback.</p>
</li>
<li>
<p>Prometheus receive and export protocol has been updated. <a href="https://prometheus.io/docs/specs/prw/remote_write_spec_2_0/">Remote Write 2.0</a> allows transporting histograms in the "native" form instead of a classic representation (classic one is still supported). In the future versions (e.g. 2.1), we could easily follow a similar pattern and add native summaries and stateset. Contributions are welcome to <a href="https://github.com/prometheus/prometheus/issues/16944">make Remote Write 2.0 stable</a>!</p>
</li>
<li>
<p>We are experimenting with the <strong>consumption</strong> compatibility modes that translate the composite types store as composite samples to classic representation. This is not trivial; there are edge cases, but it might be more feasible (and needed!) than we might have initially anticipated. See:</p>
<ul>
<li><a href="https://github.com/prometheus/prometheus/issues/16948">PromQL compatibility mode for NHCB</a></li>
<li><a href="https://github.com/prometheus/prometheus/issues/17147">Expanding on remote write</a></li>
<li>We need to also consider adding expanding for federation, remote read and other APIs.</li>
</ul>
<p>In PromQL it might work as follows, for an NHCB that used to be a classic histogram:</p>
<pre><code># New syntax gives our "foo" NHCB:    
histogram_quantile(0.9, sum(foo{job="a"}))
# Old syntax still works, expanding "foo" NHCB to classic representation:
histogram_quantile(0.9, sum(foo_bucket{job="a"}) by (le))
</code></pre>
<p>Alternatives, like <a href="https://github.com/prometheus/OpenMetrics/issues/312#issuecomment-3884502409">a special label or annotations</a>, are also discussed.</p>
</li>
</ol>
<p>When implemented, it should be possible to fully switch different parts of your metric collection pipeline to native form <strong>transparently</strong>.</p>
<h2>Summary</h2>
<p>Moving Prometheus to a native composite type world is not easy and will take time, especially around coding, testing and optimizing. Notably it switches performance characteristics of the metric load from uniform, predictable sample sizes to a sample size that depends on a type. Another challenge is code architecture - maintaining different sample types has already proven to be very verbose (we <a href="https://github.com/prometheus/prometheus/issues/17925">need unions, Go!</a>).</p>
<p>However, recent work revealed a very clean and <strong>possible</strong> path that yields clear benefits around functionality, transactionality, reliability, and efficiency in the relatively near future, which is pretty exciting!</p>
<p>If you have any questions around these changes, feel free to:</p>
<ul>
<li>DM me on Slack.</li>
<li>Visit the <code>#prometheus-dev</code> Slack channel and share your questions.</li>
<li>Comment on related issues, create PRs, also <strong>review</strong> PRs (the most impactful work!)</li>
</ul>
<p>The Prometheus community is also at KubeConEU 2026 in Amsterdam! Make sure to:</p>
<ul>
<li>Visit our Prometheus KubeCon booth.</li>
<li>Attend our <a href="https://sched.co/2EF7p">contributing workshop</a> on Wednesday, March 25, 2026 16:00.</li>
<li>Attend our <a href="https://sched.co/2EF4F">Prometheus V3 One Year In: OpenMetrics 2.0 and More!</a> session on Thursday, March 26, 13:45.</li>
</ul>
<p>I'm hoping we can share stories of other important, orthogonal shifts we see in the community in future posts. No promises (and help welcome!), but there's a lot to cover, such as (random order, not a full list):</p>
<ol>
<li>Our native <a href="https://github.com/prometheus/proposals/pull/60">start timestamp feature journey</a> that cleanly unblocks native <a href="https://github.com/prometheus/proposals/pull/48">delta temporality</a> without "hacks" like reusing gauges, separate layer of metric types or label annotations e,g., <code>__temporality__</code>.</li>
<li>Optional <a href="https://sched.co/2CVzR">schematization of Prometheus metrics</a> that attempt to solve a ton of stability problems with metric naming and shape; building on top of OpenTelemetry semconv.</li>
<li>Our <a href="https://github.com/prometheus/prometheus/issues/12608">metadata storage journey</a> that attempts to improve the OpenTelemetry Entities and resource attributes storage and consumption experience.</li>
<li>Our journey to organize and extend Prometheus scrape pull protocols with the recent ownership move of OpenMetrics.</li>
<li>An incredible <a href="https://promcon.io/2025-munich/talks/beyond-tsdb-unlocking-prometheus-with-parquet-for-modern-scale/">TSDB Parquet</a> effort, coming from the three LTS project groups (Cortex, Thanos, Mimir) working together, attempting to improve high-cardinality cases.</li>
<li>Fun experiments with PromQL extensions, like <a href="https://github.com/prometheus/prometheus/pull/17487">PromQL with pipes and variables</a> and some new SQL transpilation ideas.</li>
<li>Governance changes.</li>
</ol>
<p>See you in open-source!</p>
