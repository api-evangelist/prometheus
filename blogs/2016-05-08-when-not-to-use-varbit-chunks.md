---
title: "When (not) to use varbit chunks"
url: "https://prometheus.io/blog/2016/05/08/when-to-use-varbit-chunks/"
date: "2016-05-08T00:00:00.000Z"
author: "Björn “Beorn” Rabenstein"
feed_url: "https://prometheus.io/blog/feed.xml"
---
The embedded time series database (TSDB) of the Prometheus server organizes the raw sample data of each time series in chunks of constant 1024 bytes size. In addition to the raw sample data, a chunk contains some meta-data, which allows the selection of a different encoding for each chunk. The most fundamental distinction is the encoding version.
