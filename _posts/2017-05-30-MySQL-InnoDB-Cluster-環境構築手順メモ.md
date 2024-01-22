---
layout: post
Categories:  ["tech"]
Description:  " 検証用にInnoDB Cluster(Group Replication + MySQL Shell + MySQL Router)の環境を作ってみましたので、その際の構築手順のメモ書きです。 "
Tags: ["MySQL", "tech"]
date: "2017-05-30T01:30:00+09:00"
title: "MySQL InnoDB Cluster 環境構築手順メモ"
author: "dshimizu"
archive: ["2017"]
draft: "true"
---

<body>
<p>検証用に<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Cluster(Group Replication + <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shell + <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Router)の環境を作ってみましたので、その際の構築手順のメモ書きです。</p>
</body>

<!-- more -->

<body>
<h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Cluster</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Clusterは、<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>のための高可用性な環境を構築するための<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%DD%A1%BC%A5%CD%A5%F3%A5%C8">コンポーネント</a>群です。
具体的には以下の3つから構成されます。</p>

<ul>
    <li>Group Replication</li>
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shell</li>
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Router</li>
</ul>


<h3>構築手順</h3>


<h4>環境</h4>


<table>
<tbody>
<tr>
<th>OS</th>
<td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> 16.04 LTS</td>
</tr>
<tr>
<th><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a></th>
<td>5.7.18 (<a class="keyword" href="http://d.hatena.ne.jp/keyword/Oracle">Oracle</a>社オフィシャルパッケージ)</td>
</tr>
<tr>
<th>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shell</th>
<td>1.0.9 (<a class="keyword" href="http://d.hatena.ne.jp/keyword/Oracle">Oracle</a>社オフィシャルパッケージ)</td>
</tr>
<tr>
<th>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Router</th>
<td>2.1.3 (<a class="keyword" href="http://d.hatena.ne.jp/keyword/Oracle">Oracle</a>社オフィシャルパッケージ)</td>
</tr>
</tbody>
</table>


<h4>構成</h4>


<p>あらかじめ、<a href="https://blog.dshimizu.jp/article/439" target="_blank" rel="noopener noreferrer">こちらの記事</a>の通りに、1つのホストOS内に、複数の<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>プロセスを異なるポート/データ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リで起動して、それらでGroup Replicationを構成し、さらに<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shellと<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Routerをインストールします。</p>

<table>
<tbody>
<tr>
<th><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a></th>
<th>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>ポート</th>
<th>Group Replication用ポート</th>
<th>データ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ</th>
</tr>
<tr>
<td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#01</td>
<td>24801</td>
<td>24901</td>
<td>/var/lib/<a class="keyword" href="http://d.hatena.ne.jp/keyword/mysql">mysql</a>-01/</td>
</tr>
<tr>
<td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#02</td>
<td>24802</td>
<td>24902</td>
<td>/var/lib/<a class="keyword" href="http://d.hatena.ne.jp/keyword/mysql">mysql</a>-02/</td>
</tr>
<tr>
<td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#03</td>
<td>24803</td>
<td>24903</td>
<td>/var/lib/<a class="keyword" href="http://d.hatena.ne.jp/keyword/mysql">mysql</a>-03/</td>
</tr>
</tbody>
</table>


<div align="center">
<img src="http://blog.dshimizu.jp/wp-content/uploads/2017/05/mysql-innodb-cluster-configuration-diagram.png" alt="MySQL InnoDB Cluster Configuration Diagram">
</div>


<h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Server / <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Client / <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shell / <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Router インストール</h4>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>公式ソフトウェア<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>から<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/Community%20Server">Community Server</a> / Client / <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shell/ <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Router を取得してインストールします。
以下から、<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>公式<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>設定用パッケージを取得してそこからインストールするか、直接ダウンロードしてインストールします。</p>

<ul>
    <li><a href="https://dev.mysql.com/downloads/repo/apt/" target="_blank" rel="noopener noreferrer">MySQL :: Download MySQL APT Repository</a></li>
    <li><a href="https://dev.mysql.com/downloads/mysql/" target="_blank" rel="noopener noreferrer">MySQL :: Download MySQL Community Server </a></li>
    <li><a href="https://dev.mysql.com/downloads/shell/" target="_blank" rel="noopener noreferrer">MySQL :: Download MySQL Shell</a></li>
    <li><a href="https://dev.mysql.com/downloads/router/" target="_blank" rel="noopener noreferrer">MySQL :: Download MySQL Router</a></li>
</ul>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>公式APT<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>設定用のパッケージを取得してインストールするには以下のコマンドを実行します。</p>

<pre class="terminal"> # curl -OL https://dev.mysql.com/get/mysql-apt-config_0.8.6-1_all.deb
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>公式APT<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>設定用のパッケージをインストールします。</p>

<pre class="terminal"> # dpkg -i mysql-apt-config*
 </pre>


