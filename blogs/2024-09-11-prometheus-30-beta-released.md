---
title: "Prometheus 3.0 Beta Released"
url: "https://prometheus.io/blog/2024/09/11/prometheus-3-beta/"
date: "2024-09-11T00:00:00.000Z"
author: "The Prometheus Team"
feed_url: "https://prometheus.io/blog/feed.xml"
---
<p>The Prometheus Team is proud to announce the availability of Prometheus Version 3.0-beta!
You can download it <a href="https://github.com/prometheus/prometheus/releases/tag/v3.0.0-beta.0">here</a>.
As is traditional with a beta release, we do <strong>not</strong> recommend users install Prometheus 3.0-beta on critical production systems, but we do want everyone to test it out and find bugs.</p>
<p>In general, the only breaking changes are the removal of deprecated feature flags. The Prometheus team worked hard to ensure backwards-compatibility and not to break existing installations, so all of the new features described below build on top of existing functionality. Most users should be able to try Prometheus 3.0 out of the box without any configuration changes.</p>
<h2>What's New</h2>
<p>With over 7500 commits in the 7 years since Prometheus 2.0 came out there are too many new individual features and fixes to list, but there are some big shiny and breaking changes we wanted to call out. We need everyone in the community to try them out and report any issues you might find.
The more feedback we get, the more stable the final 3.0 release can be.</p>
<h3>New UI</h3>
<p>One of the highlights in Prometheus 3.0 is its brand new UI that is enabled by default:</p>
<p><img alt="New UI query page" src="/assets/blog/2024-09-11/blog_post_screenshot_tree_view-s.png" /></p>
<p>The UI has been completely rewritten with less clutter, a more modern look and feel, new features like a <a href="https://promlens.com/"><strong>PromLens</strong></a>-style tree view, and will make future maintenance easier by using a more modern technical stack.</p>
<p>Learn more about the new UI in general in <a href="https://promlabs.com/blog/2024/09/11/a-look-at-the-new-prometheus-3-0-ui/">Julius' detailed article on the PromLabs blog</a>.
Users can temporarily enable the old UI by using the <code>old-ui</code> feature flag.
Since the new UI is not battle-tested yet, it is also very possible that there are still bugs. If you find any, please <a href="https://github.com/prometheus/prometheus/issues/new?assignees=&#x26;labels=&#x26;projects=&#x26;template=bug_report.yml">report them on GitHub</a>.</p>
<h3>Remote Write 2.0</h3>
<p>Remote-Write 2.0 iterates on the previous protocol version by adding native support for a host of new elements including metadata, exemplars, created timestamp and native histograms. It also uses string interning to reduce payload size and CPU usage when compressing and decompressing. More details can be found <a href="https://prometheus.io/docs/specs/remote_write_spec_2_0/">here</a>.</p>
<h3>OpenTelemetry Support</h3>
<p>Prometheus intends to be the default choice for storing OpenTelemetry metrics, and 3.0 includes some big new features that makes it even better as a storage backend for OpenTelemetry metrics data.</p>
<h4>UTF-8</h4>
<p>By default, Prometheus will allow all valid UTF-8 characters to be used in metric and label names, as well as label values as has been true in version 2.x.</p>
<p>Users will need to make sure their metrics producers are configured to pass UTF-8 names, and if either side does not support UTF-8, metric names will be escaped using the traditional underscore-replacement method. PromQL queries can be written with the new quoting syntax in order to retrieve UTF-8 metrics, or users can specify the <code>__name__</code>  label name manually.</p>
<p>Not all language bindings have been updated with support for UTF-8 but the primary Go libraries have been.</p>
<h4>OTLP Ingestion</h4>
<p>Prometheus can be configured as a native receiver for the OTLP Metrics protocol, receiving OTLP metrics on the /api/v1/otlp/v1/metrics endpoint.</p>
<h3>Native Histograms</h3>
<p>Native histograms are a Prometheus metric type that offer a higher efficiency and lower cost alternative to Classic Histograms. Rather than having to choose (and potentially have to update) bucket boundaries based on the data set, native histograms have pre-set bucket boundaries based on exponential growth.</p>
<p>Native Histograms are still experimental and not yet enabled by default, and can be turned on by passing <code>--enable-feature=native-histograms</code>.  Some aspects of Native Histograms, like the text format and accessor functions / operators are still under active design.</p>
<h3>Other Breaking Changes</h3>
<p>The following feature flags have been removed, being enabled by default instead. References to these flags should be removed from configs, and will be ignored in Prometheus starting with version 3.0</p>
<ul>
<li><code>promql-at-modifier</code></li>
<li><code>promql-negative-offset</code></li>
<li><code>remote-write-receiver</code></li>
<li><code>no-scrape-default-port</code></li>
<li><code>new-service-discovery-manager</code></li>
</ul>
<p>Range selections are now <a href="https://github.com/prometheus/prometheus/issues/13213">left-open and right-closed</a>, which will avoid rare occasions that more points than intended are included in operations.</p>
<p>Agent mode is now stable and has its own config flag instead of a feature flag</p>
