---
title: "SQLite がどんなものかを少し調べながら sqlite3 コマンドでの基本操作を確認してみた"
date: 2022-11-05
slug: "SQLite がどんなものかを少し調べながら sqlite3 コマンドでの基本操作を確認してみた"
category: blog
tags: [Tech,SQLite]
---
<h2 id="はじめに">はじめに</h2>

<p>ISUCON 12 で <a class="keyword" href="https://d.hatena.ne.jp/keyword/SQLite">SQLite</a> が登場してちょっと話題になった<a href="#f-d2b63a25" id="fn-d2b63a25" name="fn-d2b63a25" title="https://isucon.net/archives/56850281.html">*1</a>。
だいぶ月日が経ったけど <a class="keyword" href="https://d.hatena.ne.jp/keyword/SQLite">SQLite</a> をそれほどきちんと触ったことがなかったので、少し調べたり触ってみたりした。</p>

<h2 id="SQLite-とその特徴"><a class="keyword" href="https://d.hatena.ne.jp/keyword/SQLite">SQLite</a> とその特徴</h2>

<p>軽量なデータベース、くらいの認識しかなかったので改めて少し調べた。</p>

<h3 id="ざっくり箇条書きまとめ">ざっくり箇条書きまとめ</h3>

<ul>
<li><a class="keyword" href="https://d.hatena.ne.jp/keyword/SQLite">SQLite</a> は C ライブラリであり、<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%BD%A1%BC%A5%B9%A5%B3%A1%BC%A5%C9">ソースコード</a>を<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>して利用する。</li>
<li>データベースは <code>xxxx.db</code> といった形式のファイルとして保存される。</li>
<li>何らかのアプリケーションから、<a class="keyword" href="https://d.hatena.ne.jp/keyword/SQLite">SQLite</a> ライブラリを利用して、直接 <code>xxx.db</code> 形式のデータベースファイルを読み書きするといった形になる。

<ul>
<li>sqlite3 コマンドが提供されているので、これを利用してシェルから <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> で対話的に操作/<a class="keyword" href="https://d.hatena.ne.jp/keyword/SQL">SQL</a>実行できる</li>
</ul>
</li>
<li>設定ファイルはない。</li>
<li>一般的な <a class="keyword" href="https://d.hatena.ne.jp/keyword/RDB">RDB</a> と違い、クライアント/サーバ構成をとっていない。

