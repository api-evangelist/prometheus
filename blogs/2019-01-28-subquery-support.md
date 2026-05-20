---
title: "Subquery Support"
url: "https://prometheus.io/blog/2019/01/28/subquery-support/"
date: "2019-01-28T00:00:00.000Z"
author: "Ganesh Vernekar"
feed_url: "https://prometheus.io/blog/feed.xml"
---
Introduction As the title suggests, a subquery is a part of a query, and allows you to do a range query within a query, which was not possible before. It has been a long-standing feature request: prometheus/prometheus/1227 . The pull request for subquery support was recently merged into Prometheus and will be available in Prometheus 2.7.
