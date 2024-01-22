---
title: "Apache Spark のクラスターを Standalone Mode で動かしてみる"
date: 2022-06-25
slug: "Apache Spark のクラスターを Standalone Mode で動かしてみる"
category: blog
tags: [Tech]
---
<h2 id="はじめに">はじめに</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a> Spark の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードを動かしてみたく、 Standalone Mode で動かしてみた。</p>

<h2 id="環境">環境</h2>

<p>Spark、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Java">Java</a> 共に Homebrew  でインストールしたものを使う。</p>

<p>試した際の Spark のバージョンは <code>3.2.1</code>。</p>

<pre class="code" data-lang="" data-unlink>% spark-shell --version
22/08/20 23:03:29 WARN Utils: Your hostname, MacBook-Pro.local resolves to a loopback address: 127.0.0.1; using 192.168.0.110 instead (on interface en0)
22/08/20 23:03:29 WARN Utils: Set SPARK_LOCAL_IP if you need to bind to another address
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by org.apache.spark.unsafe.Platform (file:/usr/local/Cellar/apache-spark/3.2.1/libexec/jars/spark-unsafe_2.12-3.2.1.jar) to constructor java.nio.DirectByteBuffer(long,int)
WARNING: Please consider reporting this to the maintainers of org.apache.spark.unsafe.Platform
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  &#39;_/
   /___/ .__/\_,_/_/ /_/\_\   version 3.2.1
      /_/

Using Scala version 2.12.15, OpenJDK 64-Bit Server VM, 11.0.14.1
Branch HEAD
Compiled by user hgao on 2022-01-20T19:26:14Z
Revision 4f25b3f71238a00508a356591553f2dfa89f8290
Url https://github.com/apache/spark
Type --help for more information.</pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Java">Java</a> のバージョンは <code>11.0.14.1</code>。</p>

<pre class="code" data-lang="" data-unlink>% java --version
openjdk 11.0.14.1 2022-02-08
OpenJDK Runtime Environment Homebrew (build 11.0.14.1+0)
OpenJDK 64-Bit Server VM Homebrew (build 11.0.14.1+0, mixed mode)</pre>


<h2 id="クラスターモード"><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモード</h2>

<p>Spark のプログラムを実行する際に、そのプログラムを動かす場所となる<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーを指定できる。これによって分散処理を実行できる。
2022 年 5 月時点の 3.2 系では、以下の4つを利用可能。</p>

<ul>
<li>Standalone</li>
<li><a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache%20Mesos">Apache Mesos</a> (非推奨)</li>
<li>YARN</li>
<li><a class="keyword" href="http://d.hatena.ne.jp/keyword/Kubernetes">Kubernetes</a></li>
</ul>


<h2 id="Standalone-モードのクラスターを動かす">Standalone モードの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーを動かす</h2>

<p>Standaloneモードを使ってみる。<a href="#f-920942f9" name="fn-920942f9" title="https://spark.apache.org/docs/3.2.0/spark-standalone.html">*1</a></p>

<p>まず、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Java">Java</a> を利用できるように<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>を設定する。</p>

<pre class="code" data-lang="" data-unlink>export JAVA_HOME=&#39;/usr/local/Cellar/openjdk@11/11.0.14.1&#39;
export PATH=${JAVA_HOME}/bin:${PATH}</pre>


<p>マスターを起動する。デフォルトだとマスターとの通信に <code>spark://localhost:7077</code> が利用される。</p>

<pre class="code" data-lang="" data-unlink>% /usr/local/Cellar/apache-spark/3.2.1/libexec/sbin/start-master.sh
starting org.apache.spark.deploy.master.Master, logging to /usr/local/Cellar/apache-spark/3.2.1/libexec/logs/spark-xxxxxxxx-org.apache.spark.deploy.master.Master-1-xxxxxxxx.out</pre>


<p>スレーブを起動する。引数にマスターを指定する。</p>

