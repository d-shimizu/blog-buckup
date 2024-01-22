---
title: "macOS で PySpark を試す"
date: 2022-06-12
slug: "macOS で PySpark を試す"
category: blog
tags: [Tech]
---
<h2 id="はじめに">はじめに</h2>

<p>PySpark がどんなものかまずはざっくり把握したく、<a class="keyword" href="http://d.hatena.ne.jp/keyword/macOS">macOS</a> で触ってみたかったのでやってみた。</p>

<h2 id="Spark">Spark</h2>

<p>大規模なデータ処理を行うためのソフトウェア(ドキュメントの言葉を使うと unified analytics engine)。
1台のサーバー上で動かすこともできるけど、YARNや<a class="keyword" href="http://d.hatena.ne.jp/keyword/K8s">K8s</a>などの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%EC%A1%BC%A5%E0%A5%EF%A1%BC%A5%AF">フレームワーク</a>と組み合わせることで複数のサーバーで分散処理を行うことができる。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fspark.apache.org%2Fdocs%2Flatest%2F" title="Overview - Spark 3.3.0 Documentation" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://spark.apache.org/docs/latest/">spark.apache.org</a></cite></p>

<h2 id="PySpark">PySpark</h2>

<p>Spark は <a class="keyword" href="http://d.hatena.ne.jp/keyword/Scala">Scala</a>で実装されているため、Spark を <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> で実行できるようにするための <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 用の <a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a> 。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fspark.apache.org%2Fdocs%2Flatest%2Fapi%2Fpython%2F" title="PySpark Documentation — PySpark 3.3.0 documentation" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://spark.apache.org/docs/latest/api/python/">spark.apache.org</a></cite></p>

<h2 id="Spark-インストール">Spark インストール</h2>

<p>Homebrew で Spark をインストールできる。</p>

<p><code>/usr/local/Cellar/apache-spark/3.2.1/</code> に必要なファイルが配置される。
<code>pyspark</code> コマンドもここの配下に配置され、PySpark も使うことができる。</p>

<pre class="code" data-lang="" data-unlink> % brew install apache-spark</pre>


<p>依存関係で <code>openjdk@11</code> もインストールされる。</p>

<pre class="code" data-lang="" data-unlink>==&gt; Downloading https://ghcr.io/v2/homebrew/core/openjdk/11/manifests/11.0.14.1
Already downloaded: /Users/daisukeshimizu/Library/Caches/Homebrew/downloads/234ae4acbb1a62a43741a4774e7b414ce200de929b1ea9d67058e97e9b55d8f7--openjdk@11-11.0.14.1.bottle_manifest.json
==&gt; Downloading https://ghcr.io/v2/homebrew/core/openjdk/11/blobs/sha256:44d92f169bc3ab7dfe2645dfdef33128bbe610f6aab84fc85a8cc9fb832de281
Already downloaded: /Users/daisukeshimizu/Library/Caches/Homebrew/downloads/93bc1716317946ab4887e71c68011db6367ed6e006f2ec3d111edbd226e1c77a--openjdk@11--11.0.14.1.catalina.bottle.tar.gz
==&gt; Downloading https://ghcr.io/v2/homebrew/core/apache-spark/manifests/3.2.1
Already downloaded: /Users/daisukeshimizu/Library/Caches/Homebrew/downloads/900859faa97b53bf17aba5521cb831ed3cb04ea7d25d6b657a67e3d89ac9b002--apache-spark-3.2.1.bottle_manifest.json
==&gt; Downloading https://ghcr.io/v2/homebrew/core/apache-spark/blobs/sha256:3f8f3309dfef579496100aad618c4f46b55795497fe259f2accb0da0c15da7ec
Already downloaded: /Users/daisukeshimizu/Library/Caches/Homebrew/downloads/b91e2c88cd7ad0a9782efaab00ddabaebaeecbfe4043558b7f68d26caada17ed--apache-spark--3.2.1.all.bottle.tar.gz
==&gt; Installing dependencies for apache-spark: openjdk@11
==&gt; Installing apache-spark dependency: openjdk@11
==&gt; Pouring openjdk@11--11.0.14.1.catalina.bottle.tar.gz
🍺  /usr/local/Cellar/openjdk@11/11.0.14.1: 678 files, 298.3MB
==&gt; Installing apache-spark
==&gt; Pouring apache-spark--3.2.1.all.bottle.tar.gz
🍺  /usr/local/Cellar/apache-spark/3.2.1: 1,472 files, 322MB
==&gt; Running `brew cleanup apache-spark`...
Disable this behaviour by setting HOMEBREW_NO_INSTALL_CLEANUP.
Hide these hints with HOMEBREW_NO_ENV_HINTS (see `man brew`).</pre>


