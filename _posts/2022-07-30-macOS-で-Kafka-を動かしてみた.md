---
title: "macOS で Kafka を動かしてみた"
date: 2022-07-30
slug: "macOS で Kafka を動かしてみた"
category: blog
tags: [Tech,Apache Kafka]
---
<h2 id="はじめに">はじめに</h2>

<p>Kafka を手軽に試せないかな、と思ったら <a class="keyword" href="https://d.hatena.ne.jp/keyword/macOS">macOS</a> だと Homebrew でインストールできるようだったので試してみた。</p>

<h2 id="インストール起動">インストール&amp;起動</h2>

<p>以下のコマンドでインストールできる。
依存関係で <a class="keyword" href="https://d.hatena.ne.jp/keyword/JDK">JDK</a> 18 や、openssl関連、Zookeeper がインストールされる。</p>

<pre class="code" data-lang="" data-unlink>% brew install kafka</pre>


<p>Zookeeper, Kafka を起動する。</p>

<pre class="code" data-lang="" data-unlink>% brew services start zookeeper
==&gt; Successfully started `zookeeper` (label: homebrew.mxcl.zookeeper)</pre>




<pre class="code" data-lang="" data-unlink>% brew services start kafka
==&gt; Successfully started `kafka` (label: homebrew.mxcl.kafka)</pre>


<p>以下のコマンドで起動しているか確認できる。</p>

<pre class="code" data-lang="" data-unlink>% brew services list
Name       Status     User           File
kafka      started    daisukeshimizu ~/Library/LaunchAgents/homebrew.mxcl.kafka.plist
zookeeper  started    daisukeshimizu ~/Library/LaunchAgents/homebrew.mxcl.zookeeper.plist</pre>


<h2 id="Kakfa-の操作">Kakfa の操作</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C1%A5%E5%A1%BC%A5%C8%A5%EA%A5%A2%A5%EB">チュートリアル</a>の内容をさらっとやってみる。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fkafka.apache.org%2Fquickstart" title="Apache Kafka" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://kafka.apache.org/quickstart">kafka.apache.org</a></cite></p>

<h3 id="トピック作成">トピック作成</h3>

<p>トピックは、Kafka のブローカーにメッセージ(データ)を保存するための単位、メッセージの種類ごとに分けて保存するためのカテゴリー・分類として指定するもの、というイメージ。</p>

<p><code>kafka-topics</code> コマンドの <code>--create</code> オプションを使ってトピックを作成する。
v2.6 とかだと <code>--zookeeper</code> オプションが利用可能だったけど、<code>--zookeeper</code> オプションは無くなっていたようで指定できなかった。もともと非推奨とされていたけど <a href="#f-2b7b0a75" id="fn-2b7b0a75" name="fn-2b7b0a75" title="https://cwiki.apache.org/confluence/display/KAFKA/KIP-377%3A+TopicCommand+to+use+AdminClient">*1</a>、どのバージョンで使えなくなっていたのかはわかってない。</p>

<pre class="code" data-lang="" data-unlink>% kafka-topics --create --topic quickstart-events --bootstrap-server localhost:9092
Created topic quickstart-events.</pre>


<p>作成したトピックの詳細を表示してみる。
<code>--replication-factor</code> や <code>--partitions</code> は指定していないので 0 となっている。</p>

<pre class="code" data-lang="" data-unlink> % kafka-topics --describe --topic quickstart-events --bootstrap-server localhost:9092
Topic: quickstart-events    TopicId: vstR8s5VTDSYYwGSzEfeLQ PartitionCount: 1   ReplicationFactor: 1    Configs: segment.bytes=1073741824
    Topic: quickstart-events    Partition: 0    Leader: 0   Replicas: 0 Isr: 0</pre>


<h3 id="トピックへのデータ登録">トピックへのデータ登録</h3>

<p><code>kafka-console-producer</code> コマンドを使って、先ほど作成したトピックへデータを登録してみる。</p>

<pre class="code" data-lang="" data-unlink>% kafka-console-producer --topic quickstart-events --bootstrap-server localhost:9092</pre>


<p>コンソール上 <code>&gt;</code> で待ち状態となるので、以下のように入力し、<code>Ctrl</code> + <code>C</code> でキャンセルする。</p>

<pre class="code" data-lang="" data-unlink>&gt;This is my first event
&gt;This is my second event</pre>


<h3 id="トピックのデータ取得">トピックのデータ取得</h3>

<p><code>kafka-console-consumer</code> コマンドを使って、先ほどトピックへ登録したデータを取得してみる。<code>--from-beginning</code> オプションを使えば、残っているうちの一番最初のデータから取得できる。</p>

<pre class="code" data-lang="" data-unlink>% kafka-console-consumer --topic quickstart-events --from-beginning --bootstrap-server localhost:9092</pre>


<p>以下のような出力が得られる。</p>

<pre class="code" data-lang="" data-unlink>This is my first event
This is my second event</pre>


<h2 id="まとめ">まとめ</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/macOS">macOS</a> に Kafka をインストールして、すごく基本的な操作をやってみた。</p>
<div class="footnote">
<p class="footnote"><a href="#fn-2b7b0a75" id="f-2b7b0a75" name="f-2b7b0a75" class="footnote-number">*1</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://cwiki.apache.org/confluence/display/KAFKA/KIP-377%3A+TopicCommand+to+use+AdminClient">https://cwiki.apache.org/confluence/display/KAFKA/KIP-377%3A+TopicCommand+to+use+AdminClient</a></span></p>
</div>
