---
title: "EMR を触りながら Hadoop 関連の各コンポーネントや構成を調べつつ、全体像がどんなイメージになるのかの自分用メモ"
date: 2022-04-17
slug: "EMR を触りながら Hadoop 関連の各コンポーネントや構成を調べつつ、全体像がどんなイメージになるのかの自分用メモ"
category: blog
tags: []
---
<h2 id="はじめに">はじめに</h2>

<p>EMR を触ることになったけど <a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> のこともそれほどわかっていないのにマネージドサービスのものを触ってもよくわからないので調べた自分用のメモ。</p>

<h2 id="Hadoop-"><a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> ？</h2>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fhadoop.apache.org%2Fdocs%2Fcurrent%2F" title="Hadoop – Apache Hadoop 3.3.3" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://hadoop.apache.org/docs/current/">hadoop.apache.org</a></cite></p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> 自体は、 <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> が開発した <a class="keyword" href="http://d.hatena.ne.jp/keyword/MapReduce">MapReduce</a> と <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> File System(GFS) の技術をもとに<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AA%A1%BC%A5%D7%A5%F3%A5%BD%A1%BC%A5%B9">オープンソース</a>として開発されているもの、という感じで、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a> Software Foundation によって運営されているプロジェクトの1つ。</p>

<blockquote><p>The <a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a>™ <a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a>® project develops open-source software for reliable, scalable, distributed computing.</p>

<p>The <a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> software library is a framework that allows for the distributed processing of large data sets across clusters of computers using simple programming models. It is designed to scale up from single servers to thousands of machines, each offering local computation and storage. Rather than rely on hardware to deliver high-availability, the library itself is designed to detect and handle failures at the application layer, so delivering a highly-available service on top of a cluster of computers, each of which may be prone to failures.</p></blockquote>

<p>何をするものか、というと上記は公式ページの抜粋 <a href="#f-0278cc99" name="fn-0278cc99" title="[https://hadoop.apache.org/docs/current/:title]">*1</a>であるけど、 <a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> が動く<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー全体で大規模なデー<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BF%A5%BB%A5%C3%A5%C8">タセット</a>の分散処理を可能にする<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%EC%A1%BC%A5%E0%A5%EF%A1%BC%A5%AF">フレームワーク</a>。</p>

<p>単一のサーバーから数千台のサーバーまでスケールアップできるように設計されており、それぞれがローカルの計算とストレージを提供する。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> ライブラリ自体は、ハードウェアに依存して高可用性を提供するのではなく、アプリケーションのレイヤで障害を検出して処理するように設計されているため、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%E2%A5%C7%A5%A3%A5%C6%A5%A3">コモディティ</a>なサーバーを束ねた<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー上で高可用性サービスを提供する。</p>

<h2 id="Hadoop-構成要素"><a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> 構成要素</h2>

<p>調べると色々出てきてよくわからなくなるが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Wikipedia">Wikipedia</a>によると大まかには以下の4つの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%DD%A1%BC%A5%CD%A5%F3%A5%C8">コンポーネント</a>からなるらしい。<a href="#f-499e5e3b" name="fn-499e5e3b" title="[https://ja.wikipedia.org/wiki/Apache_Hadoop#%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%81%E3%83%A3]">*2</a></p>

<ul>
<li><a class="keyword" href="http://d.hatena.ne.jp/keyword/HDFS">HDFS</a></li>
<li>YARN</li>
<li><a class="keyword" href="http://d.hatena.ne.jp/keyword/MapReduce">MapReduce</a></li>
<li><a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> Common</li>
</ul>


<h3 id="HDFSHadoop-Distributed-File-System"><a class="keyword" href="http://d.hatena.ne.jp/keyword/HDFS">HDFS</a>(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> Distributed File System)</h3>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/HDFS">HDFS</a> は <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> File System(GFS) に相当する <a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> で利用される<a class="keyword" href="http://d.hatena.ne.jp/keyword/%CA%AC%BB%B6%A5%D5%A5%A1%A5%A4%A5%EB%A5%B7%A5%B9%A5%C6%A5%E0">分散ファイルシステム</a>。NameNode(マスター)とDataNode(スレーブ)で構成される。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> 全体としては、NameNodeはマスターノードに、DataNodeはスレーブノードの中で構成される。</p>

<h4 id="HDFS-でのデータのアクセス"><a class="keyword" href="http://d.hatena.ne.jp/keyword/HDFS">HDFS</a> でのデータのアクセス</h4>