<ul>
<li>サーバープロセス(<a class="keyword" href="https://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a> なら postgres プロセス, <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> なら <a class="keyword" href="https://d.hatena.ne.jp/keyword/mysql">mysql</a> プロセス)といったものは存在しない</li>
<li><a class="keyword" href="https://d.hatena.ne.jp/keyword/TCP">TCP</a> や <a class="keyword" href="https://d.hatena.ne.jp/keyword/Unix">Unix</a> ソケットなどを使用してデータベースサーバーに接続する形ではない。</li>
<li>そのため、ネットワーク的に分離した別サーバー等にある <a class="keyword" href="https://d.hatena.ne.jp/keyword/SQLite">SQLite</a> のデータベースファイルにアクセスするのは難しい(<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%E6%A1%BC%A5%B9%A5%B1%A1%BC%A5%B9">ユースケース</a>としてもやらないほうが良い)</li>
</ul>
</li>
<li><a class="keyword" href="https://d.hatena.ne.jp/keyword/SQLite">SQLite</a> のすべての<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>は、ACID に完全に準拠している。</li>
</ul>


<h3 id="簡単な概念図">簡単な概念図</h3>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20221228/20221228142944.png" width="369" height="132" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<h2 id="動かしてみる">動かしてみる</h2>

<h3 id="環境">環境</h3>

<pre class="code" data-lang="" data-unlink>% sw_vers
ProductName:    macOS
ProductVersion: 12.6.1
BuildVersion:   21G217</pre>




<pre class="code" data-lang="" data-unlink> % sqlite3 -version
3.37.0 2021-12-09 01:34:53 9ff244ce0739f8ee52a3e9671adb4ee54c83c640b02e3f9d185fd2f9a179aapl</pre>


<h3 id="サンプルを使った動作と操作の確認">サンプルを使った動作と操作の確認</h3>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/SQLite">SQLite</a> Tutorial というサイトがある。</p>

<ul>
<li><a href="https://www.sqlitetutorial.net/">SQLite Tutorial &ndash; A Step-by-step SQLite Tutorial</a></li>
</ul>


<p>適当な<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成し、そのサイトからサンプルのデータベースを取得して配置する。</p>

<pre class="code" data-lang="" data-unlink>% mkdir sqlite-tutorial

% cd sqlite-tutorial

% curl -OL https://www.sqlitetutorial.net/wp-content/uploads/2018/03/chinook.zip</pre>


<p>展開する。</p>

<pre class="code" data-lang="" data-unlink>% unzip chinook.zip
Archive:  chinook.zip
  inflating: chinook.db</pre>


<p>データベースにアクセスする。</p>

<pre class="code" data-lang="" data-unlink>% sqlite3 chinook.db
SQLite version 3.37.0 2021-12-09 01:34:53
Enter &#34;.help&#34; for usage hints.
sqlite&gt;</pre>


<p><code>.help</code> はこんな感じの出力だった。</p>

<pre class="code" data-lang="" data-unlink>sqlite&gt; .help
.auth ON|OFF             Show authorizer callbacks
.backup ?DB? FILE        Backup DB (default &#34;main&#34;) to FILE
.bail on|off             Stop after hitting an error.  Default OFF
.binary on|off           Turn binary output on or off.  Default OFF
.cd DIRECTORY            Change the working directory to DIRECTORY
.changes on|off          Show number of rows changed by SQL
.check GLOB              Fail if output since .testcase does not match
.clone NEWDB             Clone data into NEWDB from the existing database
.connection [close] [#]  Open or close an auxiliary database connection
.databases               List names and files of attached databases
.dbconfig ?op? ?val?     List or change sqlite3_db_config() options
.dbinfo ?DB?             Show status information about the database
.dump ?OBJECTS?          Render database content as SQL
.echo on|off             Turn command echo on or off
.eqp on|off|full|...     Enable or disable automatic EXPLAIN QUERY PLAN
.excel                   Display the output of next command in spreadsheet
.exit ?CODE?             Exit this program with return-code CODE
.expert                  EXPERIMENTAL. Suggest indexes for queries
.explain ?on|off|auto?   Change the EXPLAIN formatting mode.  Default: auto
.filectrl CMD ...        Run various sqlite3_file_control() operations
.fullschema ?--indent?   Show schema and the content of sqlite_stat tables
.headers on|off          Turn display of headers on or off
.help ?-all? ?PATTERN?   Show help text for PATTERN
.import FILE TABLE       Import data from FILE into TABLE
.imposter INDEX TABLE    Create imposter table TABLE on index INDEX
.indexes ?TABLE?         Show names of indexes
.limit ?LIMIT? ?VAL?     Display or change the value of an SQLITE_LIMIT
.lint OPTIONS            Report potential schema issues.
.log FILE|off            Turn logging on or off.  FILE can be stderr/stdout
.mode MODE ?TABLE?       Set output mode
.nonce STRING            Disable safe mode for one command if the nonce matches
.nullvalue STRING        Use STRING in place of NULL values
.once ?OPTIONS? ?FILE?   Output for the next SQL command only to FILE
.open ?OPTIONS? ?FILE?   Close existing database and reopen FILE
.output ?FILE?           Send output to FILE or stdout if FILE is omitted
.parameter CMD ...       Manage SQL parameter bindings
.print STRING...         Print literal STRING
.progress N              Invoke progress handler after every N opcodes
.prompt MAIN CONTINUE    Replace the standard prompts
.quit                    Exit this program
.read FILE               Read input from FILE
.recover                 Recover as much data as possible from corrupt db.
.restore ?DB? FILE       Restore content of DB (default &#34;main&#34;) from FILE
.save FILE               Write in-memory database into FILE
.scanstats on|off        Turn sqlite3_stmt_scanstatus() metrics on or off
.schema ?PATTERN?        Show the CREATE statements matching PATTERN
.selftest ?OPTIONS?      Run tests defined in the SELFTEST table
.separator COL ?ROW?     Change the column and row separators
.session ?NAME? CMD ...  Create or control sessions
.sha3sum ...             Compute a SHA3 hash of database content
.shell CMD ARGS...       Run CMD ARGS... in a system shell
.show                    Show the current values for various settings
.stats ?ARG?             Show stats or turn stats on or off
.system CMD ARGS...      Run CMD ARGS... in a system shell
.tables ?TABLE?          List names of tables matching LIKE pattern TABLE
.testcase NAME           Begin redirecting output to &#39;testcase-out.txt&#39;
.testctrl CMD ...        Run various sqlite3_test_control() operations
.timeout MS              Try opening locked tables for MS milliseconds
.timer on|off            Turn SQL timer on or off
.trace ?OPTIONS?         Output each SQL statement as it is run
.vfsinfo ?AUX?           Information about the top-level VFS
.vfslist                 List all available VFSes
.vfsname ?AUX?           Print the name of the VFS stack
.width NUM1 NUM2 ...     Set minimum column widths for columnar output</pre>


<p>テーブル一覧を見てみる。</p>

<pre class="code" data-lang="" data-unlink>sqlite&gt; .table
albums          employees       invoices        playlists
artists         genres          media_types     tracks
customers       invoice_items   playlist_track</pre>


<p><code>.database</code> で、現在の接続しているデータベースを表示できる。
main という名前のデータベースを少なくとも 1 つ表示される。</p>

<p>たとえば、次のコマンドは、現在の接続のすべてのデータベースを表示します。</p>

<pre class="code" data-lang="" data-unlink>sqlite&gt; .database
main: /path/to/sqlite-tutorial/chinook.db r/w</pre>


<p>どこにも接続してない場合は下記のようになる。</p>

<pre class="code" data-lang="" data-unlink>% sqlite3
SQLite version 3.37.0 2021-12-09 01:34:53
Enter &#34;.help&#34; for usage hints.
Connected to a transient in-memory database.
Use &#34;.open FILENAME&#34; to reopen on a persistent database.

sqlite&gt; .database
main: &#34;&#34; r/w</pre>


<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/sqlite">sqlite</a> の <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> を起動中に他のデータベースに接続する場合は下記のように <code>attach database</code> コマンドを使う。</p>

<pre class="code" data-lang="" data-unlink>sqlite&gt; attach database &#34;/path/to/sqlite-tutorial/test.db&#34; as testdb;</pre>


<p>データベース一覧に追加される。</p>

<pre class="code" data-lang="" data-unlink>sqlite&gt; .database
main: /Users/daisuke.shimizu/workspace/sqlite3/db/chinook.db r/w
testdb: /Users/daisuke.shimizu/workspace/go-clean-arch-study/test.db r/w</pre>


<h2 id="まとめ">まとめ</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/SQLite">SQLite</a> をそんなにちゃんと触ったことがなかったので、ネットの記事等をざっと見つつ、手元で操作してみた。
面倒なインストール作業が不要で、データはファイルベースで管理しつつ、データベースとしての機能は普通に使えるので、少し試すには簡単だと改めて実感した。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://qiita.com/ko1nksm/items/87d27a287e1b6005d11c#%E3%81%95%E3%81%84%E3%81%94%E3%81%AB">&#x5229;&#x7528;&#x8005;&#x306F;&#x6570;&#x5341;&#x5104;&#x4EBA;&#xFF01;&#xFF1F; SQLite&#x306F;&#x3069;&#x3053;&#x304C;&#x51C4;&#x3044;&#x30C7;&#x30FC;&#x30BF;&#x30D9;&#x30FC;&#x30B9;&#x7BA1;&#x7406;&#x30B7;&#x30B9;&#x30C6;&#x30E0;&#x306A;&#x306E;&#x304B;&#x8ABF;&#x3079;&#x3066;&#x307F;&#x305F; #SQL - Qiita</a></li>
<li><a href="https://sites.google.com/site/kandamotohiro/sqlite/sqlitewhentouse">Google Sites: Sign-in</a></li>
</ul>

<div class="footnote">
<p class="footnote"><a href="#fn-d2b63a25" id="f-d2b63a25" name="f-d2b63a25" class="footnote-number">*1</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://isucon.net/archives/56850281.html">https://isucon.net/archives/56850281.html</a></span></p>
</div>
