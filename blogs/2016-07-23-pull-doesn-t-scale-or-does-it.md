---
title: "Pull doesn't scale - or does it?"
url: "https://prometheus.io/blog/2016/07/23/pull-does-not-scale-or-does-it/"
date: "2016-07-23T00:00:00.000Z"
author: "Julius Volz"
feed_url: "https://prometheus.io/blog/feed.xml"
---
Let's talk about a particularly persistent myth. Whenever there is a discussion about monitoring systems and Prometheus's pull-based metrics collection approach comes up, someone inevitably chimes in about how a pull-based approach just “fundamentally doesn't scale”. The given reasons are often vague or only apply to systems that are fundamentally different from Prometheus.
