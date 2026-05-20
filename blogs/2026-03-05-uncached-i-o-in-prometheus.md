---
title: "Uncached I/O in Prometheus"
url: "https://prometheus.io/blog/2026/03/05/uncached-io/"
date: "2026-03-05T00:00:00.000Z"
author: "Ayoub Mrini (@machine424)"
feed_url: "https://prometheus.io/blog/feed.xml"
---
Do you find yourself constantly looking up the difference between container_memory_usage_bytes , container_memory_working_set_bytes , and container_memory_rss ? Pick the wrong one and your memory limits lie to you, your benchmarks mislead you, and your container gets OOMKilled. You're not alone.
