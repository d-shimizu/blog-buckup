---
layout: post
Categories:  ["tech"]
Description:  " MySQL ShellからMySQL InnoDB Clusterを作ろうとした時に以下のようなエラーが出ましたので、その原因の調査と対応手順をまとめます。icrootと言うのは InnoDB Cluster 管理用のユーザとします。  "
Tags: ["MySQL", "tech"]
date: "2017-09-16T22:50:00+09:00"
title: "MySQL InnoDB Cluster初期作成時に Dba.createCluster"
author: "dshimizu"
archive: ["2017"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shellから<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Clusterを作ろうとした時に以下のようなエラーが出ましたので、その原因の調査と対応手順をまとめます。
icrootと言うのは <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Cluster 管理用のユーザとします。</p>

<pre class="terminal"> mysql-js&gt; cluster = dba.createCluster('ClusterDev');
A new InnoDB cluster will be created on instance 'icroot@192.168.10.65:3306'.

Warning: The instance configuration needs to be changed in order to
create an InnoDB cluster. To see which changes will be made, please
use the dba.checkInstanceConfiguration() function before confirming
to change the configuration.

Should the configuration be changed accordingly? [y|N]: y

Creating InnoDB cluster 'ClusterDev' on 'icroot@192.168.10.65:3306'...
Dba.createCluster: ERROR: 1 table(s) do not have a Primary Key or Primary Key Equivalent (non-null unique key).
ERROR: Error starting cluster: The operation could not continue due to the following requirements not being met:
Non-compatible tables found in database. (RuntimeError)
 </pre>

</body>

<!-- more -->

<body>
<h3>環境</h3>


<ul>
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> 16.04</li>
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Server 5.7.19(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Oracle">Oracle</a>公式<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/deb">deb</a>パッケージ)</li>
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shell 1.0.10(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Oracle">Oracle</a>公式<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/deb">deb</a>パッケージ)</li>
</ul>


<h3>調査と解決策</h3>


<p>どうもPKのないテーブルを扱おうとしているためにエラーとなっているようです。<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> ClusterはPKのないテーブルをサポートしていないので、PKのないテーブルがある場合にこのエラーが出るのは正常な動作と思います。
しかし、今回はデフォルトで存在する以下のデータベースしかありません。sysデータベースの中のテーブルにはPKのないテーブルがありますが、あらかじめ存在するシステムテーブルが原因なのか…</p>

<pre class="terminal"> &gt; show databases;
+-------------------------------+
| Database                      |
+-------------------------------+
| information_schema            |
| mysql                         |
| performance_schema            |
| sys                           |
+-------------------------------+
7 rows in set (0.00 sec)
 </pre>


<p>とりあえずエラーメッセージで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B0%A5%B0%A4%EB">ググる</a>と以下がヒットしました。</p>

<ul>
    <li><a href="https://forums.mysql.com/read.php?177,659302,659616#msg-659616" target="_blank" rel="noopener noreferrer">MySQL :: Re: Dba.createCluster: ERROR: 1 table(s) do not have a Primary Key or Primary Key Equiva...</a></li>
</ul>


<p>どうもsys.sys_configに対する権限が足りないので<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Clusterを扱うユーザの権限を見直せとか言われていているような感じがします。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Clusterでのユーザ権限に関するドキュメントを見てみます。</p>

<ul>
    <li><a href="https://dev.mysql.com/doc/refman/5.7/en/mysql-innodb-cluster-production-deployment.html" target="_blank" rel="noopener noreferrer">MySQL :: MySQL 5.7 Reference Manual :: 20.2.5 Production Deployment of InnoDB Cluster</a></li>
</ul>


<p>「<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E1%A5%BF%A5%C7%A1%BC%A5%BF">メタデータ</a>テーブルに完全な読み書き権限を割り当てる必要がある」とのことで、以下が必要なようです。</p>

<pre class="terminal"> GRANT ALL PRIVILEGES ON mysql_innodb_cluster_metadata.* TO your_user@'%' WITH GRANT OPTION;
GRANT RELOAD, SHUTDOWN, PROCESS, FILE, SUPER, REPLICATION SLAVE, REPLICATION CLIENT, CREATE USER ON *.* TO your_user@'%' WITH GRANT OPTION;
GRANT SELECT ON performance_schema.* TO your_user@'%' WITH GRANT OPTION;
GRANT SELECT ON *.* TO your_user@'%' WITH GRANT OPTION;
GRANT SELECT, INSERT, UPDATE, DELETE ON mysql.* TO your_user@'%' WITH GRANT OPTION;
 </pre>


<p>実際に存在するユーザ権限をみてみます。</p>

<pre class="terminal"> root@mysql1 &gt; show grants for icroot@`%`;
+-------------------------------------------------------------------------------------------------------------------------------------------+
| Grants for icroot@%                                                                                                                       |
+-------------------------------------------------------------------------------------------------------------------------------------------+
| GRANT RELOAD, SHUTDOWN, PROCESS, FILE, SUPER, REPLICATION SLAVE, REPLICATION CLIENT, CREATE USER ON *.* TO 'icroot'@'%' WITH GRANT OPTION |
| GRANT SELECT, INSERT, UPDATE, DELETE ON `mysql`.* TO 'icroot'@'%' WITH GRANT OPTION                                                       |
| GRANT SELECT ON `performance_schema`.* TO 'icroot'@'%' WITH GRANT OPTION                                                                  |
| GRANT ALL PRIVILEGES ON `mysql_innodb_cluster_metadata`.* TO 'icroot'@'%' WITH GRANT OPTION                                               |
+-------------------------------------------------------------------------------------------------------------------------------------------+
4 rows in set (0.00 sec)
 </pre>


<p>各DB/テーブルへの参照権限(<code>GRANT SELECT ON <em>.</em> TO your_user@'%' WITH GRANT OPTION;</code>)にあたる部分がないようです。</p>

<p>該当のユーザは<code>mysqlsh</code>から<code>dba.configureLocalInstance()</code>を使って作成したのですが、作成されたユーザには <code>sys.sys_config</code> への参照権限がついていないみたいです。</p>

<p>とりあえず全ての<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>ノードで <code><em>.</em></code> への参照権限を付与します。</p>

<pre class="terminal"> root@mysql1 &gt; grant select on *.* TO icroot@'%' WITH GRANT OPTION;

root@mysql1 &gt; show grants for icroot@'%';
+---------------------------------------------------------------------------------------------------------------------------------------------------+
| Grants for icroot@%                                                                                                                               |
+---------------------------------------------------------------------------------------------------------------------------------------------------+
| GRANT SELECT, RELOAD, SHUTDOWN, PROCESS, FILE, SUPER, REPLICATION SLAVE, REPLICATION CLIENT, CREATE USER ON *.* TO 'icroot'@'%' WITH GRANT OPTION |
| GRANT SELECT, INSERT, UPDATE, DELETE ON `mysql`.* TO 'icroot'@'%' WITH GRANT OPTION                                                               |
| GRANT SELECT ON `sys`.* TO 'icroot'@'%' WITH GRANT OPTION                                                                                         |
| GRANT SELECT ON `performance_schema`.* TO 'icroot'@'%' WITH GRANT OPTION                                                                          |
| GRANT ALL PRIVILEGES ON `mysql_innodb_cluster_metadata`.* TO 'icroot'@'%' WITH GRANT OPTION                                                       |
+---------------------------------------------------------------------------------------------------------------------------------------------------+
5 rows in set (0.00 sec)

root@mysql2 &gt; grant select on *.* TO icroot@'%' WITH GRANT OPTION;

root@mysql2 &gt; show grants for icroot@'%';
+---------------------------------------------------------------------------------------------------------------------------------------------------+
| Grants for icroot@%                                                                                                                               |
+---------------------------------------------------------------------------------------------------------------------------------------------------+
| GRANT SELECT, RELOAD, SHUTDOWN, PROCESS, FILE, SUPER, REPLICATION SLAVE, REPLICATION CLIENT, CREATE USER ON *.* TO 'icroot'@'%' WITH GRANT OPTION |
| GRANT SELECT, INSERT, UPDATE, DELETE ON `mysql`.* TO 'icroot'@'%' WITH GRANT OPTION                                                               |
| GRANT SELECT ON `sys`.* TO 'icroot'@'%' WITH GRANT OPTION                                                                                         |
| GRANT SELECT ON `performance_schema`.* TO 'icroot'@'%' WITH GRANT OPTION                                                                          |
| GRANT ALL PRIVILEGES ON `mysql_innodb_cluster_metadata`.* TO 'icroot'@'%' WITH GRANT OPTION                                                       |
+---------------------------------------------------------------------------------------------------------------------------------------------------+
5 rows in set (0.00 sec)

root@mysql3 &gt; grant select on *.* TO icroot@'%' WITH GRANT OPTION;

root@mysql3 &gt; show grants for icroot@'%';
+---------------------------------------------------------------------------------------------------------------------------------------------------+
| Grants for icroot@%                                                                                                                               |
+---------------------------------------------------------------------------------------------------------------------------------------------------+
| GRANT SELECT, RELOAD, SHUTDOWN, PROCESS, FILE, SUPER, REPLICATION SLAVE, REPLICATION CLIENT, CREATE USER ON *.* TO 'icroot'@'%' WITH GRANT OPTION |
| GRANT SELECT, INSERT, UPDATE, DELETE ON `mysql`.* TO 'icroot'@'%' WITH GRANT OPTION                                                               |
| GRANT SELECT ON `sys`.* TO 'icroot'@'%' WITH GRANT OPTION                                                                                         |
| GRANT SELECT ON `performance_schema`.* TO 'icroot'@'%' WITH GRANT OPTION                                                                          |
| GRANT ALL PRIVILEGES ON `mysql_innodb_cluster_metadata`.* TO 'icroot'@'%' WITH GRANT OPTION                                                       |
+---------------------------------------------------------------------------------------------------------------------------------------------------+
5 rows in set (0.00 sec)
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>作成を試してみると成功しました。</p>

<pre class="terminal"> mysql-js&gt; var cluster = dba.createCluster('devCluster');
A new InnoDB cluster will be created on instance 'icroot@192.168.10.65:3306'.

Creating InnoDB cluster 'devCluster' on 'icroot@192.168.10.65:3306'...
Adding Seed Instance...

Cluster successfully created. Use Cluster.addInstance() to add MySQL instances.
At least 3 instances are needed for the cluster to be able to withstand up to
one server failure.
 </pre>


<h3>まとめ</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shellから<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Clusterを作ろうとした時に <code>Dba.createCluster: ERROR: 1 table(s) do not have a Primary Key or Primary Key Equivalent (non-null unique key)</code> というエラーが出た事象に関しての調査と対応手順を書きました。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shell のエラーメッセージはちょっとわかりづらいです。</p>

<h3>参考</h3>


<ul>
    <li><a href="https://forums.mysql.com/read.php?177,659302,659616#msg-659616" target="_blank" rel="noopener noreferrer">MySQL :: Re: Dba.createCluster: ERROR: 1 table(s) do not have a Primary Key or Primary Key Equiva...</a></li>
    <li><a href="https://dev.mysql.com/doc/refman/5.7/en/mysql-innodb-cluster-production-deployment.html" target="_blank" rel="noopener noreferrer">MySQL :: MySQL 5.7 Reference Manual :: 20.2.5 Production Deployment of InnoDB Cluster</a></li>
</ul>

</body>
