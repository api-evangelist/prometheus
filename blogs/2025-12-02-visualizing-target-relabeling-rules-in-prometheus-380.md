---
title: "Visualizing Target Relabeling Rules in Prometheus 3.8.0"
url: "https://prometheus.io/blog/2025/12/02/visualizing-target-relabeling-rules-in-the-prometheus-ui/"
date: "2025-12-02T00:00:00.000Z"
author: "Julius Volz (@juliusv)"
feed_url: "https://prometheus.io/blog/feed.xml"
---
<p>Prometheus' <a href="https://prometheus.io/docs/prometheus/latest/configuration/configuration/#relabel_config">target relabeling</a> feature allows you to adjust the labels of a discovered target or even drop the target entirely. Relabeling rules, while powerful, can be hard to understand and debug. Your rules have to match the expected labels that your service discovery mechanism returns, and getting any step wrong could label your target incorrectly or accidentally drop it.</p>
<p>To help you figure out where things go wrong (or right), Prometheus 3.8.0 <a href="https://github.com/prometheus/prometheus/pull/17337">just added a relabeling visualizer</a> to the Prometheus server's web UI that allows you to inspect how each relabeling rule is applied to a discovered target's labels. Let's take a look at how it works!</p>
<h2>Using the relabeling visualizer</h2>
<p>If you head to any Prometheus server's "Service discovery" page (for example: <a href="https://demo.promlabs.com/service-discovery">https://demo.promlabs.com/service-discovery</a>), you will now see a new "show relabeling" button for each discovered target:</p>
<p><img alt="Service discovery page screenshot" src="/assets/blog/2025-12-02/prometheus-sd-page-show-relabeling.png" /></p>
<p>Clicking this button shows you how each relabeling rule is applied to that particular target in sequence:</p>
<p>The visualizer shows you:</p>
<ul>
<li>The <strong>initial labels</strong> of the target as discovered by the service discovery mechanism.</li>
<li>The details of <strong>each relabeling rule</strong>, including its action type and other parameters.</li>
<li><strong>How the labels change</strong> after each relabeling rule is applied, with changes, additions, and deletions highlighted in color.</li>
<li>Whether the target is ultimately <strong>kept or dropped</strong> after all relabeling rules have been applied.</li>
<li>The <strong>final output labels</strong> of the target if it is kept.</li>
</ul>
<p>To debug your relabeling rules, you can now read this diagram from top to bottom and find the exact step where the labels change in an unexpected way or where the target gets dropped. This should help you identify misconfigurations in your relabeling rules more easily.</p>
<h2>Conclusion</h2>
<p>The new relabeling visualizer in the Prometheus server's web UI is a powerful tool to help you understand and debug your target relabeling configurations. By providing a step-by-step view of how each relabeling rule affects a target's labels, it makes it easier to identify and fix issues in your setup. Update your Prometheus servers to 3.8.0 now to give it a try!</p>