<p>以下のような設定画面が出ますが、デフォルトのままOKを選択します。(後での変更も可能)</p>

<pre class="terminal"> パッケージの設定
 lqqqqqqqqqqqqqqqqqqqu mysql-apt-config を設定しています tqqqqqqqqqqqqqqqqqqqk
 x MySQL APT Repo features MySQL Server along with a variety of MySQL        x
 x components. You may select the appropriate product to choose the version  x
 x that you wish to receive.                                                 x
 x                                                                           x
 x Once you are satisfied with the configuration then select last option     x
 x 'Apply' to save the configuration. Advanced users can always change the   x
 x configurations later, depending on their own needs.                       x
 x                                                                           x
 x Which MySQL product do you wish to configure?                             x
 x                                                                           x
 x          MySQL Server (Currently selected: mysql-5.7)                     x
 x          MySQL Tools &amp; Connectors (Currently selected: Enabled)           x
 x          MySQL Preview Packages (Currently selected: Disabled)            x
 x          Ok                                                               x
 x                                                                           x
 x                                                                           x
 x                                  &lt;Ok&gt;                                     x
 x                                                                           x
 mqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqj
 </pre>


<p>パッケージリストを更新します。</p>

<pre class="terminal"> # apt update
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>、<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shell、<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Routerのパッケージをインストールします。</p>

<pre class="terminal"> # apt install mysql-server mysql-community-server mysql-shell mysql-router
 </pre>


<p>rootパスワードの入力を求められるので入力して設定します。</p>

<pre class="terminal"> パッケージの設定

                                                                                
  lqqqqqqqqqqqqqqqu mysql-community-server を設定しています tqqqqqqqqqqqqqqqqk
  x Please provide a strong password that will be set for the root account   x
  x of your MySQL database. Leave it blank to enable password less login     x
  x using UNIX socket based authentication.                                  x
  x                                                                          x
  x Enter root password:                                                     x
  x                                                                          x
  x ________________________________________________________________________ x
  x                                                                          x
  x                                  &lt;了解&gt;                                  x
  x                                                                          x
  mqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqj
 </pre>


<p>再度rootパスワードの入力を求められるので入力して設定します。</p>

<pre class="terminal"> パッケージの設定
                                                                                
                                                                                
    lqqqqqqqqqqqqqu mysql-community-server を設定しています tqqqqqqqqqqqqqqk
    x Now that you have selected a password for the root account, please   x
    x confirm by typing it again. Do not share the password with anyone.   x
    x                                                                      x
    x Re-enter root password:                                              x
    x                                                                      x
    x ____________________________________________________________________ x
    x                                                                      x
    x                                &lt;了解&gt;                                x
    x                                                                      x
    mqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqj
 </pre>


<p>以下のパッケージがインストールされていることを確認します。</p>

<pre class="terminal"> # dpkg -l | grep mysql
ii  mysql-apt-config                   0.8.6-1                            all          Auto configuration for MySQL APT Repo.
ii  mysql-client                       5.7.18-1ubuntu16.04                amd64        MySQL Client meta package depending on latest version
ii  mysql-common                       5.7.18-1ubuntu16.04                amd64        MySQL Common
ii  mysql-community-client             5.7.18-1ubuntu16.04                amd64        MySQL Client
ii  mysql-community-server             5.7.18-1ubuntu16.04                amd64        MySQL Server
ii  mysql-server                       5.7.18-1ubuntu16.04                amd64        MySQL Server meta package depending on latest version
 </pre>


<h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Group Replicationを使うための<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>準備</h4>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>をインストールしたら、以下のような3つの設定ファイルを準備します。
loose-group_replication_group_name には各サーバで一意な値 uuid を設定します。uuidgenコマンド等で作成したものを記載します。
Single-Primaryモード(1台のサーバのみ書き込み可能で、その他は読み取り専用)で稼働させるため、 <code>loose-group_replication_single_primary_mode = OFF</code>, <code>loose-group_replication_enforce_update_everywhere_checks = ON</code> の設定を削除 or <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%E1%A5%F3%A5%C8%A5%A2%A5%A6%A5%C8">コメントアウト</a>します。</p>

<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>の設定ファイル作成</h5>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#01の設定ファイル(<code>/etc/mysql/mysqld-01.cnf</code>)を作成します。</p>

<pre class="terminal"> [mysqld_safe]
socket          = /tmp/mysqld-01.sock
nice            = 0