<pre class="code" data-lang="" data-unlink>% /usr/local/Cellar/apache-spark/3.2.1/libexec/sbin/start-slave.sh spark://localhost:7077
This script is deprecated, use start-worker.sh
starting org.apache.spark.deploy.worker.Worker, logging to /usr/local/Cellar/apache-spark/3.2.1/libexec/logs/spark-xxxxxxxx-org.apache.spark.deploy.worker.Worker-1-xxxxxxxx.out</pre>


<p>各<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>共に引数を使ってオプションを指定できるけど、一旦デフォルトとする。</p>

<p><code>http://localhost:8080/</code> で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C0%A5%C3%A5%B7%A5%E5">ダッシュ</a>ボードを参照可能になる。この時点だと、 Workers の項目に1つ実行中である <code>Worker Id</code> が表示されているだけである。</p>

<h2 id="Standalone-モードのクラスター上でジョブを動かす">Standalone モードの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー上で、ジョブを動かす</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C1%A5%E5%A1%BC%A5%C8%A5%EA%A5%A2%A5%EB">チュートリアル</a>のプログラムを用意する。<a href="#f-f479fc91" name="fn-f479fc91" title="https://spark.apache.org/docs/latest/quick-start.html">*2</a></p>

<pre class="code SimpleApp.py" data-lang="SimpleApp.py" data-unlink>from pyspark.sql import SparkSession

logFile = &#34;/usr/local/Cellar/apache-spark/3.2.1/README.md&#34;  # Should be some file on your system
spark = SparkSession.builder.appName(&#34;SimpleApp&#34;).getOrCreate()
logData = spark.read.text(logFile).cache()

numAs = logData.filter(logData.value.contains(&#39;a&#39;)).count()
numBs = logData.filter(logData.value.contains(&#39;b&#39;)).count()

print(&#34;Lines with a: %i, lines with b: %i&#34; % (numAs, numBs))

spark.stop()</pre>


<p>このプログラムを<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー上で動かす。 <code>spark-submit</code> の引数に <code>--master spark://localhost:7077</code> として、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーを指定する。</p>

<pre class="code" data-lang="" data-unlink>% /usr/local/Cellar/apache-spark/3.2.1/libexec/bin/spark-submit --master spark://localhost:7077 SimpleApp.py</pre>


<p><code>http://localhost:8080/</code> にアクセスして<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C0%A5%C3%A5%B7%A5%E5">ダッシュ</a>ボードを見ると、<code>Running Applications</code> に <code>Application ID</code> が表示されていることが確認できる。ジョブが完了すると <code>Completed Applications</code> に <code>Application ID</code> が表示される。</p>

<p>とりあえず、これで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードで動くことが確認できた。</p>

<h2 id="クラスター停止"><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー停止</h2>

<p>以下でマスター、スレーブともに停止できる。</p>

<pre class="code" data-lang="" data-unlink>% /usr/local/Cellar/apache-spark/3.2.1/libexec/sbin/stop-master.sh</pre>




<pre class="code" data-lang="" data-unlink>% /usr/local/Cellar/apache-spark/3.2.1/libexec/sbin/stop-slave.sh </pre>


<h2 id="まとめ">まとめ</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a> Spark の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーを Standalone Mode で動かしてみた。
プロダクション用途で使うことはないと思うものの、Spark そのものがStandalone Modeの時にどうやって動いているのかまだよくわかっていないのでまたどこかで調べたい。</p>
<div class="footnote">
<p class="footnote"><a href="#fn-920942f9" name="f-920942f9" class="footnote-number">*1</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://spark.apache.org/docs/3.2.0/spark-standalone.html">https://spark.apache.org/docs/3.2.0/spark-standalone.html</a></span></p>
<p class="footnote"><a href="#fn-f479fc91" name="f-f479fc91" class="footnote-number">*2</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://spark.apache.org/docs/latest/quick-start.html">https://spark.apache.org/docs/latest/quick-start.html</a></span></p>
</div>