<h2 id="PySpark-起動">PySpark 起動</h2>

<p><code>pyspark</code> コマンドを実行する。</p>

<pre class="code" data-lang="" data-unlink>% pyspark </pre>


<p>以下のような出力がありつつ、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> ベースの Spark Shell が起動する。
起動時のメッセージ <code>Spark context available as 'sc' (master = local[*], app id = local-**********).</code> にある通り、ローカルマシン上の論理コアと同数のワーカースレッドで単独実行される形となっている。</p>

<pre class="code" data-lang="" data-unlink>Python 3.7.9 (v3.7.9:13c94747c7, Aug 15 2020, 01:31:08)
[Clang 6.0 (clang-600.0.57)] on darwin
Type &#34;help&#34;, &#34;copyright&#34;, &#34;credits&#34; or &#34;license&#34; for more information.
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by org.apache.spark.unsafe.Platform (file:/usr/local/Cellar/apache-spark/3.2.1/libexec/jars/spark-unsafe_2.12-3.2.1.jar) to constructor java.nio.DirectByteBuffer(long,int)
WARNING: Please consider reporting this to the maintainers of org.apache.spark.unsafe.Platform
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
Using Spark&#39;s default log4j profile: org/apache/spark/log4j-defaults.properties
Setting default log level to &#34;WARN&#34;.
To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
22/06/12 19:26:36 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  &#39;_/
   /__ / .__/\_,_/_/ /_/\_\   version 3.2.1
      /_/

Using Python version 3.7.9 (v3.7.9:13c94747c7, Aug 15 2020 01:31:08)
Spark context Web UI available at http://192.168.0.110:4040
Spark context available as &#39;sc&#39; (master = local[*], app id = local-1660991198151).
SparkSession available as &#39;spark&#39;.
&gt;&gt;&gt;</pre>


<h2 id="チュートリアルをやる"><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C1%A5%E5%A1%BC%A5%C8%A5%EA%A5%A2%A5%EB">チュートリアル</a>をやる</h2>

<p>以下の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C1%A5%E5%A1%BC%A5%C8%A5%EA%A5%A2%A5%EB">チュートリアル</a>をやりつつ動きを見てみる。</p>

<p><a href="https://spark.apache.org/docs/latest/quick-start.html">Quick Start - Spark 3.3.0 Documentation</a></p>

<p>まずはWord Countみたいなものになっている。</p>

<h3 id="チュートリアルBaisc"><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C1%A5%E5%A1%BC%A5%C8%A5%EA%A5%A2%A5%EB">チュートリアル</a>(Baisc)</h3>

<p>Spark では Datasets と呼ばれるアイテムの分散コレクション(データの集まり)という概念がある。
まずはこのDatasetsを作成することになるが、ただ、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C1%A5%E5%A1%BC%A5%C8%A5%EA%A5%A2%A5%EB">チュートリアル</a>の内容を読んでいると、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> の場合は型を指定する概念がないため、 Dataset も型を指定する必要はない模様。また、これによって Pandas と R の DataFrame の概念に合わせて、DataFrame と呼ぶらしい。</p>

<p>Spark ソース<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リの README.md ファイルのテキストから新しい DataFrame を作成する。PySpark ではなく、Spark として処理する場合は DataFrame ではなく、Datasets になると思われる。
Spark のソース<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リは <a class="keyword" href="http://d.hatena.ne.jp/keyword/macOS">macOS</a> で Homebrew でインストールした場合は <code>/usr/local/Cellar/apache-spark/</code> 以下になる。</p>

