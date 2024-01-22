---
title: "Apache Spark のクラスターモードと YARN で動く際の概要についてちょっと調べたメモ書き"
date: 2022-05-28
slug: "Apache Spark のクラスターモードと YARN で動く際の概要についてちょっと調べたメモ書き"
category: blog
tags: [Tech]
---
<h2 id="はじめに">はじめに</h2>

<p>Spark を <a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> の基盤で動かしてみて、そもそもどういう仕組みで動いているんだろうか、と気になったので調べたことのメモ。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fspark.apache.org%2Fdocs%2F3.2.0%2Fcluster-overview.html" title="Cluster Mode Overview - Spark 3.2.0 Documentation" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://spark.apache.org/docs/3.2.0/cluster-overview.html">spark.apache.org</a></cite></p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fspark.apache.org%2Fdocs%2F3.2.0%2Frunning-on-yarn.html" title="Running Spark on YARN - Spark 3.2.0 Documentation" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://spark.apache.org/docs/3.2.0/running-on-yarn.html">spark.apache.org</a></cite></p>

<h2 id="Spark-のクラスターモード">Spark の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモード</h2>

<p>Spark 自体に、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードという機能があるようで、ジョブ送信時にどの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーに接続するか、というのを指定できる。
これによって、分散システム基盤とうまく連動して処理が可能となっているようである。</p>

<p>2022 年 5 月時点の 3.2 系では、以下の4つが利用可能な模様。<a href="#f-89200dcd" name="fn-89200dcd" title="https://spark.apache.org/docs/3.2.0/cluster-overview.html:title">*1</a></p>

<ul>
<li>Standalone</li>
<li><a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache%20Mesos">Apache Mesos</a> (非推奨)</li>
<li><a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> YARN</li>
<li><a class="keyword" href="http://d.hatena.ne.jp/keyword/Kubernetes">Kubernetes</a></li>
</ul>


<p>2.2.0 から <a class="keyword" href="http://d.hatena.ne.jp/keyword/K8s">K8s</a> も実験的にサポートが開始され、2.3.0 で公式サポートという形になったようで、また、Mesos は非推奨となっている。</p>

<h2 id="Spark-のクラスターモードの構成">Spark の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードの構成</h2>

<p>厳密には Spark の機能というよりは、外部リソースとして扱うための仕組み、というイメージになるのかもしれない。</p>

<p><code>spark-submit</code> でアプリケーションを実行する際に、 <code>--master</code> オプションを利用することでどの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードを利用するかを指定できる。<a href="#f-85fe0b09" name="fn-85fe0b09" title="https://spark.apache.org/docs/3.2.0/submitting-applications.html#master-urls:title">*2</a></p>

<p>YARN を利用する場合は <code>--master yarn</code> と指定する。<a href="#f-99c14d41" name="fn-99c14d41" title="http://spark.apache.org/docs/3.2.0/cluster-overview.html:title">*3</a>
この場合、Cluster Manager に相当するのは YARN の Resource Manager になる。</p>

<ul>
<li><a href="https://spark.apache.org/docs/3.2.0/cluster-overview.html#components">Cluster Mode Overview - Spark 3.2.0 Documentation</a></li>
</ul>


<h2 id="Driver">Driver</h2>

<p>main() 関数を実行し、SparkContext を作成するプロセス。</p>

<h2 id="Executor">Executor</h2>

<p>ワーカーノード上のアプリケーション用に起動されたプロセス。
タスクを実行し、タスク全体のメモリまたはストレージにデータを保持する。
Executor の中での処理はJob, Stage, Taskという段階的な処理になっているようだがこの辺りはまだ詳しくわかっていない。 <a href="#f-bdabcb8f" name="fn-bdabcb8f" title="https://qiita.com/sigmalist/items/ea4127332abc12a99a45#spark%E3%81%AE%E5%87%A6%E7%90%86%E3%82%B8%E3%83%A7%E3%83%96%E3%82%B9%E3%83%86%E3%83%BC%E3%82%B8%E3%82%BF%E3%82%B9%E3%82%AF%E3%81%A8%E3%83%87%E3%83%BC%E3%82%BFrdd%E3%83%91%E3%83%BC%E3%83%86%E3%82%A3%E3%82%B7%E3%83%A7%E3%83%B3">*4</a></p>

<h2 id="EMR-で動く場合のイメージ図">EMR で動く場合のイメージ図</h2>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220818/20220818182949.png" width="701" height="630" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<h2 id="まとめ">まとめ</h2>

<p>ざっと概要についてまとめた。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/RDD">RDD</a> とかはまだあまりわかっていないので追って雑にまとめたい。</p>
<div class="footnote">
<p class="footnote"><a href="#fn-89200dcd" name="f-89200dcd" class="footnote-number">*1</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://spark.apache.org/docs/3.2.0/cluster-overview.html">Cluster Mode Overview - Spark 3.2.0 Documentation</a></span></p>
<p class="footnote"><a href="#fn-85fe0b09" name="f-85fe0b09" class="footnote-number">*2</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://spark.apache.org/docs/3.2.0/submitting-applications.html#master-urls">Submitting Applications - Spark 3.2.0 Documentation</a></span></p>
<p class="footnote"><a href="#fn-99c14d41" name="f-99c14d41" class="footnote-number">*3</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="http://spark.apache.org/docs/3.2.0/cluster-overview.html">Cluster Mode Overview - Spark 3.2.0 Documentation</a></span></p>
<p class="footnote"><a href="#fn-bdabcb8f" name="f-bdabcb8f" class="footnote-number">*4</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://qiita.com/sigmalist/items/ea4127332abc12a99a45#spark%E3%81%AE%E5%87%A6%E7%90%86%E3%82%B8%E3%83%A7%E3%83%96%E3%82%B9%E3%83%86%E3%83%BC%E3%82%B8%E3%82%BF%E3%82%B9%E3%82%AF%E3%81%A8%E3%83%87%E3%83%BC%E3%82%BFrdd%E3%83%91%E3%83%BC%E3%83%86%E3%82%A3%E3%82%B7%E3%83%A7%E3%83%B3">https://qiita.com/sigmalist/items/ea4127332abc12a99a45#spark%E3%81%AE%E5%87%A6%E7%90%86%E3%82%B8%E3%83%A7%E3%83%96%E3%82%B9%E3%83%86%E3%83%BC%E3%82%B8%E3%82%BF%E3%82%B9%E3%82%AF%E3%81%A8%E3%83%87%E3%83%BC%E3%82%BFrdd%E3%83%91%E3%83%BC%E3%83%86%E3%82%A3%E3%82%B7%E3%83%A7%E3%83%B3</a></span></p>
</div>
