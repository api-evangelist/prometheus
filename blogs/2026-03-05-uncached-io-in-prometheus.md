---
title: "Uncached I/O in Prometheus"
url: "https://prometheus.io/blog/2026/03/05/uncached-io/"
date: "2026-03-05T00:00:00.000Z"
author: "Ayoub Mrini (@machine424)"
feed_url: "https://prometheus.io/blog/feed.xml"
---
<p>Do you find yourself constantly looking up the difference between <code>container_memory_usage_bytes</code>, <code>container_memory_working_set_bytes</code>, and <code>container_memory_rss</code>? Pick the wrong one and your memory limits lie to you, your benchmarks mislead you, and your container gets OOMKilled.</p>
<p>You're not alone. There is even a <a href="https://github.com/kubernetes/kubernetes/issues/43916">9-year-old Kubernetes issue</a> that captures the frustration of users.</p>
<p>The explanation is simple: RAM is not used in just one way. One of the easiest things to miss is the <a href="https://en.wikipedia.org/wiki/Page_cache">page cache</a> semantics. For some containers, memory taken by page caching can make up most of the reported usage, even though that memory is largely reclaimable, creating surprising differences between those metrics.</p>
<blockquote>
<p>NOTE: The feature discussed here currently only supports Linux.</p>
</blockquote>
<p>Prometheus writes a lot of data to disk. It is, after all, a database. But not every write benefits from sitting in the page cache. Compaction writes are the clearest example: once a block is written, only a fraction of that data is likely to be queried again soon, and since there is no way to predict which fraction, caching it all offers little return. The <a href="https://prometheus.io/docs/prometheus/latest/feature_flags/#use-uncached-io">use-uncached-io</a> feature flag was built to address exactly this.</p>
<p>Bypassing the cache for those writes reduces Prometheus's page cache footprint, making its memory usage more predictable and easier to reason about. It also relieves pressure on that shared cache, lowering the risk of evicting hot data that queries and other reads actually depend on. A potential bonus is reduced CPU overhead from cache allocations and evictions. The hard constraint throughout was to avoid any measurable regression in CPU or disk I/O.</p>
<p>The flag was introduced in Prometheus <code>v3.5.0</code> and currently only supports Linux. Under the hood, it uses direct I/O, which requires proper filesystem support and a kernel <code>v2.4.10</code> or newer, though you should be fine, as that version shipped nearly 25 years ago.</p>
<p>If direct I/O helps here, why was it not done earlier, and why is it not used everywhere it would help? Because direct I/O comes with strict alignment requirements. Unlike buffered I/O, you cannot simply write any chunk of memory to any position in a file. The file offset, the memory buffer address, and the transfer size must all be aligned to the logical sector size of the underlying storage device, typically 512 or 4096 bytes.</p>
<p>To satisfy those constraints, a <a href="https://pkg.go.dev/bufio#Writer"><code>bufio.Writer</code></a>-like writer, <a href="https://github.com/prometheus/prometheus/blob/ac12e30f99df9d2f68025f0238c0aef95146e94b/tsdb/fileutil/direct_io_writer.go#L46"><code>directIOWriter</code></a>, was implemented. On Linux kernels <code>v6.1</code> or newer, Prometheus retrieves the exact alignment values via <a href="https://man7.org/linux/man-pages/man2/statx.2.html">statx</a>; on older kernels, conservative defaults are used.</p>
<p>The <code>directIOWriter</code> currently covers chunk writes during compaction only, but that alone accounts for a substantial portion of Prometheus's I/O. The results are tangible: benchmarks show a 20–50% reduction in page cache usage, as measured by <code>container_memory_cache</code>.</p>
<p><a href="/assets/blog/2026-03-05/benchmark1.png"><img alt="benchmark1" src="/assets/blog/2026-03-05/benchmark1.png" /></a></p>
<p><a href="/assets/blog/2026-03-05/benchmark2.png"><img alt="benchmark2" src="/assets/blog/2026-03-05/benchmark2.png" /></a></p>
<p>The work is not done yet, and contributions are welcome. Here are a few areas that could help move the feature closer to General Availability:</p>
<h3>Covering more write paths</h3>
<p>Direct I/O is currently limited to chunk writes during compaction. Index files and WAL writes are natural next candidates, although they would require some additional work.</p>
<h3>Building more confidence around <code>directIOWriter</code></h3>
<p>All existing TSDB tests can be run against the <code>directIOWriter</code> using a dedicated build tag: <code>go test --tags=forcedirectio ./tsdb/</code>. More tests covering edge cases for the writer itself would be welcome, and there is even an idea of formally verifying that it never violates alignment requirements.</p>
<h3>Experimenting with <code>RWF_DONTCACHE</code></h3>
<p>Introduced in Linux kernel <code>v6.14</code>, <code>RWF_DONTCACHE</code> enables uncached buffered I/O, where data still goes through the page cache but the corresponding pages are dropped afterwards. It would be worth benchmarking whether this delivers similar benefits without direct I/O's alignment constraints.</p>
<h3>Support beyond Linux</h3>
<p>Support is currently Linux-only. Contributions to extend it to other operating systems are welcome.</p>
<p>For more details, see the <a href="https://github.com/prometheus/proposals/blob/main/proposals/0045-direct-io.md">proposal</a> and the <a href="https://github.com/prometheus/prometheus/pull/15365">PR</a> that introduced the feature.</p>
