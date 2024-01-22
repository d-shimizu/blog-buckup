---
layout: post
Categories:  ["tech"]
Description:  " はじめに  さくらのVPS 512のFreeBSDでZFSを使ってますが、動作しているプロセスでメモリ使用量が高いものはないにもかかわらずメモリ使用率が高く、どうもZFSがメモリを多量に消費してswapが多発いるようでした。 その際に対応"
Tags: ["ZFS", "FreeBSD"]
date: "2015-10-31T23:55:00+09:00"
title: "さくらのVPS 512のFreeBSDでZFSのメモリ設定"
author: "dshimizu"
archive: ["2015"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p>さくらの<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPS">VPS</a> 512の<a class="keyword" href="http://d.hatena.ne.jp/keyword/FreeBSD">FreeBSD</a>で<a class="keyword" href="http://d.hatena.ne.jp/keyword/ZFS">ZFS</a>を使ってますが、動作しているプロセスでメモリ使用量が高いものはないにもかかわらずメモリ使用率が高く、どうも<a class="keyword" href="http://d.hatena.ne.jp/keyword/ZFS">ZFS</a>がメモリを多量に消費してswapが多発いるようでした。<br>その際に対応した内容を記載します。 </p> <a name="more"></a> <h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AB%A1%BC%A5%CD%A5%EB">カーネル</a>と<a class="keyword" href="http://d.hatena.ne.jp/keyword/ZFS">ZFS</a>のメモリパラメータの見直し</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AB%A1%BC%A5%CD%A5%EB">カーネル</a>に割り当てるメモリと <a class="keyword" href="http://d.hatena.ne.jp/keyword/ZFS">ZFS</a> のキャッシュに割り当てるメモリの2つを調整します。 </p> <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AB%A1%BC%A5%CD%A5%EB">カーネル</a>パラメータの見直し</h5> <p>1GBの物理メモリがある状態では、<a class="keyword" href="http://d.hatena.ne.jp/keyword/FreeBSD">FreeBSD</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AB%A1%BC%A5%CD%A5%EB">カーネル</a>に割り当てられるメモリはデフォルトでは以下でした。 </p> <pre class="terminal">  &gt; sysctl -a | grep vm.kmem_size vm.kmem_size_scale: 1 vm.kmem_size_max: 1319413950874 vm.kmem_size_min: 0 vm.kmem_size: 1017532416  </pre> <p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AB%A1%BC%A5%CD%A5%EB">カーネル</a>に割り当てるメモリとその最大値を物理メモリを超えない範囲で設定しておきます。いろいろ状態を見ながら、以下のように<code>/boot/loader.conf</code>へ追記しました。 </p> <pre class="terminal">  vm.kmem_size="330M" vm.kmem_size_max="512M"  </pre>  <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/ZFS">ZFS</a>のメモリチューニング</h5> <p><a class="keyword" href="http://d.hatena.ne.jp/keyword/ZFS">ZFS</a>のARCに割り当てられるメモリはデフォルトでは以下でした。 </p>
<pre class="terminal">  vfs.zfs.arc_min: 79494720 vfs.zfs.arc_max: 635957760  </pre> <p><a class="keyword" href="http://d.hatena.ne.jp/keyword/ZFS">ZFS</a> ARCに割り当てるメモリの最大値とキャッシュに割り当てるサイズを調整しておきます。いろいろ状態を見ながら、以下のように<code>/boot/loader.conf</code>へ追記しました。 </p> <pre class="terminal">  vfs.zfs.arc_max="64M" vfs.zfs.vdev.cache.size="4M"  </pre> <h5>一般的な設定</h5> <p>最後に<a class="keyword" href="http://d.hatena.ne.jp/keyword/ZFS">ZFS</a>の一般的な以下の設定を<code>/boot/loader.conf</code>へ追記しました。 </p> <pre class="terminal">  vfs.zfs.prefetch_disable="1" vfs.zfs.txg.timeout="5" kern.maxvnodes="250000" vfs.zfs.write_limit_override="1073741824"  </pre>  <h4>おわりに</h4>
<p>今のところこれでだいぶ安定していますが、しばらく状態を見ながら必要に応じてパラメータをいじっていこうと思います。 </p>  <h4>参考</h4> <ul>  <li><a href="https://www.freebsd.org/doc/handbook/zfs-advanced.html">19.6.Advanced Topics</a></li>
</ul>
</body>

<!-- more -->


