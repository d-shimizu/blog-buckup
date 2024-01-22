---
layout: post
Categories:  ["tech"]
Description:  " はじめに  PostgreSQLのベンチマークを行う際、pgbenchというツールはPostgreSQLに付属していることもあり、よく用いられると思います。      pgbench — PostgreSQL 9.2.X文書    pgb"
Tags: ["PostgreSQL", "ベンチマーク"]
date: "2013-03-11T00:51:00+09:00"
title: "PostgreSQLのベンチマークツール TPCC-UVa を試す"
author: "dshimizu"
archive: ["2013"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>を行う際、pgbenchというツールは<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>に付属していることもあり、よく用いられると思います。 </p>
<ul>  <li><a href="http://www.postgresql.jp/document/current/html/pgbench.html">pgbench — PostgreSQL 9.2.X文書</a></li>  <li><a href="http://lets.postgresql.jp/documents/technical/contrib/pgbench/">pgbenchの使いこなし — Let's Postgres</a></li>
</ul>
<a name="more"></a><p>pgbenchは<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-B(銀行の窓口業務をモデルにした単純な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>による性能評価基準)をベースとした<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>で、もう少し高度な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>を実施してみようと思い、<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-Cベースの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>が実行できるTPCC-UVaというツールを利用してみました。 </p>
<ul>  <li><a href="http://www.infor.uva.es/~diego/tpcc-uva.html">TPCC-UVa: A free, open-source implementation of the TPC-C Benchmark — Diego R. Llanos</a></li>
</ul>
<p>開発が終わっているのか2006年を最後にリリースが止まっていますが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a> 9.2に対しても実行できましたので、その手順等をまとめておきます。 </p>  <h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>基準</h4>
<h5>概要</h5>
<p>一般的にデータベースの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>には「<a href="http://www.tpc.org/default.asp">TPC（Transaction Processing Performance Council)</a>」という団体が定義している「<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-A」「<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-B」「<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-C」「<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-D」「<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-H」「<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-R」「<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-W」という性能評価基準があります。このうち、<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-A、B、D、R、Wは既に廃止(observe)となっていることもあり利用が少なく、現在では<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-Cや<a class="keyword" href="http://d.hatena.ne.jp/keyword/TCP">TCP</a>-E、<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-Hあたりがよく利用されるものと思います。<br><a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-C,E,Hの概要については<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>のサイトを参照していただければと思いますが、極簡単に記載しますと、 </p>
<ul>  <li><a href="http://www.tpc.org/tpcc/default.asp">TPC-C</a></li>卸売り会社の業務をモデルとした<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>処理をシミュレートして性能を評価する<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>   <li><a href="http://www.tpc.org/tpce/default.asp">TPC-E</a></li>証券会社の株取引業務をモデルとした<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>処理をシミュレートして性能を評価する<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>   <li><a href="http://www.tpc.org/tpch/default.asp">TPC-H</a></li>大型基幹業務向け意思決定支援環境をシミュレートして性能を評価する<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a> </ul>
<p>となります。 </p>
<p>特に<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-Eはかなり複雑なモデルですがテストデータも実際のデータが用いられているようで、その分より正確な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>結果が得られると考えられますが、これらの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>テストはそれぞれCPUやディスクのどちらにより負荷がかかるかなどが異なりますので、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>の仕様を把握して、実際に利用するシステムの目的に即したものを選択するのが良いと思います。 </p>
<p> </p>
<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-Cの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>ツール TPCC-UVa</h5>
<p>公式サイトに記載がありますが、TPCC-UVaは<a class="keyword" href="http://d.hatena.ne.jp/keyword/Linux">Linux</a>環境で実行できる<a class="keyword" href="http://d.hatena.ne.jp/keyword/C%B8%C0%B8%EC">C言語</a>で書かれた<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>用の<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-Cベースの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>ツールです。Diego R. Llanos氏、Eduardo Hernández Perdiguero氏、Julio Alberto Hernández Gonzalo氏らがスペインの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%EA%A5%E3%A5%C9%A5%EA%A1%BC%A5%C9">バリャドリード</a>大学の卒業研究として開発されたもののようです。 </p>
<pre class="file">  Although the TPC-C specifications are public, there was not a public implementation: each company develops and uses its own. We have developed an open-source version of the TPC-C benchmark, following the terms of the GPL license. This version, called TPCC-UVa, is written entirely in C language and can be run in any Linux system. It uses the PostgreSQL database system and a simple transaction monitor. This benchmark can be used to measure the speed of different computers or to analyze the behavior of individual components, either hardware or software, and its impact on the overall performance of the system. The development was started by Eduardo Hernández Perdiguero and Julio Alberto Hernández Gonzalo as their Bachelor's Thesis in the Escuela Universitaria Politécnica, University of Valladolid, with the advise of Diego R. Llanos.   
</pre>
<p>また、これは<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>向けの<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-Cのシンプルな実装であり、他の<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-Cによる結果とは比較できないとのことです。 </p>
<pre class="file">  Note: To release TPCC-UVa as an open-source program, we use our own version of a very simple Transaction Monitor (TM), instead of using any commercial TM. Because of that, the results obtained with TPCC-UVa, particularly our performance parameter "TPCC-UVa Transactions per minute (tpmC-uva)" should not be compared  with values of tpmC obtained with other implementations.  </pre> <h4>実行環境情報</h4>
<p>インストールや<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>を実行する環境を以下に示します。 </p> <h5>HW環境</h5>
<p>HW環境は以下の通りです。 </p>
<table>  <tr>    <td>プラットフォーム</td>    <td>さくらの<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPS">VPS</a> 512</td>  </tr>  <tr>    <td>OS</td>    <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/CentOS">CentOS</a> 6.3</td>  </tr>  <tr>    <td>CPU</td>    <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Intel">Intel</a>(R) Core(TM)2 Duo CPU T7700 @ 2.40GHz</td>  </tr>  <tr>    <td>Memory</td>    <td>1GB</td>  </tr>  <tr>    <td>HDD</td>    <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/QEMU">QEMU</a> Virtual HARDDISK (Sector(Block) Size 512B)</td>  </tr>
</table> <h5>インストール<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ</h5>
<p>取得したTPCC-UVaの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AB%A5%A4%A5%D6">アーカイブ</a>の中にTPCC-UVaの他に<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a> 8.1.4, GunPlotが含まれていますので、それらを全てインストールすることとして、インストール先<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ/作業<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リをそれぞれ以下とします。<br>※TPCC-UVaの利用には<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a> 8.1.4(の中に含まれているlibecpg.so.5.1というライブラリ)が必要になります。 </p>
<table>  <tr>    <td>作業用<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ</td>    <td>/tmp</td>  </tr>  <tr>    <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a> 8.1.4インストール先<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ</td>    <td>/home/tpcc-uva/pgsql</td>  </tr>  <tr>    <td>Gunplotインストール先<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ</td>    <td>/home/tpcc-uva/bin</td>  </tr>  <tr>    <td>TPCC-UVaインストール先<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ</td>    <td>/home/tpcc-uva/bin</td>  </tr>
</table> <h4>インストール事前準備</h4>
<p>作業は全てrootで実行しています。 </p>
<pre class="terminal">  $ su - パスワード:  </pre> <h5>必要なパッケージのインストール</h5>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a> 8.1.4のインストール時に下記のパッケージが必要になりますので、存在しない場合はインストールを行います。 </p>
<pre class="terminal">  # yum install zlib readline gcc  </pre> <h5>インストール先<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リの作成</h5>
<p>インストール先<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成します。 </p>
<pre class="terminal">  # mkdir /home/tpccuva  </pre> <h5>TPCC-UVaの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BD%A1%BC%A5%B9%A5%B3%A1%BC%A5%C9">ソースコード</a>の取得</h5>
<p>/tmp配下にTPCC-UVaの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BD%A1%BC%A5%B9%A5%B3%A1%BC%A5%C9">ソースコード</a>を取得して展開します。 </p>
<pre class="terminal">  # cd /tmp  
</pre>
<pre class="terminal">  # wget http://www.infor.uva.es/~diego/tpccuva/tpccuva-1.2.3-tarball.tar.gz  
</pre>
<pre class="terminal">  # tar -zxvf tpccuva-1.2.3-tarball.tar.gz  
</pre>
<p>展開したファイルの中にtpccuva-guide.pdfというPDFがあります。 基本的にここに記載されている手順に沿ってインストールを行います。 </p> <h4>インストール</h4>
<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a> 8.1.4のインストール</h5>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a> 8.1.4の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AB%A5%A4%A5%D6">アーカイブ</a>ファイルを展開します。 </p>
<pre class="terminal">  # cd /tmp/tpccuva-1.2.3-tarball  
</pre>
<pre class="terminal">  # bzip2 -dc postgresql-base-8.1.4.tar.bz2 | tar xvf -  
</pre>
<p>展開して作成された<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リに移動し、makeファイルを作成します。--prefixでインストール先を指定します。 </p>
<pre class="terminal">  # cd /tmp/tpccuva-1.2.3-tarball/postgresql-base-8.1.4  
</pre>
<pre class="terminal">  # ./configure --prefix=/home/tpcc-uva/pgsql  
</pre>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>、およびインストールを行います。 </p>
<pre class="terminal">  # gmake &amp;&amp; gmake install  </pre> <h5>gunplot 3.7.1のインストール</h5>
<p>続いて付属されているgunplotのインストールを行います。 </p>
<pre class="terminal">  # cd /tmp/tpccuva-1.2.3-tarball  
</pre>
<pre class="terminal">  # tar -zxvf gnuplot-3.7.1.tar.gz  
</pre>
<pre class="terminal">  # cd /tmp/tpccuva-1.2.3-tarball/gnuplot-3.7.1  
</pre>
<pre class="terminal">  # ./configure --prefix=/home/tpcc-uva/bin --without-x  
</pre>
<pre class="terminal">  # make &amp;&amp; make install  </pre> <h5>TPCC-UVaのインストール</h5>
<p>ログ生成先となる<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成します。 </p>
<pre class="terminal">  # mkdir -p /home/tpcc-uva/var/tpcc  
</pre>
<p>TPCC-UVaのファイルを展開します。 </p>
<pre class="terminal">  # cd /tmp/tpccuva-1.2.3-tarball  
</pre>
<pre class="terminal">  # tar -zxvf tpccuva-1.2.3.tar.gz  
</pre>
<p>展開して作成されたファイルに移動し、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Makefile">Makefile</a>を環境に合わせて修正します。<a class="keyword" href="http://d.hatena.ne.jp/keyword/Makefile">Makefile</a>はバックアップを取得しておきます。 </p>
<pre class="terminal">  # cd /tmp/tpccuva-1.2.3-tarball/tpccuva-1.2.3  
</pre>
<pre class="terminal">  # cp -p Makefile Makefile.backup  
</pre>
<pre class="terminal">  # vim Makefile === 修正内容 ===  # VARDIR stores where the auxiliary files will be stored during the  # benchmark execution. Do not forget the last slash! -VARDIR = $(HOME)/tpcc-uva/var/tpcc/ +#VARDIR = $(HOME)/tpcc-uva/var/tpcc/ +VARDIR = /home/pgsql/tpcc-uva/var/tpcc/   # EXECDIR stores where the executable files will be stored during the  # installation. Do not forget the last slash! -EXECDIR = $(HOME)/tpcc-uva/bin/ +#EXECDIR = $(HOME)/tpcc-uva/bin/ +EXECDIR = /home/pgsql/tpcc-uva/bin/   # INCLUDEPATH stores the location of PostgreSQL include files. -export INCLUDEPATH = $(HOME)/tpcc-uva/pgsql/include/ +#export INCLUDEPATH = $(HOME)/tpcc-uva/pgsql/include/ +export INCLUDEPATH = /home/pgsql/tpcc-uva/pgsql/include/   # LIBSPATH stores the location of PostgreSQL library files. -export LIBSPATH = $(HOME)/tpcc-uva/pgsql/lib/ +#export LIBSPATH = $(HOME)/tpcc-uva/pgsql/lib/ +export LIBSPATH = /home/pgsql/tpcc-uva/pgsql/lib/  
</pre>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>を設定します。 これは各種プログラムを実行する上で必要ですので、必要であれば実行ユーザの.bashrcなどに記載しておくと良いと思います。 </p>
<pre class="terminal">  # export PATH=$PATH:/home/tpcc-uva/pgsql/bin:/home/tpcc-uva/bin # export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/tpcc-uva/pgsql/lib  
</pre>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>して、インストールを実施します。 </p>
<pre class="terminal">  # make &amp;&amp; make install  
</pre>
<p>以上で、インストール作業が完了しました。 </p> <h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>の実行</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>を実行してみます。<br><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>対象は<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>サーバは9.2.2で、あらかじめインストールされているものとします。特にパラメータは変更しておらず、インストール時のデフォルトとしています。 <br>実行に必要な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>を設定します。※インストール時に設定したものと同様の内容です。 </p>
<pre class="terminal">  # export PATH=$PATH:/home/tpcc-uva/pgsql/bin:/home/tpcc-uva/bin/ # export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/tpcc-uva/pgsql/lib  
</pre>
<p>TPCC-UVaによる<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>プログラム、benchを実行します。 </p>
<pre class="terminal">  # /home/tpcc-uva/bin/bench        +---------------------------------------------------+     | BENCHMARK TPCC  --- UNIVERSIDAD DE VALLADOLID --- |     +---------------------------------------------------+             1.- CREATE A NEW TEST DATABASE                8.- Quit      SELECT OPTION:  
</pre>
<p><strong>※<u>接続できない場合</u></strong></p>
<p>もし、以下のようなエラーが出る場合、接続ユーザが存在していない可能性があります。 (私の環境ではpostgresユーザを含むOS上のどのユーザで実行しても、rootで接続しようとしてエラーとなっていました。接続ユーザを制御する方法があるのかはわかっていません。) </p>
<pre class="terminal">  Error number: -402         Could not connect to database template1 in line 4208. Error checking if database exists. Error number: -220         No such connection NULL in line 4216.  
</pre>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>にrootユーザを作成します。最低限DB作成権限を与えておきます。 </p>
<pre class="terminal">  # psql -U postgres  
</pre>
<pre class="terminal">  postgres=# CREATE USER root CREATEDB CREATEUSER;  
</pre>
<pre class="terminal">  postgres=# select * from pg_user;  usename  | usesysid | usecreatedb | usesuper | usecatupd | userepl |  passwd  | valuntil | useconfig ----------+----------+-------------+----------+-----------+---------+----------+----------+-----------  postgres |       10 | t           | t        | t         | t       | ******** |          |  pgbench  |    16491 | f           | f        | f         | f       | ******** |          |  root     |    16517 | t           | t        | t         | f       | ******** |          | (5 rows)  
</pre>
<p>初回実行時は"1.- CREATE A NEW TEST DATABASE"を選んで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>用のデータベースを作成します。 しばらく待つと<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>用のデータベースとテーブルが作成されます。 </p>
<pre class="terminal">  SELECT OPTION: 1  
</pre>
<p>warehouses(倉庫)をいくつ作成するか問われます。ここでは1を選択しています。<br>データが作成されると"Continue..."となるのでEnterキーを押下します。 </p>
<pre class="terminal">  Enter the number of warehouses: 1 Continue? (y/n): y Starting to populate database. Start time: 2013-03-10 11:28:31 POPULATING DATABASE WITH 1 WAREHOUSES... … POPULATION COMPLETED. Start time: 2013-03-10 11:28:31 End time: 2013-03-10 11:39:58  Continue...  
</pre>
<p>続いて以下のような画面となりますが、一旦オプションの"8.- Quit"を選択して実行を終了し、<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>サーバにログインして、テストデータベースの情報をみてみます。 </p>
<pre class="terminal">      +---------------------------------------------------+     | BENCHMARK TPCC  --- UNIVERSIDAD DE VALLADOLID --- |     +---------------------------------------------------+             2.- RESTORE EXISTING DATABASE.         3.- RUN THE TEST.         4.- CHECK DATABASE CONSISTENCY.         5.- DELETE DATABASE.          7.- CHECK DATABASE STATE.           8.- Quit      SELECT OPTION:  
</pre>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>サーバにログインすると、tpccというデータベースが作成されており、その中に以下のようなテーブルが作成されていることが分かります。 </p>
<pre class="terminal">  # psql -U root tpcc  
</pre>
<pre class="terminal">  tpcc=# \l              List of databases         Name         |  Owner   | Encoding ---------------------+----------+----------  pgbench             | pgbench  | UTF8  postgres            | postgres | UTF8  template0           | postgres | UTF8  template1           | postgres | UTF8  tpcc                | root     | UTF8 (9 rows)  
</pre>
<pre class="terminal">  tpcc=# \d           List of relations  Schema |    Name    | Type  | Owner --------+------------+-------+-------  public | customer   | table | root  public | district   | table | root  public | history    | table | root  public | item       | table | root  public | new_order  | table | root  public | order_line | table | root  public | orderr     | table | root  public | stock      | table | root  public | warehouse  | table | root (9 rows)  
</pre>
<p>各テーブルは以下のようなデータが作成されています。 テーブルの中にはデタラメな値が入力されています。 </p>
<table>  <tr>    <td>customer</td>    <td>顧客テーブル。1倉庫あたり3万行のデータが作成されます。</td>  </tr>  <tr>    <td>district</td>    <td>配送地域テーブル。1倉庫あたり10行のデータが作成されます。</td>  </tr>  <tr>    <td>hisotory</td>    <td>支払い処理履歴テーブル。1倉庫あたり3万行のデータが作成されます。</td>  </tr>  <tr>    <td>item</td>    <td>商品テーブル。倉庫の数に関わらず、10万行のデータが作成されます。</td>  </tr>  <tr>    <td>new_orders</td>    <td>新規注文テーブル。1倉庫あたり9千行のデータが作成されます。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>実行中にデータが増加(受注後)/減少(配送後)していきます。</td>  </tr>  <tr>    <td>order_line</td>    <td>注文明細テーブル。1倉庫あたり30万行のデータが作成されます。</td>  </tr>  <tr>    <td>orderr</td>    <td>注文テーブル。1倉庫あたり3万行のデータが作成されます。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>実行中にデータが増加していきます。</td>  </tr>   <tr>    <td>stock</td>    <td>在庫テーブル。1倉庫あたり10万行のデータが作成されます。</td>  </tr>  <tr>    <td>warehouse</td>    <td>倉庫テーブル。1倉庫あたり1行のデータが作成されます。</td>  </tr>
</table>
<p>続けます。再度benchを実行します。 </p>
<pre class="terminal">  # bench         +---------------------------------------------------+     | BENCHMARK TPCC  --- UNIVERSIDAD DE VALLADOLID --- |     +---------------------------------------------------+             2.- RESTORE EXISTING DATABASE.         3.- RUN THE TEST.         4.- CHECK DATABASE CONSISTENCY.         5.- DELETE DATABASE.          7.- CHECK DATABASE STATE.           8.- Quit      SELECT OPTION:  
</pre>
<p>オプションの"3.- RUN THE TEST."を選択し、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>テストを実行してみます。選択肢の </p>
<pre class="terminal">  SELECT OPTION: 3 -&gt;Enter number of warehouses 1 -&gt;Enter number of terminals per warehouse (max. 10) 10 -&gt;Enter ramp-up period (minutes) 20 -&gt;Enter measurement interval (minutes) 300 Continue? (y/n): y -&gt;Do you want to perform vacuums during the test? (y/n): y         -&gt;Enter interval between vacuums (minutes): 60 -&gt;Do you want to specify the maximum number of vacuums? (y/n): y         -&gt;Enter the maximum number of vacuums: 1 -&gt;Performing vacuums every 60 minutes, to reach a maximum number of 1. Start the test? (y/n): y  
</pre>
<ul>  <li>Number of warehouses：</li>warehousesの数。先ほど作成した数を指定します。   <li>Number of terminals per warehouse：</li>1つのwarehousesに対する同時接続数。<a class="keyword" href="http://d.hatena.ne.jp/keyword/TPC">TPC</a>-C公式仕様書では10となっているようです。   <li>Ramp-up period：</li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>の記録を開始するまでの時間(分単位)。データがメモリにキャッシュされるまでの時間待つのが望ましいため、プログラム実行開始から<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>計測開始までの待ち時間を指定できます。ユーザガイドには20分程度と記載されていますが、環境に合わせて指定します。   <li>Measurement period：</li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>測定を行う時間(分単位)。2時間(120min)から最大で8時間(480min)の値を指定します。   <li>interval between vacuums：</li>バキュームを行う間隔(分単位)。   <li>maximum number of vacuums：</li>バキュームを行う最大回数。 </ul>
<p>以下のような処理が流れ続けます。終了するまで待ちます。 </p>
<pre class="terminal">  … &gt;&gt; NEW_ORDER Transaction.    Terminal 6.        Done. &gt;&gt; PAYMENT Transaction.      Terminal 8.        Done. &gt;&gt; PAYMENT Transaction.      Terminal 1.        Done. &gt;&gt; PAYMENT Transaction.      Terminal 9.        Done. &gt;&gt; PAYMENT Transaction.      Terminal 0.        Done. &gt;&gt; NEW_ORDER Transaction.    Terminal 7.        Done. &gt;&gt; ORDER_STATUS Transaction. Terminal 3.        Done. &gt;&gt; DELIVERY Transaction.     Terminal 7.        Done. &gt;&gt; NEW_ORDER Transaction.    Terminal 2.        Done. &gt;&gt; PAYMENT Transaction.      Terminal 5.        Done. &gt;&gt; NEW_ORDER Transaction.    Terminal 6.        Done. &gt;&gt; PAYMENT Transaction.      Terminal 2.        Done. &gt;&gt; PAYMENT Transaction.      Terminal 8.        Done. …  </pre> <h5>結果データの取得</h5>
<p>処理が完了したら、結果のデータを収集します。<br>データの保存先として以下の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成しておきます。 </p>
<pre class="terminal">  # mkdir -p /tmp/tpccuva_result  
</pre>
<p>benchコマンドを実行し、オプションの"6.- SHOW AND STORE TEST RESULTS"を選択します。 benchを実行した際のカレント<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リに*.datというグラフの生成に必要なファイルが作成されますので、出力結果を保存する先の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リに移動しておきます。 </p>
<pre class="terminal">  # cd /tmp/tpccuva_result  
</pre>
<pre class="terminal">  # /home/tpcc-uva/bin/bench              +---------------------------------------------------+     | BENCHMARK TPCC  --- UNIVERSIDAD DE VALLADOLID --- |     +---------------------------------------------------+             2.- RESTORE EXISTING DATABASE.         3.- RUN THE TEST.         4.- CHECK DATABASE CONSISTENCY.         5.- DELETE DATABASE.         6.- SHOW AND STORE TEST RESULTS         7.- CHECK DATABASE STATE.           8.- Quit     SELECT OPTION: 6  
</pre>
<p>以下のようなデータが結果が表示されますが、最後にファイルに出力できるのでそのままEnterキーを押下し続けます。 </p>
<pre class="terminal">  Computation of performance test done on 2013-03-10 at 13:25:27 for 1 warehouses.  Start of measurement interval: 20.000083 m  End of measurement interval: 320.000083 m  COMPUTED THROUGHPUT: 12.537 tpmC-uva using 1 warehouses.  8642 Transactions committed. NEW-ORDER TRANSACTIONS:   3761 Transactions committed into measurement interval. Total transactions: 4015.   Percentage of Total transactions: 43.520%   Percentage of "well done": 100.000%   Response time (min/avg/max/90th): 0.012 / 0.047 / 0.277 / 0.040   Percentage of rolled-back transactions: 1.064% .   Average number of items per order: 9.360 .   Percentage of remote items: --- (One warehouse only).   Think time (min/avg/max): 0.000 / 12.167 / 109.000  Continue...  PAYMENT TRANSACTIONS:   3757 Transactions committed into measurement interval. Total transactions: 4019.   Percentage of Total transactions: 43.474%   Percentage of "well done": 100.000%   Response time (min/avg/max/90th): 0.002 / 0.018 / 0.299 / 0.000   Percentage of remote transactions: --- (One warehouse only)   Percentage of customers selected by C_ID: 40.964% .   Think time (min/avg/max): 0.000 / 12.141 / 109.000  Continue...  ORDER-STATUS TRANSACTIONS:   372 Transactions committed into measurement interval. Total transactions: 400.   Percentage of Total transactions: 4.305%   Percentage of "well done": 100.000%   Response time (min/avg/max/90th): 0.003 / 0.018 / 0.218 / 0.000   Percentage of customers selected by C_ID: 42.742% .   Think time (min/avg/max): 0.000 / 9.642 / 50.000  Continue...  DELIVERY TRANSACTIONS:   376 Transactions committed into measurement interval. Total transactions: 402.   Percentage of Total transactions: 4.351%   Percentage of "well done": 100.000%   Response time (min/avg/max/90th): 0.000 / 0.000 / 0.001 / 0.000   Percentage of execution time &lt; 80s : 100.000%   Execution time min/avg/max: 0.031/0.061/0.171   No. of skipped districts: 0 .   Percentage of skipped districts: 0.000%.   Think time (min/avg/max): 0.000 / 5.136 / 25.000  Continue...  STOCK-LEVEL TRANSACTIONS:   376 Transactions committed into measurement interval. Total transactions: 402.   Percentage of Total transactions: 4.351%   Percentage of "well done": 100.000%   Response time (min/avg/max/90th): 0.003 / 0.020 / 0.150 / 0.000   Think time (min/avg/max): 0.000 / 4.987 / 25.000  Continue...   Longest checkpoints: Start Time      Elapsed time since test start (s)       Execution time (s) Sun Mar 10 13:45:27 2013        1200.029000             11.729000 Sun Mar 10 16:45:44 2013        12016.485000            4.010000 Sun Mar 10 14:45:39 2013        4812.168000             3.007000 Sun Mar 10 18:15:48 2013        17421.301000            0.601000 Continue ...   Longest vacuums: Start Time      Elapsed time since test start (s)       Execution time (s) Sun Mar 10 14:25:27 2013        3600.040000             7.242000  &gt;&gt; TEST PASSED Continue...  
</pre>
<p>ここでファイルに出力するかどうか問われます。 <br>"y"を入力し、ファイル名を適当に付けて保存します。ここでは/tmp/tpccuva_result配下にtpcc-uva_result_20130310.<a class="keyword" href="http://d.hatena.ne.jp/keyword/tpc">tpc</a>として出力させることとし、/tmp/tpccuva_result/tpcc-uva_result_20130310.<a class="keyword" href="http://d.hatena.ne.jp/keyword/tpc">tpc</a>という形で指定しています。なお、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを指定しない場合、ファイルはbenchを実行した際のカレント<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リに出力されます。 <br>ここで保存し忘れても再度"6.- SHOW AND STORE TEST RESULTS"を指定して出力できます。 </p>
<pre class="terminal">  Do yo want to store the results into a file? (y/n): y    Enter file name (prefereably with .tpc extension)    filename: /tmp/tpccuva_result/tpcc-uva_result_20130310.tpc    Results have been written on file /tmp/tpccuva_result/tpcc-uva_result_20130310.tpc. Continue...  </pre> <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Gnuplot">Gnuplot</a>でのグラフ出力</h5>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Gnuplot">Gnuplot</a>で結果をグラフ出力します。付属のPDRファイル：tpccuva-guide.pdfのP17～P18ページの内容を元に以下のような<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>を作成します。<br></p>
<pre class="terminal">  # vim /tmp/tpccuva_result/tpccuva_result/516.gnp set terminal postscript 22 set size 1 , 1 set pointsize 1 set output "/tmp/tpccuva_result/561-NewOrder.eps" set title "Response Time Distribution, New Order transactions" set xlabel "Response Time (s)" set ylabel "Number of Transactions" plot [0: XX ] "/tmp/tpccuva_result/g1NewOrder.dat" with lines  set output "/tmp/tpccuva_result/561-Delivery.eps" set title "Response Time Distribution, Delivery transactions" set xlabel "Response Time (s)" set ylabel "Number of Transactions" plot [0: XX ] "/tmp/tpccuva_result/g1Delivery.dat" with lines  set output "/tmp/tpccuva_result/561-OrderStatus.eps" set title "Response Time Distribution, Order Status transactions" set xlabel "Response Time (s)" set ylabel "Number of Transactions" plot [0: XX ] "/tmp/tpccuva_result/g1OrderStatus.dat" with lines  set output "/tmp/tpccuva_result/561-Payment.eps" set title "Response Time Distribution, Payment transactions" set xlabel "Response Time (s)" set ylabel "Number of Transactions" plot [0: XX ] "/tmp/tpccuva_result/g1Payment.dat" with lines  set output "/tmp/tpccuva_result/561-StockLevel.eps" set title "Response Time Distribution, Stock Level transactions" set xlabel "Response Time (s)" set ylabel "Number of Transactions" plot [0: XX ] "/tmp/tpccuva_result/g1StockLevel.dat" with lines  
</pre>
<p>"set output"で出力するファイル名を指定します。<br>グラフのサイズは"set size X, X"で調整します。<br>"plot[0: XX]"の"XX"は各<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>結果の"Response time (min/<a class="keyword" href="http://d.hatena.ne.jp/keyword/avg">avg</a>/max/90th)"の"90th"の4倍の値を指定します。<br></p>
<p>作成した<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>を実行します。 </p>
<pre class="terminal">  # /home/tpcc-uva/bin/bin/gnuplot /tmp/tpccuva_result/516.gnp  
</pre>
<p>作成されたファイルの1つを画像ビューアで観てみますと以下のような形となります。 </p>
<a href="http://3.bp.blogspot.com/-n5ygR4pi-fQ/UTyCLjGgOcI/AAAAAAAAASk/nQftCj0mhl4/s1600/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88+2013-03-10+21.52.32.png" imageanchor="1"><img border="0" align="center" src="http://3.bp.blogspot.com/-n5ygR4pi-fQ/UTyCLjGgOcI/AAAAAAAAASk/nQftCj0mhl4/s320/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88+2013-03-10+21.52.32.png"></a><p>以上のように結果やグラフを収集し、分析/チューニングをしていくことになります。 </p>
<p>まだ結果をきちんと確認できていませんが、上記の内容で実行した際にはCPUの%user使用率が100%に張り付いた状態となり、ほとんどI/O負荷は発生しませんでした。 "COMPUTED THROUGHPUT: 12.537"となっており、CPUがネックなのかCPUに負荷がかかる仕様なのか分かりませんが、まずは、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>の内容を見直してみる必要があるように思っています。 </p> <h4>おわりに</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>、パフォーマンスチューニング等のために使えるかと思ってTPCC-UVaを試してみました。開発が進んでいないように思え残念ですが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>用のDBの作成や<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>結果出力、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>用のテーブルの削除などが<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%E9%A5%AF%A5%C6%A5%A3%A5%D6">インタラクティブ</a>に実行できる点は割と使いやすいように思いました。<br>ただ、最近では<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>に対応した<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D9%A5%F3%A5%C1%A5%DE%A1%BC%A5%AF">ベンチマーク</a>ツールは最近では他にももっと良いツールがあるかも知れません。 </p>
</body>

<!-- more -->