<p>NameNodeはメタ情報を管理し、DataNodeはデータの実体を保存する。
NameNodeがダウンすると<a class="keyword" href="http://d.hatena.ne.jp/keyword/HDFS">HDFS</a>自体が停止してしまう。NameNode自体の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%BE%E9%C4%B9%B2%BD">冗長化</a>しようとすると、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a> ZooKeeperが利用したAct/<a class="keyword" href="http://d.hatena.ne.jp/keyword/Stb">Stb</a>構成をとる必要がある。ZooKeeper についてはここでは記載しない。</p>

<p>データを保存する際、<code>dfs.block.size</code> で設定されたサイズに分割されたデータを1つのブロックとしてDataNodeへ保存する。EMR 5.33 系だと<code>dfs.block.size</code>のデフォルト128MB。
また、<code>dfs.replication</code> で設定された値分、他のDataNodeにデータのコピーが保存される。これによって特定のDataNode障害時のデータロストをできるだけ防ぐ。EMR 5.33 系だとデフォルトは 3。この場合3つ同じデータを持つことになるため、3台のDataNodeで100GBのストレージを持っていても、使える容量は300GBではなく実質100GB程度になる。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/HDFS">HDFS</a>上のデータにアクセスする場合は、ブロックの配置先等の情報をNameNodeに問い合わせ、そこから取得した情報を用いてDataNodeにアクセスしてブロックを取得する。</p>

<h3 id="YARNYet-Another-Resource-Negotiator">YARN(Yet Another Resource Negotiator)</h3>

<p>YARNはResourceManager(マスター)、NodeManager(スレーブ)、ApplicationMasterから構成される。</p>

<h4 id="ResourceManager">ResourceManager</h4>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー全体のリソース管理を行うプロセス。<a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a>ではマスターノードで実行される。
ResourceManagerにはSchedulerとApplicationsManagerの2つの主要<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%DD%A1%BC%A5%CD%A5%F3%A5%C8">コンポーネント</a>がある。</p>

<p>Schedulerは文字通りスケジューリングやハードウェアリソースの割当を担い、ApplicationsManagerはジョブの送信を受け入れ、ApplicationMasterへのコンテナの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%CD%A5%B4%A5%B7%A5%A8%A1%BC%A5%C8">ネゴシエート</a>、失敗時にApplicationMasterコンテナーを再始動などを行う。ここでのコンテナとは、特定のノードで特定の量のリソース（メモリ、CPUなど）を使用する権限をアプリケーションに付与する仕組みのこと。</p>

<h4 id="NodeManager">NodeManager</h4>

<p>実際にアプリケーション(ジョブ)を実行するプロセス。<a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a>ではスレーブノードで実行される。
コンテナのリソース使用量（CPU、メ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E2%A5%EA%A1%BC">モリー</a>、ディスク、ネットワーク）を監視し、それをApplicationMasterと連携する役目も担う。</p>

<h4 id="Application-Master">Application Master</h4>

<p>ResourceManager(ApplicationsManager/Scheduler) と適切なリソースコンテナを<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%CD%A5%B4%A5%B7%A5%A8%A1%BC%A5%C8">ネゴシエート</a>すると言ったやり取りをし、NodeManager とアプリケーションのステータスを追跡し、進行状況を監視する、といったやり取りをする。
実行するアプリケーションごとに1つ、スレーブノードで実行される。</p>

<h3 id="MapReduce"><a class="keyword" href="http://d.hatena.ne.jp/keyword/MapReduce">MapReduce</a></h3>

<p>論文によると以下のように大規模なデー<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BF%A5%BB%A5%C3%A5%C8">タセット</a>を処理、または生成するためのプログラミングモデル、関連する実装、とのこと。<a href="#f-e686ee02" name="fn-e686ee02" title="[https://static.googleusercontent.com/media/research.google.com/ja//archive/mapreduce-osdi04.pdf:title]">*3</a></p>

<p>あんまりちゃんとわかってないが、Map処理でデータをKey, <a class="keyword" href="http://d.hatena.ne.jp/keyword/Value">Value</a>形式に分割し、Reduce処理でそのデータを集約する、と言った感じの動きするものらしい。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> での <a class="keyword" href="http://d.hatena.ne.jp/keyword/MapReduce">MapReduce</a> は YARN の仕組みの上で動く模様。 <a href="#f-3ce11667" name="fn-3ce11667" title="[https://hadoop.apache.org/docs/stable/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html]">*4</a></p>