<pre class="code" data-lang="" data-unlink>&gt;&gt;&gt; textFile = spark.read.text(&#34;/usr/local/Cellar/apache-spark/3.2.1/README.md&#34;)</pre>


<p>行数を数える。</p>

<pre class="code" data-lang="" data-unlink>&gt;&gt;&gt; textFile.count()
109</pre>


<p>最初の行を出力する。</p>

<pre class="code" data-lang="" data-unlink>&gt;&gt;&gt; textFile.first()
Row(value=&#39;# Apache Spark&#39;)</pre>


<p>Spark という文字列を含む行数を数える。</p>

<pre class="code" data-lang="" data-unlink>&gt;&gt;&gt; textFile.filter(textFile.value.contains(&#34;Spark&#34;)).count()
19</pre>


<h3 id="チュートリアルPySparkアプリケーション実行"><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C1%A5%E5%A1%BC%A5%C8%A5%EA%A5%A2%A5%EB">チュートリアル</a>(PySparkアプリケーション実行)</h3>

<p><code>spark-submit</code> によって PySpark アプリケーションを実行してみる。</p>

<p><code>SimpleApp.py</code> を作成して適当な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リに保存する。
これは、<code>/usr/local/Cellar/apache-spark/3.2.1/README.md</code> ファイル内に <code>a</code>, <code>b</code> の文字がいくつ出てきたかをカウントする。</p>

<pre class="code SimpleApp.py" data-lang="SimpleApp.py" data-unlink>from pyspark.sql import SparkSession

logFile = &#34;/usr/local/Cellar/apache-spark/3.2.1/README.md&#34;
spark = SparkSession.builder.appName(&#34;SimpleApp&#34;).getOrCreate()
logData = spark.read.text(logFile).cache()

numAs = logData.filter(logData.value.contains(&#39;a&#39;)).count()
numBs = logData.filter(logData.value.contains(&#39;b&#39;)).count()

print(&#34;Lines with a: %i, lines with b: %i&#34; % (numAs, numBs))

spark.stop()</pre>


<p>実行してみる。</p>

<pre class="code" data-lang="" data-unlink>% spark-submit --master local[*] SimpleApp.py</pre>


<p>出力がドバドバ出る。細かくみると Executer の動きをもう少し把握できそうだけどいったん置いておいて、最後の方に <code>Lines with a: 65, lines with b: 33</code> と出力される。</p>

<pre class="code" data-lang="" data-unlink>WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by org.apache.spark.unsafe.Platform (file:/usr/local/Cellar/apache-spark/3.2.1/libexec/jars/spark-unsafe_2.12-3.2.1.jar) to constructor java.nio.DirectByteBuffer(long,int)
WARNING: Please consider reporting this to the maintainers of org.apache.spark.unsafe.Platform
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
Using Spark&#39;s default log4j profile: org/apache/spark/log4j-defaults.properties
22/06/12 22:04:07 INFO SparkContext: Running Spark version 3.2.1
22/06/12 22:04:07 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
22/06/12 22:04:08 INFO ResourceUtils: ==============================================================
22/06/12 22:04:08 INFO ResourceUtils: No custom resources configured for spark.driver.
22/06/12 22:04:08 INFO ResourceUtils: ==============================================================
22/06/12 22:04:08 INFO SparkContext: Submitted application: SimpleApp
22/06/12 22:04:08 INFO ResourceProfile: Default ResourceProfile created, executor resources: Map(cores -&gt; name: cores, amount: 1, script: , vendor: , memory -&gt; name: memory, amount: 1024, script: , vendor: , offHeap -&gt; name: offHeap, amount: 0, script: , vendor: ), task resources: Map(cpus -&gt; name: cpus, amount: 1.0)
22/06/12 22:04:08 INFO ResourceProfile: Limiting resource is cpu
22/06/12 22:04:08 INFO ResourceProfileManager: Added ResourceProfile id: 0
22/06/12 22:04:08 INFO SecurityManager: Changing view acls to: daisukeshimizu
22/06/12 22:04:08 INFO SecurityManager: Changing modify acls to: daisukeshimizu
22/06/12 22:04:08 INFO SecurityManager: Changing view acls groups to:
22/06/12 22:04:08 INFO SecurityManager: Changing modify acls groups to:
22/06/12 22:04:08 INFO SecurityManager: SecurityManager: authentication disabled; ui acls disabled; users  with view permissions: Set(daisukeshimizu); groups with view permissions: Set(); users  with modify permissions: Set(daisukeshimizu); groups with modify permissions: Set()
22/06/12 22:04:08 INFO Utils: Successfully started service &#39;sparkDriver&#39; on port 60424.
22/06/12 22:04:08 INFO SparkEnv: Registering MapOutputTracker
22/06/12 22:04:08 INFO SparkEnv: Registering BlockManagerMaster
22/06/12 22:04:08 INFO BlockManagerMasterEndpoint: Using org.apache.spark.storage.DefaultTopologyMapper for getting topology information
22/06/12 22:04:08 INFO BlockManagerMasterEndpoint: BlockManagerMasterEndpoint up
22/06/12 22:04:09 INFO SparkEnv: Registering BlockManagerMasterHeartbeat
22/06/12 22:04:09 INFO DiskBlockManager: Created local directory at /private/var/folders/6_/yxwcl8kn6dz_fpt7r0z1qd8h0000gn/T/blockmgr-d53cb198-ba7d-4cd9-9d5e-2d751b8e1185
22/06/12 22:04:09 INFO MemoryStore: MemoryStore started with capacity 434.4 MiB
22/06/12 22:04:09 INFO SparkEnv: Registering OutputCommitCoordinator
22/06/12 22:04:09 WARN Utils: Service &#39;SparkUI&#39; could not bind on port 4040. Attempting port 4041.
22/06/12 22:04:09 INFO Utils: Successfully started service &#39;SparkUI&#39; on port 4041.
22/06/12 22:04:09 INFO SparkUI: Bound SparkUI to 0.0.0.0, and started at http://192.168.0.110:4041
22/06/12 22:04:09 INFO Executor: Starting executor ID driver on host 192.168.0.110
22/06/12 22:04:09 INFO Utils: Successfully started service &#39;org.apache.spark.network.netty.NettyBlockTransferService&#39; on port 60425.
22/06/12 22:04:09 INFO NettyBlockTransferService: Server created on 192.168.0.110:60425
22/06/12 22:04:09 INFO BlockManager: Using org.apache.spark.storage.RandomBlockReplicationPolicy for block replication policy
22/06/12 22:04:09 INFO BlockManagerMaster: Registering BlockManager BlockManagerId(driver, 192.168.0.110, 60425, None)
22/06/12 22:04:09 INFO BlockManagerMasterEndpoint: Registering block manager 192.168.0.110:60425 with 434.4 MiB RAM, BlockManagerId(driver, 192.168.0.110, 60425, None)
22/06/12 22:04:09 INFO BlockManagerMaster: Registered BlockManager BlockManagerId(driver, 192.168.0.110, 60425, None)
22/06/12 22:04:09 INFO BlockManager: Initialized BlockManager: BlockManagerId(driver, 192.168.0.110, 60425, None)
22/06/12 22:04:10 INFO SharedState: Setting hive.metastore.warehouse.dir (&#39;null&#39;) to the value of spark.sql.warehouse.dir.
22/06/12 22:04:10 INFO SharedState: Warehouse path is &#39;file:/Users/daisukeshimizu/local/bin/spark-warehouse&#39;.
22/06/12 22:04:12 INFO InMemoryFileIndex: It took 53 ms to list leaf files for 1 paths.
22/06/12 22:04:14 INFO FileSourceStrategy: Pushed Filters:
22/06/12 22:04:14 INFO FileSourceStrategy: Post-Scan Filters:
22/06/12 22:04:14 INFO FileSourceStrategy: Output Data Schema: struct&lt;value: string&gt;
22/06/12 22:04:16 INFO CodeGenerator: Code generated in 271.021986 ms
22/06/12 22:04:16 INFO MemoryStore: Block broadcast_0 stored as values in memory (estimated size 189.9 KiB, free 434.2 MiB)
22/06/12 22:04:16 INFO MemoryStore: Block broadcast_0_piece0 stored as bytes in memory (estimated size 32.8 KiB, free 434.2 MiB)
22/06/12 22:04:16 INFO BlockManagerInfo: Added broadcast_0_piece0 in memory on 192.168.0.110:60425 (size: 32.8 KiB, free: 434.4 MiB)
22/06/12 22:04:16 INFO SparkContext: Created broadcast 0 from count at NativeMethodAccessorImpl.java:0
22/06/12 22:04:16 INFO FileSourceScanExec: Planning scan with bin packing, max size: 4194304 bytes, open cost is considered as scanning 4194304 bytes.
22/06/12 22:04:16 INFO DefaultCachedBatchSerializer: Predicate isnotnull(value#0) generates partition filter: ((value.count#37 - value.nullCount#36) &gt; 0)
22/06/12 22:04:16 INFO DAGScheduler: Registering RDD 8 (count at NativeMethodAccessorImpl.java:0) as input to shuffle 0
22/06/12 22:04:16 INFO DAGScheduler: Got map stage job 0 (count at NativeMethodAccessorImpl.java:0) with 1 output partitions
22/06/12 22:04:16 INFO DAGScheduler: Final stage: ShuffleMapStage 0 (count at NativeMethodAccessorImpl.java:0)
22/06/12 22:04:16 INFO DAGScheduler: Parents of final stage: List()
22/06/12 22:04:16 INFO DAGScheduler: Missing parents: List()
22/06/12 22:04:16 INFO DAGScheduler: Submitting ShuffleMapStage 0 (MapPartitionsRDD[8] at count at NativeMethodAccessorImpl.java:0), which has no missing parents
22/06/12 22:04:17 INFO MemoryStore: Block broadcast_1 stored as values in memory (estimated size 25.5 KiB, free 434.2 MiB)
22/06/12 22:04:17 INFO MemoryStore: Block broadcast_1_piece0 stored as bytes in memory (estimated size 11.8 KiB, free 434.1 MiB)
22/06/12 22:04:17 INFO BlockManagerInfo: Added broadcast_1_piece0 in memory on 192.168.0.110:60425 (size: 11.8 KiB, free: 434.4 MiB)
22/06/12 22:04:17 INFO SparkContext: Created broadcast 1 from broadcast at DAGScheduler.scala:1478
22/06/12 22:04:17 INFO DAGScheduler: Submitting 1 missing tasks from ShuffleMapStage 0 (MapPartitionsRDD[8] at count at NativeMethodAccessorImpl.java:0) (first 15 tasks are for partitions Vector(0))
22/06/12 22:04:17 INFO TaskSchedulerImpl: Adding task set 0.0 with 1 tasks resource profile 0
22/06/12 22:04:17 INFO TaskSetManager: Starting task 0.0 in stage 0.0 (TID 0) (192.168.0.110, executor driver, partition 0, PROCESS_LOCAL, 4867 bytes) taskResourceAssignments Map()
22/06/12 22:04:17 INFO Executor: Running task 0.0 in stage 0.0 (TID 0)
22/06/12 22:04:17 INFO FileScanRDD: Reading File path: file:///usr/local/Cellar/apache-spark/3.2.1/README.md, range: 0-4512, partition values: [empty row]
22/06/12 22:04:17 INFO CodeGenerator: Code generated in 16.078328 ms
22/06/12 22:04:17 INFO MemoryStore: Block rdd_3_0 stored as values in memory (estimated size 5.1 KiB, free 434.1 MiB)
22/06/12 22:04:17 INFO BlockManagerInfo: Added rdd_3_0 in memory on 192.168.0.110:60425 (size: 5.1 KiB, free: 434.4 MiB)
22/06/12 22:04:17 INFO CodeGenerator: Code generated in 7.043371 ms
22/06/12 22:04:17 INFO CodeGenerator: Code generated in 87.238152 ms
22/06/12 22:04:17 INFO Executor: Finished task 0.0 in stage 0.0 (TID 0). 2158 bytes result sent to driver
22/06/12 22:04:18 INFO TaskSetManager: Finished task 0.0 in stage 0.0 (TID 0) in 739 ms on 192.168.0.110 (executor driver) (1/1)
22/06/12 22:04:18 INFO TaskSchedulerImpl: Removed TaskSet 0.0, whose tasks have all completed, from pool
22/06/12 22:04:18 INFO DAGScheduler: ShuffleMapStage 0 (count at NativeMethodAccessorImpl.java:0) finished in 1.028 s
22/06/12 22:04:18 INFO DAGScheduler: looking for newly runnable stages
22/06/12 22:04:18 INFO DAGScheduler: running: Set()
22/06/12 22:04:18 INFO DAGScheduler: waiting: Set()
22/06/12 22:04:18 INFO DAGScheduler: failed: Set()
22/06/12 22:04:18 INFO CodeGenerator: Code generated in 20.556764 ms
22/06/12 22:04:18 INFO SparkContext: Starting job: count at NativeMethodAccessorImpl.java:0
22/06/12 22:04:18 INFO DAGScheduler: Got job 1 (count at NativeMethodAccessorImpl.java:0) with 1 output partitions
22/06/12 22:04:18 INFO DAGScheduler: Final stage: ResultStage 2 (count at NativeMethodAccessorImpl.java:0)
22/06/12 22:04:18 INFO DAGScheduler: Parents of final stage: List(ShuffleMapStage 1)
22/06/12 22:04:18 INFO DAGScheduler: Missing parents: List()
22/06/12 22:04:18 INFO DAGScheduler: Submitting ResultStage 2 (MapPartitionsRDD[11] at count at NativeMethodAccessorImpl.java:0), which has no missing parents
22/06/12 22:04:18 INFO MemoryStore: Block broadcast_2 stored as values in memory (estimated size 11.0 KiB, free 434.1 MiB)
22/06/12 22:04:18 INFO MemoryStore: Block broadcast_2_piece0 stored as bytes in memory (estimated size 5.5 KiB, free 434.1 MiB)
22/06/12 22:04:18 INFO BlockManagerInfo: Added broadcast_2_piece0 in memory on 192.168.0.110:60425 (size: 5.5 KiB, free: 434.3 MiB)
22/06/12 22:04:18 INFO SparkContext: Created broadcast 2 from broadcast at DAGScheduler.scala:1478
22/06/12 22:04:18 INFO DAGScheduler: Submitting 1 missing tasks from ResultStage 2 (MapPartitionsRDD[11] at count at NativeMethodAccessorImpl.java:0) (first 15 tasks are for partitions Vector(0))
22/06/12 22:04:18 INFO TaskSchedulerImpl: Adding task set 2.0 with 1 tasks resource profile 0
22/06/12 22:04:18 INFO TaskSetManager: Starting task 0.0 in stage 2.0 (TID 1) (192.168.0.110, executor driver, partition 0, NODE_LOCAL, 4453 bytes) taskResourceAssignments Map()
22/06/12 22:04:18 INFO Executor: Running task 0.0 in stage 2.0 (TID 1)
22/06/12 22:04:18 INFO ShuffleBlockFetcherIterator: Getting 1 (60.0 B) non-empty blocks including 1 (60.0 B) local and 0 (0.0 B) host-local and 0 (0.0 B) push-merged-local and 0 (0.0 B) remote blocks
22/06/12 22:04:18 INFO ShuffleBlockFetcherIterator: Started 0 remote fetches in 48 ms
22/06/12 22:04:18 INFO Executor: Finished task 0.0 in stage 2.0 (TID 1). 2691 bytes result sent to driver
22/06/12 22:04:18 INFO TaskSetManager: Finished task 0.0 in stage 2.0 (TID 1) in 277 ms on 192.168.0.110 (executor driver) (1/1)
22/06/12 22:04:18 INFO TaskSchedulerImpl: Removed TaskSet 2.0, whose tasks have all completed, from pool
22/06/12 22:04:18 INFO DAGScheduler: ResultStage 2 (count at NativeMethodAccessorImpl.java:0) finished in 0.343 s
22/06/12 22:04:18 INFO DAGScheduler: Job 1 is finished. Cancelling potential speculative or zombie tasks for this job
22/06/12 22:04:18 INFO TaskSchedulerImpl: Killing all running tasks in stage 2: Stage finished
22/06/12 22:04:18 INFO DAGScheduler: Job 1 finished: count at NativeMethodAccessorImpl.java:0, took 0.443001 s
22/06/12 22:04:18 INFO DefaultCachedBatchSerializer: Predicate isnotnull(value#0) generates partition filter: ((value.count#68 - value.nullCount#67) &gt; 0)
22/06/12 22:04:18 INFO DAGScheduler: Registering RDD 16 (count at NativeMethodAccessorImpl.java:0) as input to shuffle 1
22/06/12 22:04:18 INFO DAGScheduler: Got map stage job 2 (count at NativeMethodAccessorImpl.java:0) with 1 output partitions
22/06/12 22:04:18 INFO DAGScheduler: Final stage: ShuffleMapStage 3 (count at NativeMethodAccessorImpl.java:0)
22/06/12 22:04:18 INFO DAGScheduler: Parents of final stage: List()
22/06/12 22:04:18 INFO DAGScheduler: Missing parents: List()
22/06/12 22:04:18 INFO DAGScheduler: Submitting ShuffleMapStage 3 (MapPartitionsRDD[16] at count at NativeMethodAccessorImpl.java:0), which has no missing parents
22/06/12 22:04:18 INFO MemoryStore: Block broadcast_3 stored as values in memory (estimated size 25.5 KiB, free 434.1 MiB)
22/06/12 22:04:18 INFO MemoryStore: Block broadcast_3_piece0 stored as bytes in memory (estimated size 11.8 KiB, free 434.1 MiB)
22/06/12 22:04:18 INFO BlockManagerInfo: Added broadcast_3_piece0 in memory on 192.168.0.110:60425 (size: 11.8 KiB, free: 434.3 MiB)
22/06/12 22:04:18 INFO SparkContext: Created broadcast 3 from broadcast at DAGScheduler.scala:1478
22/06/12 22:04:18 INFO DAGScheduler: Submitting 1 missing tasks from ShuffleMapStage 3 (MapPartitionsRDD[16] at count at NativeMethodAccessorImpl.java:0) (first 15 tasks are for partitions Vector(0))
22/06/12 22:04:18 INFO TaskSchedulerImpl: Adding task set 3.0 with 1 tasks resource profile 0
22/06/12 22:04:18 INFO TaskSetManager: Starting task 0.0 in stage 3.0 (TID 2) (192.168.0.110, executor driver, partition 0, PROCESS_LOCAL, 4867 bytes) taskResourceAssignments Map()
22/06/12 22:04:18 INFO Executor: Running task 0.0 in stage 3.0 (TID 2)
22/06/12 22:04:18 INFO BlockManager: Found block rdd_3_0 locally
22/06/12 22:04:18 INFO Executor: Finished task 0.0 in stage 3.0 (TID 2). 2115 bytes result sent to driver
22/06/12 22:04:18 INFO TaskSetManager: Finished task 0.0 in stage 3.0 (TID 2) in 41 ms on 192.168.0.110 (executor driver) (1/1)
22/06/12 22:04:18 INFO TaskSchedulerImpl: Removed TaskSet 3.0, whose tasks have all completed, from pool
22/06/12 22:04:18 INFO DAGScheduler: ShuffleMapStage 3 (count at NativeMethodAccessorImpl.java:0) finished in 0.066 s
22/06/12 22:04:18 INFO DAGScheduler: looking for newly runnable stages
22/06/12 22:04:18 INFO DAGScheduler: running: Set()
22/06/12 22:04:18 INFO DAGScheduler: waiting: Set()
22/06/12 22:04:18 INFO DAGScheduler: failed: Set()
22/06/12 22:04:19 INFO SparkContext: Starting job: count at NativeMethodAccessorImpl.java:0
22/06/12 22:04:19 INFO DAGScheduler: Got job 3 (count at NativeMethodAccessorImpl.java:0) with 1 output partitions
22/06/12 22:04:19 INFO DAGScheduler: Final stage: ResultStage 5 (count at NativeMethodAccessorImpl.java:0)
22/06/12 22:04:19 INFO DAGScheduler: Parents of final stage: List(ShuffleMapStage 4)
22/06/12 22:04:19 INFO DAGScheduler: Missing parents: List()
22/06/12 22:04:19 INFO DAGScheduler: Submitting ResultStage 5 (MapPartitionsRDD[19] at count at NativeMethodAccessorImpl.java:0), which has no missing parents
22/06/12 22:04:19 INFO MemoryStore: Block broadcast_4 stored as values in memory (estimated size 11.0 KiB, free 434.1 MiB)
22/06/12 22:04:19 INFO MemoryStore: Block broadcast_4_piece0 stored as bytes in memory (estimated size 5.5 KiB, free 434.1 MiB)
22/06/12 22:04:19 INFO BlockManagerInfo: Added broadcast_4_piece0 in memory on 192.168.0.110:60425 (size: 5.5 KiB, free: 434.3 MiB)
22/06/12 22:04:19 INFO SparkContext: Created broadcast 4 from broadcast at DAGScheduler.scala:1478
22/06/12 22:04:19 INFO DAGScheduler: Submitting 1 missing tasks from ResultStage 5 (MapPartitionsRDD[19] at count at NativeMethodAccessorImpl.java:0) (first 15 tasks are for partitions Vector(0))
22/06/12 22:04:19 INFO TaskSchedulerImpl: Adding task set 5.0 with 1 tasks resource profile 0
22/06/12 22:04:19 INFO TaskSetManager: Starting task 0.0 in stage 5.0 (TID 3) (192.168.0.110, executor driver, partition 0, NODE_LOCAL, 4453 bytes) taskResourceAssignments Map()
22/06/12 22:04:19 INFO Executor: Running task 0.0 in stage 5.0 (TID 3)
22/06/12 22:04:19 INFO ShuffleBlockFetcherIterator: Getting 1 (60.0 B) non-empty blocks including 1 (60.0 B) local and 0 (0.0 B) host-local and 0 (0.0 B) push-merged-local and 0 (0.0 B) remote blocks
22/06/12 22:04:19 INFO ShuffleBlockFetcherIterator: Started 0 remote fetches in 2 ms
22/06/12 22:04:19 INFO Executor: Finished task 0.0 in stage 5.0 (TID 3). 2648 bytes result sent to driver
22/06/12 22:04:19 INFO TaskSetManager: Finished task 0.0 in stage 5.0 (TID 3) in 19 ms on 192.168.0.110 (executor driver) (1/1)
22/06/12 22:04:19 INFO TaskSchedulerImpl: Removed TaskSet 5.0, whose tasks have all completed, from pool
22/06/12 22:04:19 INFO DAGScheduler: ResultStage 5 (count at NativeMethodAccessorImpl.java:0) finished in 0.046 s
22/06/12 22:04:19 INFO DAGScheduler: Job 3 is finished. Cancelling potential speculative or zombie tasks for this job
22/06/12 22:04:19 INFO TaskSchedulerImpl: Killing all running tasks in stage 5: Stage finished
22/06/12 22:04:19 INFO DAGScheduler: Job 3 finished: count at NativeMethodAccessorImpl.java:0, took 0.063246 s
Lines with a: 65, lines with b: 33
22/06/12 22:04:19 INFO SparkUI: Stopped Spark web UI at http://192.168.0.110:4041
22/06/12 22:04:19 INFO MapOutputTrackerMasterEndpoint: MapOutputTrackerMasterEndpoint stopped!
22/06/12 22:04:19 INFO MemoryStore: MemoryStore cleared
22/06/12 22:04:19 INFO BlockManager: BlockManager stopped
22/06/12 22:04:19 INFO BlockManagerMaster: BlockManagerMaster stopped
22/06/12 22:04:19 INFO OutputCommitCoordinator$OutputCommitCoordinatorEndpoint: OutputCommitCoordinator stopped!
22/06/12 22:04:19 INFO SparkContext: Successfully stopped SparkContext
22/06/12 22:04:19 INFO ShutdownHookManager: Shutdown hook called
22/06/12 22:04:19 INFO ShutdownHookManager: Deleting directory /private/var/folders/6_/yxwcl8kn6dz_fpt7r0z1qd8h0000gn/T/spark-52635c0d-f1a3-4957-bf76-4a989d7ac08b
22/06/12 22:04:19 INFO ShutdownHookManager: Deleting directory /private/var/folders/6_/yxwcl8kn6dz_fpt7r0z1qd8h0000gn/T/spark-a66a2146-3e22-4894-b63f-7631ae8d86b1
22/06/12 22:04:19 INFO ShutdownHookManager: Deleting directory /private/var/folders/6_/yxwcl8kn6dz_fpt7r0z1qd8h0000gn/T/spark-a66a2146-3e22-4894-b63f-7631ae8d86b1/pyspark-f67202d9-534f-4e01-8d2f-8434baa5d3ae</pre>


<h2 id="まとめ">まとめ</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/macOS">macOS</a> で PySpark を動かしてみたくて、Homebrew でインストールして、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C1%A5%E5%A1%BC%A5%C8%A5%EA%A5%A2%A5%EB">チュートリアル</a>の内容をやってみた。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーモードのの仕組みとかもわかっていないとあんまりピンとこないかもしれない。</p>

