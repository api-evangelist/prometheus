---
title: "YACE is joining Prometheus Community"
url: "https://prometheus.io/blog/2024/11/19/yace-joining-prometheus-community/"
date: "2024-11-19T00:00:00.000Z"
author: "Thomas Peitz (@thomaspeitz)"
feed_url: "https://prometheus.io/blog/feed.xml"
---
<p><a href="https://github.com/prometheus-community/yet-another-cloudwatch-exporter">Yet Another Cloudwatch Exporter</a> (YACE) has officially joined the Prometheus community! This move will make it more accessible to users and open new opportunities for contributors to enhance and maintain the project. There's also a blog post from <a href="https://grafana.com/blog/2024/11/19/yace-moves-to-prometheus-community/">Cristian Greco's point of view</a>.</p>
<h2>The early days</h2>
<p>When I first started YACE, I had no idea it would grow to this scale. At the time, I was working with <a href="https://www.ivx.com">Invision AG</a> (not to be confused with the design app), a company focused on workforce management software. They fully supported me in open-sourcing the tool, and with the help of my teammate <a href="https://github.com/kforsthoevel">Kai Forsthövel</a>, YACE was brought to life.</p>
<p>Our first commit was back in 2018, with one of our primary goals being to make CloudWatch metrics easy to scale and automatically detect what to measure, all while keeping the user experience simple and intuitive. InVision AG was scaling their infrastructure up and down due to machine learning workloads and we needed something that detects new infrastructure easily. This focus on simplicity has remained a core priority. From that point on, YACE began to find its audience.</p>
<h2>Yace Gains Momentum</h2>
<p>As YACE expanded, so did the support around it. One pivotal moment was when <a href="https://github.com/cristiangreco">Cristian Greco</a> from Grafana Labs reached out. I was feeling overwhelmed and hardly keeping up when Cristian stepped in, simply asking where he could help. He quickly became the main releaser and led Grafana Labs' contributions to YACE, a turning point that made a huge impact on the project. Along with an incredible community of contributors from all over the world, they elevated YACE beyond what I could have achieved alone, shaping it into a truly global tool. YACE is no longer just my project or Invision's—it belongs to the community.</p>
<h2>Gratitude and Future Vision</h2>
<p>I am immensely grateful to every developer, tester, and user who has contributed to YACE's success. This journey has shown me the power of community and open source collaboration. But we're not done yet.</p>
<p>It's time to take Yace even further—into the heart of the Prometheus ecosystem. Making Yace as the official Amazon CloudWatch exporter for Prometheus will make it easier and more accessible for everyone. With ongoing support from Grafana Labs and my commitment to refining the user experience, we'll ensure YACE becomes an intuitive tool that anyone can use effortlessly.</p>
<h2>Try out YACE on your own</h2>
<p>Try out <strong><a href="https://github.com/prometheus-community/yet-another-cloudwatch-exporter">YACE (Yet Another CloudWatch Exporter)</a></strong> by following our step-by-step <a href="https://github.com/prometheus-community/yet-another-cloudwatch-exporter/blob/master/docs/installation.md">Installation Guide</a>.</p>
<p>You can explore various configuration examples <a href="https://github.com/prometheus-community/yet-another-cloudwatch-exporter/tree/master/examples">here</a> to get started with monitoring specific AWS services.</p>
<p>Our goal is to enable easy auto-discovery across all AWS services, making it simple to monitor any dynamic infrastructure.</p>
