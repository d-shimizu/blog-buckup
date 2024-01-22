---
title: "Apache Kafka の構成要素とかに関して調べたことの自分用メモ"
date: 2022-04-02
slug: "Apache Kafka の構成要素とかに関して調べたことの自分用メモ"
category: blog
tags: [Tech]
---
<h2 id="はじめに">はじめに</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Managed Streaming for <a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a> Kafka (MSK) を触る必要が出てきたので、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a> Kafka の概要を浅く理解するために調べた自分用メモ。
まだあんまりわかっていないので、誤ったことを書いている可能性が高いので、都度修正する予定。</p>

<h2 id="Apache-Kafka"><a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a> Kafka</h2>

<ul>
<li><a href="https://kafka.apache.org/documentation/">https://kafka.apache.org/documentation/</a><cite class="hatena-citation"><a href="https://kafka.apache.org/documentation/">kafka.apache.org</a></cite></li>
</ul>


<h2 id="用語系">用語系</h2>

<h3 id="イベントストリーミング">イベントストリーミング</h3>

<p>ドキュメントではイベントストリーミングプラットフォームとされている。</p>

<p>イベントストリーミングが何か、明確な言葉の定義があるのかわかっていないが、モバイルデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>やセンサー、システムを利用しているユーザーのアクションにより発生するデータなど、何らかのデータを発生させる端末やシステム(＝イベントソース？)から、短い間隔で継続的(無制限に)に発生して送られてくるいろんな形式のデータ群、みたいな感じだと思われる。</p>

<p>イベントストリームプラットフォームが何か、というと、ドキュメントでは、イベントを読み書きしたり、継続的に他のシステムからimport/exportしたり、必要な期間保持し続けたり、イベント発生時や過去のデータも処理することができるもの、とのこと。</p>

<h3 id="イベント">イベント</h3>

<p>何かが起こったことの記録(≒発生するデータ)。Kafka (というかイベントストリーミング系のシステム?)ではレコードまたはメッセージなどとも呼ばれる模様。</p>

<h2 id="全体構成の概要図">全体構成の概要図</h2>

<p>多分こんな感じっぽい。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220711/20220711175545.png" width="1200" height="701" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<p>Broker <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーの管理を、 Zookeeper ではなく、Broker自体の機能に含める、といった大きな変更が進んでいるようなので、今後のバージョンで Zookeeper はなくなっていく模様。<a href="#f-1e495556" name="fn-1e495556" title="[https://issues.apache.org/jira/browse/KAFKA-9119:title]">*1</a>
MSK を使っている場合は Zookeeper もマネージドになっているので直<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C0%DC%BF%A8">接触</a>る機会はないけど、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a> ZooKeeper の接続エンドポイントは払い出されるので、意識する必要はある。</p>

<ul>
<li><a href="https://cwiki.apache.org/confluence/display/KAFKA/KIP-500%3A+Replace+ZooKeeper+with+a+Self-Managed+Metadata+Quorum">KIP-500: Replace ZooKeeper with a Self-Managed Metadata Quorum - Apache Kafka - Apache Software Foundation</a></li>
</ul>


<h2 id="構成要素">構成要素</h2>

<h3 id="Broker">Broker</h3>

<p>Kafka の本体。以下の Topic/Partition によって、Broker にイベントのデータが保管される。
Kakfa Producer はここにデータを送信し、Kakfa Consumer はここからデータを取得する。
基本的には複数台の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー構成となる。現状では、Zookeeper が複数台の Broker を管理する。</p>

<h4 id="Topic">Topic</h4>

<p>Broker 内でのデータの保存先の単位みたいなもの。
あるデータ群は Topic A に、また別のデータ群は Topic B に保存する、扱うデータの種類に応じて Topic を分ける、といった感じの使い方になると思われる。</p>

<h4 id="Partition">Partition</h4>

<p>1つの Topic に保存されるデータを、分散するための仕組み、というイメージ。
1つの Topic は複数のPartitionで構成されており、各 Partition は Broker 間で複製された複数の Replica で構成される。</p>

<h3 id="Producer">Producer</h3>

<p>Kakfa Broker 上の Topic の Partition へデータを書き込むためのクライアント。
Kafka へデータを書き込むアプリケーションは Kakfa Producer のライブラリを使用して、データを書き込む。</p>

<h3 id="Consumer">Consumer</h3>

<p>Kakfa Broker 上の Topic の Partion からデータを取得/読み込むためのクライアント。
Kakfa からデータを取得するアプリケーションは Kakfa Consumer ライブラリを使用して、データを読み込む。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://deeeet.com/writing/2015/09/01/apache-kafka/">Apache Kafka&#x306B;&#x5165;&#x9580;&#x3057;&#x305F; | Taichi Nakashima</a></li>
<li><a href="https://qiita.com/keikesu0122/items/48be51a65d34d90c11e9">Apache Kafka &#x5165;&#x9580; - Qiita</a></li>
<li><a href="https://kafka.apache.org/documentation/">Apache Kafka</a></li>
<li><a href="https://qiita.com/sigmalist/items/5a26ab519cbdf1e07af3">Apache Kafka&#x306E;&#x6982;&#x8981;&#x3068;&#x30A2;&#x30FC;&#x30AD;&#x30C6;&#x30AF;&#x30C1;&#x30E3; - Qiita</a></li>
<li><a href="https://qiita.com/kimutansk/items/60e48ec15e954fa95e1c">&#x30B9;&#x30C8;&#x30EA;&#x30FC;&#x30E0;&#x51E6;&#x7406;&#x3068;&#x306F;&#x4F55;&#x304B;&#xFF1F;&#xFF0B;2016&#x5E74;&#x306E;&#x51FA;&#x6765;&#x4E8B; - Qiita</a></li>
<li><a href="https://joker1007.hatenablog.com/entry/2022/01/19/170608">Kafka&#x5165;&#x9580; &#x7B2C;1&#x56DE; &#x300C;&#x305D;&#x3082;&#x305D;&#x3082;Kafka&#x3068;&#x306F;&#x306A;&#x306B;&#x304B;&#x300D; - joker1007&rsquo;s diary</a></li>
<li><a href="https://qiita.com/ShinHashitani/items/f8f131748f4649926017">Cloud Native Kafka - Zookeeper&#x306E;&#x3044;&#x3089;&#x306A;&#x3044;Kafka - Qiita</a></li>
<li><a href="https://qiita.com/masanori0001/items/5ea5f69b875a9675efc4">&#x30B9;&#x30C8;&#x30EA;&#x30FC;&#x30DF;&#x30F3;&#x30B0;&#x51E6;&#x7406;&#x3092;&#x8003;&#x3048;&#x308B; - Qiita</a></li>
</ul>

<div class="footnote">
<p class="footnote"><a href="#fn-1e495556" name="f-1e495556" class="footnote-number">*1</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://issues.apache.org/jira/browse/KAFKA-9119">[KAFKA-9119] KIP-500: Replace ZooKeeper with a Metadata Quorum - ASF JIRA</a></span></p>
</div>
