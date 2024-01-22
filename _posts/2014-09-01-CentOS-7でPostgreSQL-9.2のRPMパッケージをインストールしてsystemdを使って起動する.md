---
layout: post
Categories:  ["tech"]
Description:  " はじめに  CentOS 7.0へPostgreSQLのRPMパッケージをインストールし、systemd経由で起動させるための備忘録です。2014/8/31時点でCentOS 7.0のBaseリポジトリに登録されているPostgreSQL"
Tags: ["PostgreSQL", "さくらのVPS", "CentOS 7"]
date: "2014-09-01T08:36:00+09:00"
title: "CentOS 7でPostgreSQL 9.2のRPMパッケージをインストールしてsystemdを使って起動する"
author: "dshimizu"
archive: ["2014"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/CentOS">CentOS</a> 7.0へ<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/RPM">RPM</a>パッケージをインストールし、systemd経由で起動させるための備忘録です。2014/8/31時点で<a class="keyword" href="http://d.hatena.ne.jp/keyword/CentOS">CentOS</a> 7.0のBase<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>に登録されている<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>のバージョンは9.2.7-1でした。 </p> <a name="more"></a> <h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>のインストールと起動</h4>
<h5>環境</h5>
<p>インストール環境は以下です </p>
<table>
<tr>  <td bgcolor="#cccccc">プラットフォーム</td>  <td>さくらの<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPS">VPS</a> (<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSD">SSD</a> 1G)</td>
</tr>
<tr>  <td bgcolor="#cccccc">OS</td>  <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/CentOS">CentOS</a> 7.0 <a class="keyword" href="http://d.hatena.ne.jp/keyword/x86">x86</a>_64 (64bit)</td>
</tr>
</table> <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/RPM">RPM</a>パッケージのインストール</h5>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/RPM">RPM</a>パッケージをインストールします。Base<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>にパッケージが存在するので、<a class="keyword" href="http://d.hatena.ne.jp/keyword/yum">yum</a>でインストールします。 </p>
<pre class="terminal">  % sudo yum install postgresql postgresql-server postgresql-libs postgresql-devel postgresql-contrib  </pre> <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>の起動</h5>
<p>起動を試みますが失敗します。 </p>
<pre class="terminal">  % sudo systemctl start postgresql.service [sudo] password for shimizu: Job for postgresql.service failed. See 'systemctl status postgresql.service' and 'journalctl -xn' for details.  </pre> <p>詳細を観るためにstatusを確認しろ、といったメッセージが出ますのでとりあえず確認してみます。<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>のデータ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リがない、といった内容のメッセージが出力されているようです。 </p>
<pre class="terminal">  % sudo systemctl status postgresql.service postgresql.service - PostgreSQL database server    Loaded: loaded (/usr/lib/systemd/system/postgresql.service; disabled)    Active: failed (Result: exit-code) since 日 2014-08-31 21:36:51 JST; 16s ago   Process: 6571 ExecStartPre=/usr/bin/postgresql-check-db-dir ${PGDATA} (code=exited, status=1/FAILURE)  </pre> <p><a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>のユニットファイル<code>/usr/lib/systemd/system/postgresql.service</code>でデータ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リに何が指定されているかを確認してみると<code>/var/lib/pgsql/data</code>が指定されていることがわかります。<br>データ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リの場所を変更したければ、この項目を修正します。ここでは変更せずに進めます。 </p>
<pre class="terminal">  % cat /usr/lib/systemd/system/postgresql.service ・・・ # Location of database directory Environment=PGDATA=/var/lib/pgsql/data ・・・  </pre> <p>指定されているデータ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成します。 </p>
<pre class="terminal">  % sudo mkdir -p /var/lib/pgsql/data  </pre> <p>所有者、所有グループを<code>postgres</code>へ変更します。 </p>
<pre class="terminal">  % sudo chown postgres:postgres /var/lib/pgsql/data  </pre> <p><code>postgres</code>ユーザへスイッチします。 </p>
<pre class="terminal">  % sudo -i -u postgres  </pre> <p>postgresユーザでデータベース<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>を初期化します。 </p>
<pre class="terminal">  -bash-4.2$ initdb  -D '/var/lib/pgsql/data' データベースシステム内のファイルの所有者は"postgres"ユーザでした。 このユーザがサーバプロセスを所有しなければなりません。  データベースクラスタはロケール"ja_JP.UTF-8"で初期化されます。 したがってデフォルトのデータベース符号化方式はUTF8に設定されました。 initdb: ロケール"ja_JP.UTF-8"用の適切なテキスト検索設定が見つかりません デフォルトのテキスト検索設定はsimpleに設定されました。  ディレクトリ/var/lib/pgsql/dataの権限を設定しています ... ok サブディレクトリを作成しています ... ok デフォルトのmax_connectionsを選択しています ... 100 デフォルトの shared_buffers を選択しています ... 32MB 設定ファイルを作成しています ... ok /var/lib/pgsql/data/base/1にtemplate1データベースを作成しています ... ok pg_authidを初期化しています ... ok 依存関係を初期化しています ... ok システムビューを作成しています ... ok システムオブジェクトの定義をロードしています ... ok 照合順序を作成しています ... ok 変換を作成しています ... ok ディレクトリを作成しています ... ok 組み込みオブジェクトに権限を設定しています ... ok 情報スキーマを作成しています ... ok PL/pgSQL サーバサイド言語をロードしています ... ok template1データベースをバキュームしています ... ok template1からtemplate0へコピーしています ... ok template1からpostgresへコピーしています ... ok  警告: ローカル接続向けに"trust"認証が有効です。 pg_hba.confを編集する、もしくは、次回initdbを実行する時に-Aオプショ ン、または、--auth-localおよび--auth-hostを使用することで変更するこ とができます。  成功しました。以下を使用してデータベースサーバを起動することができます。      postmaster -D /var/lib/pgsql/data または     pg_ctl -D /var/lib/pgsql/data -l logfile start   </pre> <p>これで準備が整いました。<code>postgres</code>ユーザからログアウトします。 </p>
<pre class="terminal">  -bash-4.2$ exit  </pre> <p>改めて起動を試みます。 </p>
<pre class="terminal">  % sudo systemctl start postgresql.service  </pre> <p>状態を確認してみます。 </p>
<pre class="terminal">  %  sudo systemctl status postgresql.service postgresql.service - PostgreSQL database server    Loaded: loaded (/usr/lib/systemd/system/postgresql.service; disabled)    Active: active (running) since 日 2014-08-31 22:07:22 JST; 20s ago   Process: 7027 ExecStart=/usr/bin/pg_ctl start -D ${PGDATA} -s -o -p ${PGPORT} -w -t 300 (code=exited, status=0/SUCCESS)   Process: 7021 ExecStartPre=/usr/bin/postgresql-check-db-dir ${PGDATA} (code=exited, status=0/SUCCESS)  Main PID: 7031 (postgres)    CGroup: /system.slice/postgresql.service            ├─7031 /usr/bin/postgres -D /var/lib/pgsql/data -p 5432            ├─7032 postgres: logger process            ├─7034 postgres: checkpointer process            ├─7035 postgres: writer process            ├─7036 postgres: wal writer process            ├─7037 postgres: autovacuum launcher process            └─7038 postgres: stats collector process   8月 31 22:07:22 www4242gi.sakura.ne.jp systemd[1]: Started PostgreSQL database server.  </pre> <p>プロセスを確認してみます。 </p>
<pre class="terminal">  % ps auxfwww | grep postgres shimizu   7140  0.0  0.0 112656   988 pts/0    S+   22:47   0:00              \_ grep --color=auto postgres postgres  7031  0.0  0.5 233264  9464 ?        S    22:07   0:00 /usr/bin/postgres -D /var/lib/pgsql/data -p 5432 postgres  7032  0.0  0.0 193012  1460 ?        Ss   22:07   0:00  \_ postgres: logger process postgres  7034  0.0  0.0 233264  1668 ?        Ss   22:07   0:00  \_ postgres: checkpointer process postgres  7035  0.0  0.1 233264  1940 ?        Ss   22:07   0:00  \_ postgres: writer process postgres  7036  0.0  0.0 233264  1444 ?        Ss   22:07   0:00  \_ postgres: wal writer process postgres  7037  0.0  0.1 234088  2932 ?        Ss   22:07   0:00  \_ postgres: autovacuum launcher process postgres  7038  0.0  0.0 193008  1672 ?        Ss   22:07   0:00  \_ postgres: stats collector process  
</pre>
<p>問題ないようです。 </p>
<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>のデータベースへの接続</h5>
<p>データベースにログインしてみます。<br>※デフォルトの<code>pg_hba.conf</code>の設定では<code>local</code>からの接続が<code>trust</code>(<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>データベースサーバに接続できる全てのユーザが、任意の<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>ユーザとしてパスワードなしでログインすることを許可する)設定になっていますので、どのOSユーザからでもログインできるはずです。 </p>
<pre class="terminal">  % psql -U postgres psql (9.2.7) "help" でヘルプを表示します.  postgres=#  
</pre>
<p>問題ないようです。<br>接続を終了するには、<code>\q</code>を入力します。 </p> <h4>おわりに</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/CentOS">CentOS</a> 7.0へ<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a> 9.2の<a class="keyword" href="http://d.hatena.ne.jp/keyword/RPM">RPM</a>パッケージをインストールしてから<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>を起動して接続するまでの基本的な手順を記載しました。<code>systemd</code>はまだよく分かりません。 </p> <h4>参考</h4>
<ul>  <li><a href="https://wiki.archlinux.org/index.php/PostgreSQL">PostgreSQL - ArchWiki</a></li>  <li><a href="https://bbs.archlinux.org/viewtopic.php?id=149446">[SOLVED] Postgresql and systemd - Unable to start or enable service (Page 1) / Applications &amp; Des...</a></li>
</ul>
</body>

<!-- more -->