[mysqld]
#
# * Basic Settings
#
server_id = 100001
user            = mysql
pid-file        = /tmp/mysqld-01.pid
socket          = /tmp/mysqld-01.sock
port            = 24801
basedir         = /usr
datadir         = /var/lib/mysql-01/
tmpdir          = /tmp
skip-external-locking
slow_query_log          = 1
slow_query_log_file     = /var/log/mysql/mysql-01-slow.log
log-error       = /var/log/mysql/mysql-01-error.log
log_timestamps = SYSTEM
explicit_defaults_for_timestamp = true
#
# * Binary-log setting
#
log_bin = /var/log/mysql/mysql-01-bin.log
binlog_format = ROW
#
# * General replication settings
#
gtid_mode = ON
enforce_gtid_consistency = ON
master_info_repository = TABLE
relay_log_info_repository = TABLE
binlog_checksum = NONE
log_slave_updates = ON
transaction_write_set_extraction = XXHASH64
loose-group_replication_bootstrap_group = OFF
loose-group_replication_start_on_boot = OFF
loose-group_replication_ssl_mode = DISABLED
loose-group_replication_recovery_use_ssl = 0
#
# * Replication group settings
#
loose-group_replication_group_name = "********-****-****-****-************"
loose-group_replication_ip_whitelist = "127.0.0.1"
loose-group_replication_group_seeds= "127.0.0.1:24901,127.0.0.1:24902,127.0.0.1:24903"
loose-group_replication_local_address= "127.0.0.1:24901"
#
# * Multi-primary mode settings
#
#loose-group_replication_single_primary_mode = OFF
#loose-group_replication_enforce_update_everywhere_checks = ON
#
# * Host specific replication configuration
#
report_host = "127.0.0.1"
report_port = 24801
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#02の設定ファイル(<code>/etc/mysql/mysqld-02.cnf</code>)を作成します。</p>

<pre class="terminal"> [mysqld_safe]
socket          = /tmp/mysqld-02.sock
nice            = 0

[mysqld]
#
# * Basic Settings
#
server_id = 100002
user            = mysql
pid-file        = /tmp/mysqld-02.pid
socket          = /tmp/mysqld-02.sock
port            = 24802
basedir         = /usr
datadir         = /var/lib/mysql-02/
tmpdir          = /tmp
skip-external-locking
slow_query_log          = 1
slow_query_log_file     = /var/log/mysql/mysql-02-slow.log
log-error       = /var/log/mysql/mysql-02-error.log
log_timestamps = SYSTEM
explicit_defaults_for_timestamp = true
#
# * Binary-log setting
#
log_bin = /var/log/mysql/mysql-02-bin.log
binlog_format = ROW
#
# * General replication settings
#
gtid_mode = ON
enforce_gtid_consistency = ON
master_info_repository = TABLE
relay_log_info_repository = TABLE
binlog_checksum = NONE
log_slave_updates = ON
transaction_write_set_extraction = XXHASH64
loose-group_replication_bootstrap_group = OFF
loose-group_replication_start_on_boot = OFF
loose-group_replication_ssl_mode = DISABLED
loose-group_replication_recovery_use_ssl = 0
#
# * Replication group settings
#
loose-group_replication_group_name = "********-****-****-****-************"
loose-group_replication_ip_whitelist = "127.0.0.1"
loose-group_replication_group_seeds= "127.0.0.1:24901,127.0.0.1:24902,127.0.0.1:24903"
loose-group_replication_local_address= "127.0.0.1:24902"
#
# * Multi-primary mode settings
#
#loose-group_replication_single_primary_mode = OFF
#loose-group_replication_enforce_update_everywhere_checks = ON
#
# * Host specific replication configuration
#
report_host = "127.0.0.1"
report_port = 24801
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#03の設定ファイル(<code>/etc/mysql/mysqld-03.cnf</code>)を作成します。</p>

<pre class="terminal"> [mysqld_safe]
socket          = /tmp/mysqld-03.sock
nice            = 0

[mysqld]
#
# * Basic Settings
#
server_id = 100003
user            = mysql
pid-file        = /tmp/mysqld-03.pid
socket          = /tmp/mysqld-03.sock
port            = 24803
basedir         = /usr
datadir         = /var/lib/mysql-01/
tmpdir          = /tmp
skip-external-locking
slow_query_log          = 1
slow_query_log_file     = /var/log/mysql/mysql-03-slow.log
log-error       = /var/log/mysql/mysql-03-error.log
log_timestamps = SYSTEM
explicit_defaults_for_timestamp = true
#
# * Binary-log setting
#
log_bin = /var/log/mysql/mysql-03-bin.log
binlog_format = ROW
#
# * General replication settings
#
gtid_mode = ON
enforce_gtid_consistency = ON
master_info_repository = TABLE
relay_log_info_repository = TABLE
binlog_checksum = NONE
log_slave_updates = ON
transaction_write_set_extraction = XXHASH64
loose-group_replication_bootstrap_group = OFF
loose-group_replication_start_on_boot = OFF
loose-group_replication_ssl_mode = DISABLED
loose-group_replication_recovery_use_ssl = 0
#
# * Replication group settings
#
loose-group_replication_group_name = "********-****-****-****-************"
loose-group_replication_ip_whitelist = "127.0.0.1"
loose-group_replication_group_seeds= "127.0.0.1:24901,127.0.0.1:24902,127.0.0.1:24903"
loose-group_replication_local_address= "127.0.0.1:24903"
#
# * Multi-primary mode settings
#
#loose-group_replication_single_primary_mode = OFF
#loose-group_replication_enforce_update_everywhere_checks = ON
#
# * Host specific replication configuration
#
report_host = "127.0.0.1"
report_port = 24801
 </pre>


