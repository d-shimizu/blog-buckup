---
title: "Kafka の管理コマンドで Topic を作成する処理がどうなっているか調べた"
date: 2022-05-14
slug: "Kafka の管理コマンドで Topic を作成する処理がどうなっているか調べた"
category: blog
tags: [Tech]
---
<h2 id="はじめに">はじめに</h2>

<p>Kafka には多数の管理コマンドの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%A7%A5%EB%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">シェルスクリプト</a>が用意されている。
この管理コマンドの中の <code>kafka-topic.sh</code> で Topic を作成するときの動きを調べた。</p>

<h2 id="環境">環境</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Managed Kafka の推奨バージョンが v2.6.2 となっていたので、 v2.6 のもので調べた。</p>

<h2 id="kafka-topicsh"><code>kafka-topic.sh</code></h2>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Fapache%2Fkafka%2Fblob%2F2.6%2Fbin%2Fkafka-topics.sh" title="kafka/kafka-topics.sh at 2.6 · apache/kafka" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://github.com/apache/kafka/blob/2.6/bin/kafka-topics.sh">github.com</a></cite></p>

<p>コメントは書かれているが、中身は実質以下のみ。</p>

<pre class="code shell" data-lang="shell" data-unlink>exec $(dirname $0)/kafka-run-class.sh kafka.admin.TopicCommand &#34;$@&#34;</pre>


<p><code>kafka-topic.sh</code> は Topic の作成以外にも色々なことに使うことができるけど、<code>kafka-run-class.sh</code> から、オプションを渡しつつ <a class="keyword" href="http://d.hatena.ne.jp/keyword/Scala">Scala</a> のコードを呼び出す形になっている模様。</p>

<h2 id="kafka-run-classsh"><code>kafka-run-class.sh</code></h2>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Fapache%2Fkafka%2Fblob%2F2.6%2Fbin%2Fkafka-run-class.sh" title="kafka/kafka-run-class.sh at 2.6 · apache/kafka" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://github.com/apache/kafka/blob/2.6/bin/kafka-run-class.sh">github.com</a></cite></p>

<p><code>kafka-run-class.sh</code> は jar ファイルなどの読み込みを行なっている模様。これを動かして必要な環境をセットしつつ本体のコードを実行していると思う。</p>

<h2 id="kafkaadminTopicCommand"><code>kafka.admin.TopicCommand</code></h2>

<p>呼び出されるのは以下のコードと思われる。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Fapache%2Fkafka%2Fblob%2F2.6%2Fcore%2Fsrc%2Fmain%2Fscala%2Fkafka%2Fadmin%2FTopicCommand.scala" title="kafka/TopicCommand.scala at 2.6 · apache/kafka" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://github.com/apache/kafka/blob/2.6/core/src/main/scala/kafka/admin/TopicCommand.scala">github.com</a></cite></p>

<p>TopicCommand オブジェクトで、最初に <code>TopicCommandOptions()</code> のクラスが呼び出され、<code>kafka-topic.sh</code>  に指定したオプションの解析っぽいことが行われている。</p>

<ul>
<li><a href="https://github.com/apache/kafka/blob/2.6/core/src/main/scala/kafka/admin/TopicCommand.scala#L53">kafka/TopicCommand.scala at 2.6 &middot; apache/kafka &middot; GitHub</a></li>
</ul>


<pre class="code lang-scala" data-lang="scala" data-unlink>    <span class="synType">val</span> opts = <span class="synStatement">new</span> TopicCommandOptions(args)
</pre>


<p>その後、以下の部分で、 Zookeeper に接続するかどうかによって実行する処理が変わっている。
これは、Kafka に関する処理は、Zookeeper に接続するか、Broker (bootstrap-server) に接続するかに分かれているためかと思う。
トピック作成時は Broker に接続する形になるので、<code>AdminClientTopicService()</code>が呼び出されている。</p>

<ul>
<li><a href="https://github.com/apache/kafka/blob/2.6/core/src/main/scala/kafka/admin/TopicCommand.scala#L56-L59">kafka/TopicCommand.scala at 2.6 &middot; apache/kafka &middot; GitHub</a></li>
</ul>


<pre class="code lang-scala" data-lang="scala" data-unlink>    <span class="synType">val</span> topicService = <span class="synStatement">if</span> (opts.zkConnect.isDefined)
      ZookeeperTopicService(opts.zkConnect)
    <span class="synStatement">else</span>
      AdminClientTopicService(opts.commandConfig, opts.bootstrapServer)
</pre>


<p><code>AdminClientTopicService()</code> オブジェクトの apply メソッドが呼び出され、その後 <code>createAdminClient</code> メソッドが呼び出され、そこで create が実行されている感じのようだった。</p>

<ul>
<li><a href="https://github.com/apache/kafka/blob/2.6/core/src/main/scala/kafka/admin/TopicCommand.scala#L223-L229">kafka/TopicCommand.scala at 2.6 &middot; apache/kafka &middot; GitHub</a></li>
</ul>


<pre class="code lang-scala" data-lang="scala" data-unlink><span class="synType">  object</span> AdminClientTopicService {
<span class="synIdentifier">    def</span> createAdminClient(commandConfig: Properties, bootstrapServer: Option[<span class="synConstant">String</span>]): Admin = {
      bootstrapServer match {
        <span class="synType">case</span> Some(serverList) =&gt; commandConfig.put(CommonClientConfigs.BOOTSTRAP_SERVERS_CONFIG, serverList)
        <span class="synType">case</span> None =&gt;
      }
      Admin.create(commandConfig)
    }

<span class="synIdentifier">    def</span> apply(commandConfig: Properties, bootstrapServer: Option[<span class="synConstant">String</span>]): AdminClientTopicService =
      <span class="synStatement">new</span> AdminClientTopicService(createAdminClient(commandConfig, bootstrapServer))
  }
</pre>


<h2 id="まとめ">まとめ</h2>

<p><code>kafka-topics.sh</code> で実行できる機能の中の、トピックの作成部分のコードを読んでみた。
<code>kafka-topics.sh</code> にいろんな機能をまとめる形になっているのか、ちょっと気になった。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://github.com/apache/kafka/blob/2.6/core/src/main/scala/kafka/admin/TopicCommand.scala">kafka/TopicCommand.scala at 2.6 &middot; apache/kafka &middot; GitHub</a></li>
<li><a href="https://sugarnaoming.github.io/scala_newbie/chapter120.html">object&#x3068;case class &middot; GitBook</a></li>
<li><a href="https://i-beam.org/2017/12/06/kafka-topics/">Kafka&#x306E;&#x7BA1;&#x7406;&#x30C4;&#x30FC;&#x30EB;&#x3092;&#x8AAD;&#x3080; - kafka-topics.sh&#x7DE8; - Folioscope</a></li>
<li><a href="https://udomomo.hatenablog.com/entry/2020/02/02/234913">&#x3010;Kafka&#x3011;--bootstrap-server&#x3068;--zookeeper&#x306E;&#x9055;&#x3044; - &#x308A;&#x3093;&#x3054;&#x3068;&#x30D0;&#x30CA;&#x30CA;&#x3068;&#x30A8;&#x30F3;&#x30B8;&#x30CB;&#x30A2;</a></li>
</ul>


