---
title: "Amazon Linux 2023 で MySQL 公式クライアントを使おうと思うならどうするのが良いかと思ってちょろっと調べたことの雑多なメモ"
date: 2023-07-20
slug: "Amazon Linux 2023 で MySQL 公式クライアントを使おうと思うならどうするのが良いかと思ってちょろっと調べたことの雑多なメモ"
category: blog
tags: [Amazon Linux 2023,MySQL]
---
<h2 id="はじめに">はじめに</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/Linux">Linux</a> 2023 で動いている EC2 から RDS <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> や Aurora <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> に接続したいことが結構あります(Fargate の踏み台使えとかはなし)。</p>

<p>しかし、<a class="keyword" href="https://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/Linux">Linux</a> 2023 の公式 <a class="keyword" href="https://d.hatena.ne.jp/keyword/yum">yum</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>にも <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> クライアントのパッケージはなさそうでした。</p>

<p>実際どうするのが一番良さそうなのか気になりつつ、2023/7時点での対応状況などなどを軽く調べてみました。</p>

<h2 id="AWSのドキュメント"><a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a>のドキュメント</h2>

<p>RDS <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> DB<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>の接続には <a class="keyword" href="https://d.hatena.ne.jp/keyword/MariaDB">MariaDB</a> クライアントを使うように促されてます。</p>

<ul>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToInstance.html">Connecting to a DB instance running the MySQL database engine - Amazon Relational Database Service</a></li>
</ul>


<p>Aurora <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> への接続は、Aurora <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> のバージョンと互換性のあるバージョンの<a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a>クライアントを使え、ということみたいです。</p>

<ul>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Connecting.html#Aurora.Connecting.AuroraMySQL">Connecting to an Amazon Aurora DB cluster - Amazon Aurora</a></li>
</ul>


<p>一方でアプリケーションで使うドライバは以下を使うのがよさそうに思われました。フェイルオーバー時の挙動に対応してそうに思われました。
* <a href="https://github.com/awslabs/aws-mysql-jdbc">GitHub - awslabs/aws-mysql-jdbc: The Amazon Web Services (AWS) JDBC Driver for MySQL is a driver that enables applications to take full advantage of the features of clustered MySQL databases.</a>
* <a href="https://awslabs.github.io/aws-mysql-jdbc/">Amazon Web Services (AWS) JDBC Driver for MySQL</a></p>

<h2 id="MySQL側"><a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a>側</h2>

<p>一方で、<a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> 側では、<a class="keyword" href="https://d.hatena.ne.jp/keyword/RHEL">RHEL</a>/<a class="keyword" href="https://d.hatena.ne.jp/keyword/Oracle">Oracle</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/Linux">Linux</a>/<a class="keyword" href="https://d.hatena.ne.jp/keyword/Fedora">Fedora</a> 向けの <a class="keyword" href="https://d.hatena.ne.jp/keyword/yum">yum</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>は提供されています。
これらを使うことでインストールして使えそうですが(試してない)、厳密には <a class="keyword" href="https://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/Linux">Linux</a> 2023 のものではありません。</p>

<ul>
<li><a href="https://dev.mysql.com/downloads/repo/yum/">MySQL :: Download MySQL Yum Repository</a></li>
</ul>


<p>ソースビルドも考えましたが、今のところ <a class="keyword" href="https://d.hatena.ne.jp/keyword/glibc">glibc</a> で合っているものがなさそうでした。</p>

<ul>
<li><a href="https://dev.mysql.com/downloads/mysql/">MySQL :: Download MySQL Community Server</a></li>
</ul>


<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/Linux">Linux</a> 2023 で公式の<a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a>クライアントを使おうと思うと、以下のような感じで Docker イメージを使うのが楽そうに思いました。</p>

<pre class="code terminal" data-lang="terminal" data-unlink>% docker run --rm -it mysql:8.0.33 mysql -h db-hostname -u db-user -p</pre>


<h2 id="MariaDB-と-MySQL"><a class="keyword" href="https://d.hatena.ne.jp/keyword/MariaDB">MariaDB</a> と <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a></h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/MariaDB">MariaDB</a> と <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a>の対応ってどんな感じかあまり分かってなかったのでこれも少し見てみました。</p>

<p>サーバー側は <a class="keyword" href="https://d.hatena.ne.jp/keyword/MariaDB">MariaDB</a> 10.3 あたりから微妙に非互換が出てきているようです。</p>

<ul>
<li><a href="https://mariadb.com/kb/en/mariadb-vs-mysql-compatibility/#incompatibilities-between-currently-maintained-mariadb-versions-and-mysql">MariaDB versus MySQL - Compatibility - MariaDB Knowledge Base</a></li>
</ul>


<p>一部コ<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%CD%A5%AF%A5%BF%A1%BC">ネクター</a>では問題があったりするようでした。</p>

<ul>
<li><a href="https://mariadb.com/kb/en/maria-db-driver-with-aws-aurora/">Maria DB Driver with AWS Aurora - MariaDB Knowledge Base</a></li>
</ul>


<p>でも<a class="keyword" href="https://d.hatena.ne.jp/keyword/MariaDB">MariaDB</a> クライアントを利用して接続すること自体は通常は大丈夫そうな感じはします。</p>

<h2 id="まとめ">まとめ</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/Linux">Linux</a> 2023 から、通常のちょっとしたオペレーションで Aurora <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> へ接続する分には <a class="keyword" href="https://d.hatena.ne.jp/keyword/MariaDB">MariaDB</a> クライアントでもよさそうに思いました。
<a class="keyword" href="https://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/Linux">Linux</a> 2023 で公式の<a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a>クライアントを使おうと思うと、Dockerイメージを使うのが楽そうに思いました。
アプリケーションで利用する場合は、<a class="keyword" href="https://d.hatena.ne.jp/keyword/aws">aws</a>-<a class="keyword" href="https://d.hatena.ne.jp/keyword/mysql">mysql</a>-<a class="keyword" href="https://d.hatena.ne.jp/keyword/jdbc">jdbc</a>を使うのが良さそうに思いました。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToInstance.html">Connecting to a DB instance running the MySQL database engine - Amazon Relational Database Service</a></li>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Connecting.html#Aurora.Connecting.AuroraMySQL">Connecting to an Amazon Aurora DB cluster - Amazon Aurora</a></li>
<li><a href="https://github.com/awslabs/aws-mysql-jdbc">GitHub - awslabs/aws-mysql-jdbc: The Amazon Web Services (AWS) JDBC Driver for MySQL is a driver that enables applications to take full advantage of the features of clustered MySQL databases.</a></li>
<li><a href="https://awslabs.github.io/aws-mysql-jdbc/">Amazon Web Services (AWS) JDBC Driver for MySQL</a></li>
<li><a href="https://mariadb.com/kb/en/maria-db-driver-with-aws-aurora/">Maria DB Driver with AWS Aurora - MariaDB Knowledge Base</a></li>
</ul>