<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>データ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ初期化</h5>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>データ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを初期化します。
単一ホスト内に異なる<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>を起動する必要があるため、データ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを分けます。
ここでは <code>/var/lib/mysql-{01,02,03}</code> としていますが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a>ではAppArmorによって<code>/var/lib/mysql</code>以外をデータ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リとて初期化しようとするとエラーになります。
まずは <code>/etc/apparmor.d/usr.sbin.mysqld</code> へ <code>/var/lib/mysql-{01,02,03}</code> をデータ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リとして扱えるように許可設定を行います。</p>

<pre class="terminal"> # diff -u /etc/apparmor.d/usr.sbin.mysqld{.org,}
--- /etc/apparmor.d/usr.sbin.mysqld.org YYYY-MM-DD hh:mm:ss.000000000 +0900
+++ /etc/apparmor.d/usr.sbin.mysqld     YYYY-MM-DD hh:mm:ss.********* +0900
@@ -48,6 +48,10 @@
 # Allow data dir access
   /var/lib/mysql/ r,
   /var/lib/mysql/** rwk,
+  /var/lib/mysql-01/ r,
+  /var/lib/mysql-01/** rwk,
+  /var/lib/mysql-02/ r,
+  /var/lib/mysql-02/** rwk,
+  /var/lib/mysql-03/ r,
+  /var/lib/mysql-03/** rwk,

 </pre>


<p>設定を反映します。</p>

<pre class="terminal"> # apparmor_parser -r /etc/apparmor.d/usr.sbin.mysqld
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#01のデータ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを初期化します。</p>

<pre class="terminal"> # install -d -o mysql -g mysql -m 700 /var/lib/mysql-01
# mysqld --defaults-file=/etc/mysql/mysqld-01.cnf --initialize --user=mysql --explicit_defaults_for_timestamp
(ログファイルにテンポラリパスワードが記録されているのでメモ)
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#02のデータ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを初期化します。</p>

<pre class="terminal"> # install -d -o mysql -g mysql -m 700 /var/lib/mysql-02
# mysqld --defaults-file=/etc/mysql/mysqld-02.cnf --initialize --user=mysql --explicit_defaults_for_timestamp
(ログファイルにテンポラリパスワードが記録されているのでメモ)
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#03のデータ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを初期化します。</p>

<pre class="terminal"> # install -d -o mysql -g mysql -m 700 /var/lib/mysql-03
# mysqld --defaults-file=/etc/mysql/mysqld-03.cnf --initialize --user=mysql --explicit_defaults_for_timestamp
(ログファイルにテンポラリパスワードが記録されているのでメモ)
 </pre>


<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>起動</h5>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#01を起動します。</p>

<pre class="terminal"> # mysqld --defaults-file=/etc/mysql/mysqld-01.cnf --user=mysql &amp;
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#02を起動します。</p>

<pre class="terminal"> # mysqld --defaults-file=/etc/mysql/mysqld-02.cnf --user=mysql &amp;
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#03を起動します。</p>

<pre class="terminal"> # mysqld --defaults-file=/etc/mysql/mysqld-03.cnf --user=mysql &amp;
 </pre>


<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> rootパスワード設定</h5>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#01のrootパスワードを再設定します。</p>

<pre class="terminal"> # mysql -u root -P 24801 --protocol=tcp -h localhost -p

mysql#01&gt; set password for root@localhost=password('********');
Query OK, 0 rows affected, 1 warning (0.01 sec)
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#02のrootパスワードを再設定します。</p>

<pre class="terminal"> # mysql -u root -P 24802 --protocol=tcp -h localhost -p

mysql#02&gt; set password for root@localhost=password('********');
Query OK, 0 rows affected, 1 warning (0.01 sec)
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#03のrootパスワードを再設定します。</p>

<pre class="terminal"> # mysql -u root -P 24803 --protocol=tcp -h localhost -p

mysql#03&gt; set password for root@localhost=password('********');
Query OK, 0 rows affected, 1 warning (0.01 sec)
 </pre>


<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>用ユーザ作成</h5>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#01に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>用のユーザを作成します。</p>

<pre class="terminal"> mysql#01&gt; SET SQL_LOG_BIN=0;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#01&gt; GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%' IDENTIFIED BY '********';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#01&gt; SET SQL_LOG_BIN=1;
Query OK, 0 rows affected, 1 warning (0.00 sec)
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#02に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>用のユーザを作成します。</p>

<pre class="terminal"> mysql#02&gt; SET SQL_LOG_BIN=0;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#02&gt; GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%' IDENTIFIED BY '********';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#02&gt; SET SQL_LOG_BIN=1;
Query OK, 0 rows affected, 1 warning (0.00 sec)
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#03に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>用のユーザを作成します。</p>

<pre class="terminal"> mysql#03&gt; SET SQL_LOG_BIN=0;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#03&gt; GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%' IDENTIFIED BY '********';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#03&gt; SET SQL_LOG_BIN=1;
Query OK, 0 rows affected, 1 warning (0.00 sec)
 </pre>


<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>設定</h5>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#01に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>接続用の設定を行います。</p>

<pre class="terminal"> mysql#01&gt; RESET MASTER;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#01&gt; CHANGE MASTER TO MASTER_USER='repl', MASTER_PASSWORD='********' FOR CHANNEL 'group_replication_recovery';
Query OK, 0 rows affected, 1 warning (0.00 sec)
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#02に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>接続用の設定を行います。</p>

<pre class="terminal"> mysql#02&gt; RESET MASTER;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#02&gt; CHANGE MASTER TO MASTER_USER='repl', MASTER_PASSWORD='********' FOR CHANNEL 'group_replication_recovery';
Query OK, 0 rows affected, 1 warning (0.00 sec)
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#03に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>接続用の設定を行います。</p>

<pre class="terminal"> mysql#03&gt; RESET MASTER;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#03&gt; CHANGE MASTER TO MASTER_USER='repl', MASTER_PASSWORD='********' FOR CHANNEL 'group_replication_recovery';
Query OK, 0 rows affected, 1 warning (0.00 sec)
 </pre>


<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Group Replication<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>有効化</h5>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#01のGroup Replication<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>を有効化します。</p>

<pre class="terminal"> mysql#01&gt; select * from information_schema.plugins where plugin_name='group_replication'\G
Empty set (0.00 sec)

mysql#01&gt; INSTALL PLUGIN group_replication SONAME 'group_replication.so';
Query OK, 0 rows affected (0.00 sec)

mysql#01&gt; select * from information_schema.plugins where plugin_name='group_replication'\G
*************************** 1. row ***************************
           PLUGIN_NAME: group_replication
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: GROUP REPLICATION
   PLUGIN_TYPE_VERSION: 1.1
        PLUGIN_LIBRARY: group_replication.so
PLUGIN_LIBRARY_VERSION: 1.7
         PLUGIN_AUTHOR: ORACLE
    PLUGIN_DESCRIPTION: Group Replication (1.0.0)
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: ON
1 row in set (0.00 sec)
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#02のGroup Replication<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>を有効化します。</p>

<pre class="terminal"> mysql#02&gt; select * from information_schema.plugins where plugin_name='group_replication'\G
Empty set (0.00 sec)

mysql#02&gt; INSTALL PLUGIN group_replication SONAME 'group_replication.so';
Query OK, 0 rows affected (0.00 sec)

mysql#02&gt; select * from information_schema.plugins where plugin_name='group_replication'\G
*************************** 1. row ***************************
           PLUGIN_NAME: group_replication
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: GROUP REPLICATION
   PLUGIN_TYPE_VERSION: 1.1
        PLUGIN_LIBRARY: group_replication.so
PLUGIN_LIBRARY_VERSION: 1.7
         PLUGIN_AUTHOR: ORACLE
    PLUGIN_DESCRIPTION: Group Replication (1.0.0)
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: ON
1 row in set (0.00 sec)
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#03のGroup Replication<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>を有効化します。</p>

<pre class="terminal"> mysql#03&gt; select * from information_schema.plugins where plugin_name='group_replication'\G
Empty set (0.00 sec)

mysql#03&gt; INSTALL PLUGIN group_replication SONAME 'group_replication.so';
Query OK, 0 rows affected (0.00 sec)

mysql#03&gt; select * from information_schema.plugins where plugin_name='group_replication'\G
*************************** 1. row ***************************
           PLUGIN_NAME: group_replication
        PLUGIN_VERSION: 1.0
         PLUGIN_STATUS: ACTIVE
           PLUGIN_TYPE: GROUP REPLICATION
   PLUGIN_TYPE_VERSION: 1.1
        PLUGIN_LIBRARY: group_replication.so
PLUGIN_LIBRARY_VERSION: 1.7
         PLUGIN_AUTHOR: ORACLE
    PLUGIN_DESCRIPTION: Group Replication (1.0.0)
        PLUGIN_LICENSE: GPL
           LOAD_OPTION: ON
1 row in set (0.00 sec)
 </pre>


<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Cluster管理用ユーザ作成</h5>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Cluster管理用のユーザを作成します。</p>

<pre class="terminal"> mysql#01&gt; SET SQL_LOG_BIN=0;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#01&gt; GRANT SUPER, FILE, GRANT OPTION, RELOAD, SHUTDOWN, PROCESS,REPLICATION SLAVE, REPLICATION CLIENT, CREATE USER ON *.* TO icroot@'%' IDENTIFIED BY '********' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#01&gt; GRANT SELECT, INSERT, UPDATE, DELETE ON `mysql`.* TO 'icroot'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#01&gt; GRANT SELECT ON `performance_schema`.* TO 'icroot'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#01&gt; GRANT SELECT ON *.* TO 'icroot'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#01&gt; GRANT ALL PRIVILEGES ON `mysql_innodb_cluster_metadata`.* TO 'icroot'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#01&gt; SET SQL_LOG_BIN=1;
Query OK, 0 rows affected, 1 warning (0.00 sec)
 </pre>


<pre class="terminal"> mysql#02&gt; SET SQL_LOG_BIN=0;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#02&gt; GRANT SUPER, FILE, GRANT OPTION, RELOAD, SHUTDOWN, PROCESS,REPLICATION SLAVE, REPLICATION CLIENT, CREATE USER ON *.* TO icroot@'%' IDENTIFIED BY '********';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#02&gt; GRANT SELECT, INSERT, UPDATE, DELETE ON `mysql`.* TO 'icroot'@'%';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#02&gt; GRANT SELECT ON `performance_schema`.* TO 'icroot'@'%';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#02&gt; GRANT SELECT ON *.* TO 'icroot'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#02&gt; GRANT ALL PRIVILEGES ON `mysql_innodb_cluster_metadata`.* TO 'icroot'@'%';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#02&gt; SET SQL_LOG_BIN=1;
Query OK, 0 rows affected, 1 warning (0.00 sec)
 </pre>


<pre class="terminal"> mysql#03&gt; SET SQL_LOG_BIN=0;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#03&gt; GRANT SUPER, FILE, GRANT OPTION, RELOAD, SHUTDOWN, PROCESS,REPLICATION SLAVE, REPLICATION CLIENT, CREATE USER ON *.* TO icroot@'%' IDENTIFIED BY '********';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#03&gt; GRANT SELECT, INSERT, UPDATE, DELETE ON `mysql`.* TO 'icroot'@'%';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#03&gt; GRANT SELECT ON `performance_schema`.* TO 'icroot'@'%';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#03&gt; GRANT SELECT ON *.* TO 'icroot'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#03&gt; GRANT ALL PRIVILEGES ON `mysql_innodb_cluster_metadata`.* TO 'icroot'@'%';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql#03&gt; SET SQL_LOG_BIN=1;
Query OK, 0 rows affected, 1 warning (0.00 sec)
 </pre>


<h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> ShellでのGroup Replication設定</h4>


<p>まず、<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shellを使ってGroup Replicationが稼働できる状態にしておくため、<a href="https://blog.dshimizu.jp/article/439" target="_blank" rel="noopener noreferrer">こちらの記事</a>にある<code>/etc/mysql/mysqld-01.cnf</code>, <code>/etc/mysql/mysqld-02.cnf</code>, <code>/etc/mysql/mysqld-03.cnf</code>を準備して、3つの<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を起動します。
Group Replicationが動いている場合は<code>stop group_replication</code>しておきます。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shellを起動するために<code>mysqlsh</code>コマンドを実行します。
※root権限化で実行します。</p>

<pre class="terminal"> # mysqlsh
Welcome to MySQL Shell 1.0.9

Copyright (c) 2016, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type '\help', '\h' or '\?' for help, type '\quit' or '\q' to exit.

Currently in JavaScript mode. Use \sql to switch to SQL mode and execute queries.
mysql-js&gt;
 </pre>


<p>Group Replication用の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を設定します。
設定には <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shell の AdminAPI という<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>を設定および管理するための<a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a>を使います。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shell上の <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B0%A5%ED%A1%BC%A5%D0%A5%EB%CA%D1%BF%F4">グローバル変数</a> 'dba' を使ってAdminAPI機能にアクセスして<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>を操作・管理します。</p>

<p>configureLocalInstanceというクラスを使います。これは<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>の構成をレビューして、グループ複製および<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>での使用に有効かどうかを識別します。 操作の結果を含む<a class="keyword" href="http://d.hatena.ne.jp/keyword/JSON">JSON</a>オブジェクトが返されます。</p>

<pre class="terminal"> mysql-js&gt; dba.configureLocalInstance('root@localhost:24801')
Please provide the password for 'root@localhost:24801':

Detecting the configuration file...
Found configuration file at standard location: /etc/mysql/mysql.conf.d/mysqld.cnf
Do you want to modify this file? [Y|n]: n    # &lt;-デフォルトのコンフィグファイルを使わないので n を入力します。
Default file not found at the standard locations.
Please specify the path to the MySQL configuration file: /etc/mysql/mysqld-01.cnf    # &lt;-MySQL#01の設定ファイルを指定
MySQL user 'root' cannot be verified to have access to other hosts in the network.

1) Create root@% with necessary grants
2) Create account with different name
3) Continue without creating account
4) Cancel
Please select an option [1]: 3    # &lt;- 上記でユーザを作成していない場合はここで 2 を選択して作成
Validating instance...

The instance 'localhost:24801' is valid for Cluster usage
You can now use it in an InnoDB Cluster.

{
    "status": "ok" 
}
 </pre>


<pre class="terminal"> mysql-js&gt; dba.configureLocalInstance('root@localhost:24802')
Please provide the password for 'root@localhost:24802':

Detecting the configuration file...
Found configuration file at standard location: /etc/mysql/mysql.conf.d/mysqld.cnf
Do you want to modify this file? [Y|n]: n    # &lt;-デフォルトのコンフィグファイルを使わないので n を入力します。
Default file not found at the standard locations.
Please specify the path to the MySQL configuration file: /etc/mysql/mysqld-02.cnf    # &lt;-MySQL#01の設定ファイルを指定
MySQL user 'root' cannot be verified to have access to other hosts in the network.

1) Create root@% with necessary grants
2) Create account with different name
3) Continue without creating account
4) Cancel
Please select an option [1]: 3    # &lt;- 上記でユーザを作成していない場合はここで 2 を選択して作成
Validating instance...

The instance 'localhost:24802' is valid for Cluster usage
You can now use it in an InnoDB Cluster.

{
    "status": "ok" 
}
 </pre>


<pre class="terminal"> mysql-js&gt; dba.configureLocalInstance('root@localhost:24803')
Please provide the password for 'root@localhost:24803':

Detecting the configuration file...
Found configuration file at standard location: /etc/mysql/mysql.conf.d/mysqld.cnf
Do you want to modify this file? [Y|n]: n    # &lt;-デフォルトのコンフィグファイルを使わないので n を入力します。
Default file not found at the standard locations.
Please specify the path to the MySQL configuration file: /etc/mysql/mysqld-03.cnf    # &lt;-MySQL#01の設定ファイルを指定
MySQL user 'root' cannot be verified to have access to other hosts in the network.

1) Create root@% with necessary grants
2) Create account with different name
3) Continue without creating account
4) Cancel
Please select an option [1]: 3    # &lt;- 上記でユーザを作成していない場合はここで 2 を選択して作成
Validating instance...

The instance 'localhost:24803' is valid for Cluster usage
You can now use it in an InnoDB Cluster.

{
    "status": "ok" 
}
 </pre>


<p>3つの<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>への接続情報を変数 i1, i2, i3 へ格納します。</p>

<pre class="terminal"> mysql-js&gt; var i1 = 'icroot@192.168.10.1:24801'
mysql-js&gt; var i2 = 'icroot@192.168.10.1:24802'
mysql-js&gt; var i3 = 'icroot@192.168.10.1:24803'
 </pre>


<p>shell関数のconnectクラスを使って、いずれかの<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>とのコネクションを確立します。</p>

<pre class="terminal"> mysql-js&gt; shell.connect(i1)
Please provide the password for 'icroot@192.168.10.1:24801':
Creating a Session to 'icroot@192.168.10.1:24801'
Classic Session successfully established. No default schema selected.
 </pre>


<p>dba関数のcreateClusterクラスを使って、新規に<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーを作成します。
引数には<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>の名称を入力します。ここではClusterDevとしています。</p>

<pre class="terminal"> mysql-js&gt; var cluster = dba.createCluster('ClusterDev');
A new InnoDB cluster will be created on instance 'icroot@192.168.10.1:24801'.

Creating InnoDB cluster 'ClusterDev' on 'icroot@192.168.10.1:24801'...
Adding Seed Instance...

Cluster successfully created. Use Cluster.addInstance() to add MySQL instances.
At least 3 instances are needed for the cluster to be able to withstand up to
one server failure.
 </pre>


<p>cluster変数にaddInstanceクラスを使って、残りの<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>に追加します。</p>

<pre class="terminal"> mysql-js&gt; cluster.addInstance(i2);
A new instance will be added to the InnoDB cluster. Depending on the amount of
data on the cluster this might take from a few seconds to several hours.

Please provide the password for 'icroot@192.168.10.1:24802':
Adding instance to the cluster ...

The instance 'icroot@192.168.10.1:24802' was successfully added to the cluster.
 </pre>


<pre class="terminal"> mysql-js&gt; cluster.addInstance(i3);
A new instance will be added to the InnoDB cluster. Depending on the amount of
data on the cluster this might take from a few seconds to several hours.

Please provide the password for 'icroot@192.168.10.1:24803':
Adding instance to the cluster ...

The instance 'icroot@192.168.10.1:24803' was successfully added to the cluster.
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>のステータスを確認します。</p>

<pre class="terminal"> mysql-js&gt; cluster.status();
{
    "clusterName": "ClusterDev",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "192.168.10.1:24801",
        "status": "OK",
        "statusText": "Cluster is ONLINE and can tolerate up to ONE failure.",
        "topology": {
            "192.168.10.1:24801": {
                "address": "192.168.10.1:24801",
                "mode": "R/W",
                "readReplicas": {},
                "role": "HA",
                "status": "ONLINE"
            },
            "192.168.10.1:24802": {
                "address": "192.168.10.1:24802",
                "mode": "R/O",
                "readReplicas": {},
                "role": "HA",
                "status": "ONLINE"
            },
            "192.168.10.1:24803": {
                "address": "192.168.10.1:24803",
                "mode": "R/O",
                "readReplicas": {},
                "role": "HA",
                "status": "ONLINE"
            }
        }
    }
}
 </pre>


<h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Router の設定</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Routerの設定ファイルを生成します。</p>

<pre class="terminal"> # mysqlrouter --bootstrap icroot@192.168.10.1:24801 --user mysqlrouter
Please enter MySQL password for icroot:     # &lt;-パスワードを入力

Bootstrapping system MySQL Router instance...
MySQL Router  has now been configured for the InnoDB cluster 'ClusterDev'.

The following connection information can be used to connect to the cluster.

Classic MySQL protocol connections to cluster 'ClusterDev':
- Read/Write Connections: localhost:6446
- Read/Only Connections: localhost:6447

X protocol connections to cluster 'ClusterDev':
- Read/Write Connections: localhost:64460
- Read/Only Connections: localhost:64470
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Routerを起動します。</p>

<pre class="terminal"> # sudo -u mysqlrouter /usr/bin/mysqlrouter -c /etc/mysqlrouter/mysqlrouter.conf &amp;
 </pre>


<h4>確認</h4>


<p>マスターには<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Routerの6446ポートを使って接続可能になっており、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BB%A5%AB%A5%F3%A5%C0%A5%EA">セカンダリ</a>には<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Routerの6447ポートを使って接続可能になっています。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BB%A5%AB%A5%F3%A5%C0%A5%EA">セカンダリ</a>は2台あるので、いずれかにロードバランスされます。</p>

<p><strong>マスターへの接続(<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#01:24801)</strong></p>

<pre class="terminal"> # mysql -u icroot --protocol=tcp -P 6446 -h 192.168.10.1 -p -e "select @@port;"
Enter password:
+--------+
| @@port |
+--------+
|  24801 |
+--------+
 </pre>


<p><strong><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BB%A5%AB%A5%F3%A5%C0%A5%EA">セカンダリ</a>への接続(<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#02:24802 or <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>#03:24803へのバランシング)</strong></p>

<pre class="terminal"> # mysql -u icroot --protocol=tcp -P 6447 -h 192.168.10.1 -p -e "select @@port;"
Enter password:
+--------+
| @@port |
+--------+
|  24802 |
+--------+
 </pre>


<pre class="terminal"> # mysql -u icroot --protocol=tcp -P 6447 -h 192.168.10.1 -p -e "select @@port;"
Enter password:
+--------+
| @@port |
+--------+
|  24803 |
+--------+
 </pre>


<h3>まとめ</h3>


<p>これで <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Cluster環境を構築できました。
次は動作検証について書こうと思います。</p>

<h3>参考</h3>


<ul>
    <li><a href="https://dev.mysql.com/doc/refman/5.7/en/mysql-innodb-cluster-userguide.html" target="_blank" rel="noopener noreferrer">MySQL :: MySQL 5.7 Reference Manual :: 20 InnoDB Cluster</a></li>
    <li><a href="https://dev.mysql.com/doc/refman/5.7/en/mysql-shell.html" target="_blank" rel="noopener noreferrer">MySQL :: MySQL 5.7 Reference Manual :: 3.8 MySQL Shell User Guide</a></li>
    <li><a href="https://www.mysql.com/jp/products/enterprise/router.html" target="_blank" rel="noopener noreferrer">MySQL :: MySQL Router</a></li>
    <li><a href="http://mysqlserverteam.com/mysql-innodb-cluster-ga/" target="_blank" rel="noopener noreferrer">MySQL InnoDB Cluster GA is Available Now! | MySQL Server Blog</a></li>
    <li><a href="https://bugs.mysql.com/bug.php?id=85567" target="_blank" rel="noopener noreferrer">MySQL Bugs: #85567: cluster.rejoinInstance() doesn't use the user in the connection string parameter</a></li>
    <li><a href="https://bugs.mysql.com/bug.php?id=86050" target="_blank" rel="noopener noreferrer">MySQL Bugs: #86050: mysqlrouter does not start with default mysqlrouter --bootstrap</a></li>
    <li><a href="http://mysqlserverteam.com/mysql-innodb-cluster-a-hands-on-tutorial/" target="_blank" rel="noopener noreferrer">MySQL InnoDB Cluster – A Hands on Tutorial | MySQL Server Blog</a></li>
</ul>

</body>