<h3 id="Hadoop-Common"><a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a> Common</h3>

<p>共通して利用されるライブラリ群。</p>

<p>多分この辺りのものを指しているのではないかと思われるけど具体的にはわかっていない。</p>

<ul>
<li><a href="https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-common">https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-common</a></li>
</ul>


<h3 id="EMR-概要図">EMR 概要図</h3>

<p>多分こんな感じだと思っている。誤りに気づいたら都度アップデートしていく。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220726/20220726130309.png" width="701" height="551" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://oss.nttdata.com/hadoop/hadoop.html">&#x5206;&#x6563;&#x51E6;&#x7406;&#x6280;&#x8853;&#x300C;Hadoop&#x300D;&#x3068;&#x306F;&#xFF1A;NTT&#x30C7;&#x30FC;&#x30BF;&#x306E;Hadoop&#x30BD;&#x30EA;&#x30E5;&#x30FC;&#x30B7;&#x30E7;&#x30F3;</a></li>
<li><a href="https://gihyo.jp/admin/serial/01/how_hadoop_works/0014">&#x7B2C;14&#x56DE; Hadoop&#x306E;&#x8A2D;&#x8A08;&#x3068;&#x5B9F;&#x88C5;&#xFF5E;&#x4E26;&#x5217;&#x30C7;&#x30FC;&#x30BF;&#x51E6;&#x7406;&#x30D5;&#x30EC;&#x30FC;&#x30E0;&#x30EF;&#x30FC;&#x30AF;Hadoop MapReduce&#xFF3B;2&#xFF3D; | gihyo.jp</a></li>
<li><a href="https://www.sraoss.co.jp/wp-content/uploads/files/event_seminar/material/2012/20120316_Hadoop_OSC2012TokyoSpring_sraoss.pdf">https://www.sraoss.co.jp/wp-content/uploads/files/event_seminar/material/2012/20120316_Hadoop_OSC2012TokyoSpring_sraoss.pdf</a></li>
<li><a href="https://www.slideshare.net/hideaki_honda/hadoop-30143627">Hadoop &#x57FA;&#x790E;</a></li>
<li><a href="https://cloud.google.com/learn/what-is-hadoop?hl=ja">Hadoop &#x3068;&#x306F; &nbsp;|&nbsp; Google Cloud</a></li>
<li><a href="https://qiita.com/keigodasu/items/09f7e0a15d721b0b5212">&#x3042;&#x306E;&#x65E5;&#x898B;&#x305F;YARN&#x306E;&#x304A;&#x4ED5;&#x4E8B;&#x3092;&#x50D5;&#x9054;&#x306F;&#x307E;&#x3060;&#x77E5;&#x3089;&#x306A;&#x3044;&#x3002; - Qiita</a></li>
<li><a href="https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop 3.3.3 &ndash; Apache Hadoop YARN</a></li>
<li><a href="https://blog.cloudera.com/apache-hadoop-yarn-concepts-and-applications/">Apache Hadoop YARN &ndash; Concepts and Applications - Cloudera Blog</a></li>
<li><a href="https://www.mhlw.go.jp/content/11800000/000754300.pdf">https://www.mhlw.go.jp/content/11800000/000754300.pdf</a></li>
</ul>

<div class="footnote">
<p class="footnote"><a href="#fn-0278cc99" name="f-0278cc99" class="footnote-number">*1</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://hadoop.apache.org/docs/current/">Hadoop &ndash; Apache Hadoop 3.3.3</a></span></p>
<p class="footnote"><a href="#fn-499e5e3b" name="f-499e5e3b" class="footnote-number">*2</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://ja.wikipedia.org/wiki/Apache_Hadoop#%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%81%E3%83%A3">https://ja.wikipedia.org/wiki/Apache_Hadoop#%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%81%E3%83%A3</a></span></p>
<p class="footnote"><a href="#fn-e686ee02" name="f-e686ee02" class="footnote-number">*3</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://static.googleusercontent.com/media/research.google.com/ja//archive/mapreduce-osdi04.pdf">https://static.googleusercontent.com/media/research.google.com/ja//archive/mapreduce-osdi04.pdf</a></span></p>
<p class="footnote"><a href="#fn-3ce11667" name="f-3ce11667" class="footnote-number">*4</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://hadoop.apache.org/docs/stable/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html">https://hadoop.apache.org/docs/stable/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html</a></span></p>
</div>
