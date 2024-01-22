---
layout: post
Categories:  ["tech"]
Description:  " ElastiCache(Redis)をクラスターモードの有効と無効があるが、どっちを選んでもElastiCacheとしてはクラスターとなり、Redisの経験も浅い自分としてはよくわからなかったので調べた。 "
Tags: ["AWS", "ElastiCache", "Redis", "tech"]
date: "2019-07-07T21:10:00+09:00"
title: "ElastiCache Redisの構成要素とクラスターモード有効と無効の違い"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p>ElastiCache(Redis)を<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードの有効と無効があるが、どっちを選んでもElastiCacheとしては<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーとなり、Redisの経験も浅い自分としてはよくわからなかったので調べた。</p>
</body>

<!-- more -->

<body>
<h3>ElastiCache(Redis)を構成する<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%DD%A1%BC%A5%CD%A5%F3%A5%C8">コンポーネント</a>
</h3>


<p>下記ドキュメントに書かれている。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/AmazonElastiCache/latest/red-ug/WhatIs.Terms.html">Redis 用 ElastiCache の用語 - Redis 用 Amazon ElastiCache</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/AmazonElastiCache/latest/red-ug/WhatIs.Components.html">Redis 用 ElastiCache コンポーネントと機能</a></li>
</ul>


<p>登場する<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%DD%A1%BC%A5%CD%A5%F3%A5%C8">コンポーネント</a>は下記。</p>

<ul>
    <li>ノード</li>
ElastiCacheの最小の構成要素。メモリとかCPUが割り当てられている。(≒<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>みたいなものだと思っている。)
    <li>シャード</li>
複数のノードをまとめたグループ。データをシャードごとに分散して保存できる。シャードにはID(NodeGroupId)が割り当てられている。
1つのシャード内に複数のノードが存在することで、シャード内のノード間でデータが同期(<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>)される。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードが無効のElastiCache<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーではシャードは常に1つとなる。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードが有効のElastiCache<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーではシャードは最大90個となる。
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー</li>
シャードをまとめたグループ。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーにもID(ReplicationGroupID)が割り当てられる。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードが有効のElastiCache<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーでは、複数のシャードを作ることでデータがシャード間で分割される。</ul>


<p>なるほど、わかるようでよくわからんけど、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーは複数のシャードから成り、シャードは複数のノードから成るというのはわかった。</p>

<h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードを有効/無効とRedis Cluster</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードを無効にした場合、ElastiCacheとしてはMaster/Slave構成の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーとなる(≒Redis (<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードが無効) <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー)。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードを有効にした場合、ElastiCacheとしてはRedis Clusterとなる(≒Redis (<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードが有効) <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー)。</p>

<p>Redis (<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードが有効) <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー(≒Redis Cluster)の場合は、Redis Clusterの制約がそのまま当てはまる。
マルチマスター構成となるが、可用性を考えると最低3ノード、可用性を考えると6つのノードが推奨らしい。マスターとスレーブの2台1セットが3つで計6つ。
この場合、ElastiCache上の構成としては、ノードが6つ、シャードはマスターとスレーブの2台1セットが1シャードとなり計3つ、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーは1つとなる。
これでシャード(マスターとスレーブの2台1セット)毎にデータを分割して保存していける。</p>

<p>Redis (<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードが無効) <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー(≒Master/Slave構成の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー)ではマスターが1台のスレーブ複数台の構成しかとれない。
ノードは複数台いけるがマスターは1台、シャードと<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーは1つとなる。</p>

<p>いずれの構成もElastiCacheとしての制約もあるので、どっちが良いかは要件次第だと思う。</p>

<h3>まとめ</h3>


<p>ElastiCache Redisの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードの有効/無効の違いについて、ElastiCache Redisの構成要素を調べて整理した。
Cluster Modeの有効と無効を選択できるが、どちらにしてもElastiCacheとしては<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーである。
Cluster Modeの有効の場合はRedis Cluster(≒Redis (<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードが有効) <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー)、Cluster Modeの有効の場合はMaster/Slave構成の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー(≒Redis (<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードが無効) <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー)となる。</p>

<p>最初は、ElastiCache(Redis)としての用語と、Redisとしての用語がごちゃごちゃにならないように理解する必要があると思う。</p>

<h3>参考</h3>


<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/AmazonElastiCache/latest/red-ug/WhatIs.Terms.html">Redis 用 ElastiCache の用語 - Redis 用 Amazon ElastiCache</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/AmazonElastiCache/latest/red-ug/WhatIs.Components.html">Redis 用 ElastiCache コンポーネントと機能</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://redis.io/topics/cluster-tutorial">Redis cluster tutorial ? Redis</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://redis.io/topics/cluster-spec">Redis Cluster Specification ? Redis</a></li>
</ul>

</body>
