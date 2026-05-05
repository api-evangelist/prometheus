---
title: "Announcing Prometheus 3.0"
url: "https://prometheus.io/blog/2024/11/14/prometheus-3-0/"
date: "2024-11-14T00:00:00.000Z"
author: "The Prometheus Team"
feed_url: "https://prometheus.io/blog/feed.xml"
---
<p>Following the recent release of <a href="https://prometheus.io/blog/2024/09/11/prometheus-3-beta/">Prometheus 3.0 beta</a> at PromCon in Berlin, the Prometheus Team
is excited to announce the immediate availability of Prometheus Version 3.0!</p>
<p>This latest version marks a significant milestone as it is the first major release in 7 years. Prometheus has come a long way in that time,
evolving from a project for early adopters to becoming a standard part of the cloud native monitoring stack. Prometheus 3.0 aims to
continue that journey by adding some exciting new features while largely maintaining stability and compatibility with previous versions.</p>
<p>The full 3.0 release adds some new features on top of the beta and also introduces a few additional breaking changes that we will describe in this article.</p>
<h2>What's New</h2>
<p>Here is a summary of the exciting changes that have been released as part of the beta version, as well as what has been added since:</p>
<h3>New UI</h3>
<p>One of the highlights in Prometheus 3.0 is its brand-new UI that is enabled by default:</p>
<p><img alt="New UI query page" src="/assets/blog/2024-11-14/blog_post_screenshot_tree_view-s.png" /></p>
<p>The UI has been completely rewritten with less clutter, a more modern look and feel, new features like a <a href="https://promlens.com/"><strong>PromLens</strong></a>-style tree view,
and will make future maintenance easier by using a more modern technical stack.</p>
<p>Learn more about the new UI in general in <a href="https://promlabs.com/blog/2024/09/11/a-look-at-the-new-prometheus-3-0-ui/">Julius' detailed article on the PromLabs blog</a>.
Users can temporarily enable the old UI by using the <code>old-ui</code> feature flag.</p>
<p>Since the new UI is not battle-tested yet, it is also very possible that there are still bugs. If you find any, please
<a href="https://github.com/prometheus/prometheus/issues/new?assignees=&#x26;labels=&#x26;projects=&#x26;template=bug_report.yml">report them on GitHub</a>.</p>
<p>Since the beta, the user interface has been updated to support UTF-8 metric and label names.</p>
<p><img alt="New UTF-8 UI" src="/assets/blog/2024-11-14/utf8_ui.png" /></p>
<h3>Remote Write 2.0</h3>
<p>Remote-Write 2.0 iterates on the previous protocol version by adding native support for a host of new elements including metadata, exemplars,
created timestamp and native histograms. It also uses string interning to reduce payload size and CPU usage when compressing and decompressing.
There is better handling for partial writes to provide more details to clients when this occurs. More details can be found
<a href="https://prometheus.io/docs/specs/remote_write_spec_2_0/">here</a>.</p>
<h3>UTF-8 Support</h3>
<p>Prometheus now allows all valid UTF-8 characters to be used in metric and label names by default, as well as label values,
as has been true in version 2.x.</p>
<p>Users will need to make sure their metrics producers are configured to pass UTF-8 names, and if either side does not support UTF-8,
metric names will be escaped using the traditional underscore-replacement method. PromQL queries can be written with the new quoting syntax
in order to retrieve UTF-8 metrics, or users can specify the <code>__name__</code>  label name manually.</p>
<p>Currently only the Go client library has been updated to support UTF-8, but support for other languages will be added soon.</p>
<h3>OTLP Support</h3>
<p>In alignment with <a href="https://prometheus.io/blog/2024/03/14/commitment-to-opentelemetry/">our commitment to OpenTelemetry</a>, Prometheus 3.0 features
several new features to improve interoperability with OpenTelemetry.</p>
<h4>OTLP Ingestion</h4>
<p>Prometheus can be configured as a native receiver for the OTLP Metrics protocol, receiving OTLP metrics on the <code>/api/v1/otlp/v1/metrics</code> endpoint.</p>
<p>See our <a href="https://prometheus.io/docs/guides/opentelemetry">guide</a> on best practices for consuming OTLP metric traffic into Prometheus.</p>
<h4>UTF-8 Normalization</h4>
<p>With Prometheus 3.0, thanks to <a href="#utf-8-support">UTF-8 support</a>, users can store and query OpenTelemetry metrics without annoying changes to metric and label names like <a href="https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/pkg/translator/prometheus">changing dots to underscores</a>.</p>
<p>Notably this allows <strong>less confusion</strong> for users and tooling in terms of the discrepancy between what’s defined in OpenTelemetry semantic convention or SDK and what’s actually queryable.</p>
<p>To achieve this for OTLP ingestion, Prometheus 3.0 has experimental support for different translation strategies. See <a href="https://prometheus.io/docs/prometheus/latest/configuration/configuration/#:~:text=Settings%20related%20to%20the%20OTLP%20receiver%20feature">otlp section in the Prometheus configuration</a> for details.</p>
<blockquote>
<p>NOTE: While “NoUTF8EscapingWithSuffixes” strategy allows special characters, it still adds required suffixes for the best experience. See <a href="https://github.com/prometheus/proposals/pull/39">the proposal on the future work to enable no suffixes</a> in Prometheus.</p>
</blockquote>
<h3>Native Histograms</h3>
<p>Native histograms are a Prometheus metric type that offer a higher efficiency and lower cost alternative to Classic Histograms. Rather than having to choose (and potentially have to update) bucket boundaries based on the data set, native histograms have pre-set bucket boundaries based on exponential growth.</p>
<p>Native Histograms are still experimental and not yet enabled by default, and can be turned on by passing <code>--enable-feature=native-histograms</code>. Some aspects of Native Histograms, like the text format and accessor functions / operators are still under active design.</p>
<h3>Breaking Changes</h3>
<p>The Prometheus community strives to <a href="https://prometheus.io/docs/prometheus/latest/stability/">not break existing features within a major release</a>. With a new major release we took the opportunity to clean up a few, but small, long-standing issues. In other words, Prometheus 3.0 contains a few breaking changes. This includes changes to feature flags, configuration files, PromQL, and scrape protocols.</p>
<p>Please read the <a href="https://prometheus.io/docs/prometheus/3.0/migration/">migration guide</a> to find out if your setup is affected and what actions to take.</p>
<h2>Performance</h2>
<p>It’s impressive to see what we have accomplished in the community since Prometheus 2.0. We all love numbers, so let’s celebrate the efficiency improvements we made for both CPU and memory use for the TSDB mode. Below you can see performance numbers between 3 Prometheus versions on the node with 8 CPU and 49 GB allocatable memory.</p>
<ul>
<li>2.0.0 (7 years ago)</li>
<li>2.18.0 (4 years ago)</li>
<li>3.0.0 (now)</li>
</ul>
<p><img alt="Memory bytes" src="/assets/blog/2024-11-14/memory_bytes_ui.png" /></p>
<p><img alt="CPU seconds" src="/assets/blog/2024-11-14/cpu_seconds_ui.png" /></p>
<p>It’s furthermore impressive that those numbers were taken using our <a href="https://github.com/prometheus/prometheus/pull/15366">prombench macrobenchmark</a>
that uses the same PromQL queries, configuration and environment–highlighting backward compatibility and stability for the core features, even with 3.0.</p>
<h2>What's Next</h2>
<p>There are still tons of exciting features and improvements we can make in Prometheus and the ecosystem. Here is a non-exhaustive list to get you excited and…
hopefully motivate you to contribute and join us!</p>
<ul>
<li>New, more inclusive <strong>governance</strong></li>
<li>More <strong>OpenTelemetry</strong> compatibility and features</li>
<li>OpenMetrics 2.0, now under Prometheus governance!</li>
<li>Native Histograms stability (and with custom buckets!)</li>
<li>More optimizations!</li>
<li>UTF-8 support coverage in more SDKs and tools</li>
</ul>
<h2>Try It Out!</h2>
<p>You can try out Prometheus 3.0 by downloading it from our <a href="https://prometheus.io/download/#prometheus">official binaries</a> and <a href="https://quay.io/repository/prometheus/prometheus?tab=tags">container images</a>.</p>
<p>If you are upgrading from Prometheus 2.x, check out the migration guide for more information on any adjustments you will have to make.
Please note that we strongly recommend upgrading to v2.55 before upgrading to v3.0. Rollback is possible from v3.0 to v2.55, but not to earlier versions.</p>
<p>As always, we welcome feedback and contributions from the community!</p>
