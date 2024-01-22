---
layout: post
Categories:  ["tech"]
Description:  " Group Replication は、マルチマスターのレプリケーション構成を行うためのMySQLの機能で、5.7.17からプラグインとして提供され利用可能となっています。  Group Replication ではレプリケーションを構成"
Tags: ["MySQL", "tech"]
date: "2017-08-18T01:20:00+09:00"
title: "Mulit PrimaryのMySQL Group Replicationでノードをまたぐロックの挙動"
author: "dshimizu"
archive: ["2017"]
draft: "true"
---

<body>
<p>Group Replication は、マルチマスターの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>構成を行うための<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>の機能で、5.7.17から<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>として提供され利用可能となっています。</p>

<p>Group Replication では<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>を構成する各<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>ノードに書き込みができる Mulit Primary 構成と、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>を構成するいずれか１つのノードにのみ書き込みができる Single Primary 構成があります。
Single Primaryでは特定のノードにしか書き込みができないのでノードをまたぐロックについては気にする必要がありませんが、Multi-Primaryでは複数のノードから書き込みが行われるため、ノードをまたぐロックも気にしなければなりません。</p>

<p>しかし、Group Replication で Writeが行われた時のノード間の整合性チェックのプロセスにはこれらのロックについては考慮されていません。
異なるノードでの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>による更新処理は正常に実行され、楽観的ロックにより COMMIT 後にチェックが行われて不整合が生じた場合にエラーが返されるようです。</p>
</body>

<!-- more -->

<body>
<h3>確認</h3>


<h4>準備</h4>


<p>まずMulti Primary モードで Group Replication を開始しておきます。</p>

<pre class="terminal"> root@mysql1[(none)] &gt; SELECT * FROM performance_schema.replication_group_members;
+---------------------------+--------------------------------------+---------------+-------------+--------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST   | MEMBER_PORT | MEMBER_STATE |
+---------------------------+--------------------------------------+---------------+-------------+--------------+
| group_replication_applier | 5c592b4a-961c-11e7-9da1-0204e4dfe4d8 | 192.168.10.65 |        3306 | ONLINE       |
| group_replication_applier | 6da540cb-961c-11e7-86b4-0204e4dfe4d8 | 192.168.10.66 |        3306 | ONLINE       |
| group_replication_applier | 8883dc93-961c-11e7-8438-0204e4dfe4d8 | 192.168.10.67 |        3306 | ONLINE       |
+---------------------------+--------------------------------------+---------------+-------------+--------------+
3 rows in set (0.00 sec)

root@mysql01[testdb] &gt; show variables like "%group_replication_single_primary_mode%";
+---------------------------------------+-------+
| Variable_name                         | Value |
+---------------------------------------+-------+
| group_replication_single_primary_mode | OFF   |
+---------------------------------------+-------+
1 row in set (0.00 sec)
 </pre>


<p>テストように以下のようなテーブルを準備し、適当にデータを入れておきます。</p>

<pre class="terminal"> root@mysql1[testdb] &gt; desc user;
+------------+-------------+------+-----+---------+----------------+
| Field      | Type        | Null | Key | Default | Extra          |
+------------+-------------+------+-----+---------+----------------+
| user_id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| user_name  | varchar(45) | YES  |     | NULL    |                |
| user_age   | int(11)     | YES  |     | NULL    |                |
| created_at | datetime    | YES  |     | NULL    |                |
+------------+-------------+------+-----+---------+----------------+
4 rows in set (0.00 sec)

root@mysql1[testdb] &gt; select * from user;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       28 | 2016-06-08 18:00:35 |
|       9 | TEST USER02 |        7 | 2017-04-27 18:41:06 |
|      16 | TEST USER03 |       75 | 2015-10-03 06:17:01 |
|      23 | TEST USER04 |       24 | 2017-02-18 10:49:58 |
|      30 | TEST USER05 |       57 | 2016-03-26 23:28:43 |
+---------+-------------+----------+---------------------+
5 rows in set (0.00 sec)
 </pre>


<p>この<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>分離レベルは<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>のデフォルトです。</p>

<pre class="terminal"> root@mysql01[(none)] &gt; SELECT @@GLOBAL.tx_isolation, @@tx_isolation;
+-----------------------+-----------------+
| @@GLOBAL.tx_isolation | @@tx_isolation  |
+-----------------------+-----------------+
| REPEATABLE-READ       | REPEATABLE-READ |
+-----------------------+-----------------+
1 row in set (0.00 sec)
 </pre>


<p>この<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>分離レベルでは、ある<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>Aで参照できる値はその<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>Aが開始された時点の値のままとなり、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>Aがコミットされるまで変わりません。別の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>Bが値を変更したとしてもそれによって結果に変化はありません(ここで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>Aが<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>Bと同じカラムを更新しようとした場合ロックが発生します)。</p>

<p>とりあえずこれで準備完了です。</p>

<h4>排他ロック</h4>


<p>排他ロックについて確認してみます。</p>

<p>まずはノード1で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>(tx1)を開始し、<code>SELECT ... FOR UPDATE</code>による特定の行に排他ロックをかけます。</p>

<pre class="terminal"> root@mysql1[testdb] &gt; start transaction;
Query OK, 0 rows affected (0.00 sec)

root@mysql1[testdb] &gt; select * from user where user_id='2' for update;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       28 | 2016-06-08 18:00:35 |
+---------+-------------+----------+---------------------+
1 row in set (0.01 sec)
 </pre>


<p>続いて、ノード2で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>(tx2)を開始し、SELECT ... FOR UPDATEで同じ行に排他ロックをかけます。
通常は、ここで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>がブロックされるはずですが、ノードをまたいだ排他ロックをブロックすることができないため、そのまま通ってしまいます。</p>

<pre class="terminal"> root@mysql2[testdb] &gt; start transaction;
Query OK, 0 rows affected (0.00 sec)

root@mysql2[testdb] &gt;  select * from user where user_id='2' for update;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       28 | 2016-06-08 18:00:35 |
+---------+-------------+----------+---------------------+
1 row in set (0.00 sec)
 </pre>


<p>ノード１で実行した<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>(tx1)で行を更新します。</p>

<pre class="terminal"> root@mysql1[testdb] &gt; update user set user_age = '30' where user_id = '2' ;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

root@mysql1[testdb] &gt; select * from user;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       30 | 2016-06-08 18:00:35 |
|       9 | TEST USER02 |        7 | 2017-04-27 18:41:06 |
|      16 | TEST USER03 |       75 | 2015-10-03 06:17:01 |
|      23 | TEST USER04 |       24 | 2017-02-18 10:49:58 |
|      30 | TEST USER05 |       57 | 2016-03-26 23:28:43 |
+---------+-------------+----------+---------------------+
5 rows in set (0.00 sec)
 </pre>


<p>ノード2で実行した<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>(tx2)で行を更新します。</p>

<pre class="terminal"> root@mysql2[testdb] Fri Aug 10 21:16:10 2017 &gt; update user set user_age = '35' where user_id = '2' ;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

root@mysql1[testdb] &gt; select * from user;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       35 | 2016-06-08 18:00:35 |
|       9 | TEST USER02 |        7 | 2017-04-27 18:41:06 |
|      16 | TEST USER03 |       75 | 2015-10-03 06:17:01 |
|      23 | TEST USER04 |       24 | 2017-02-18 10:49:58 |
|      30 | TEST USER05 |       57 | 2016-03-26 23:28:43 |
+---------+-------------+----------+---------------------+
5 rows in set (0.00 sec)
 </pre>


<p>ノード1の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>をコミットすると成功します。</p>

<pre class="terminal"> root@mysql1[testdb] &gt; commit;
Query OK, 0 rows affected (0.01 sec)
 </pre>


<p>ノード2の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>をコミットすると失敗します。</p>

<pre class="terminal"> root@mysql2[testdb] &gt; commit;
ERROR 1180 (HY000): Got error 149 during COMMIT
 </pre>


<p>結果をみてみます。</p>

<pre class="terminal"> root@mysql1[testdb] &gt; select * from user;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       30 | 2016-06-08 18:00:35 |
|       9 | TEST USER02 |        7 | 2017-04-27 18:41:06 |
|      16 | TEST USER03 |       75 | 2015-10-03 06:17:01 |
|      23 | TEST USER04 |       24 | 2017-02-18 10:49:58 |
|      30 | TEST USER05 |       57 | 2016-03-26 23:28:43 |
+---------+-------------+----------+---------------------+
5 rows in set (0.00 sec)
 </pre>


<pre class="terminal"> root@mysql2[testdb] &gt; select * from user;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       30 | 2016-06-08 18:00:35 |
|       9 | TEST USER02 |        7 | 2017-04-27 18:41:06 |
|      16 | TEST USER03 |       75 | 2015-10-03 06:17:01 |
|      23 | TEST USER04 |       24 | 2017-02-18 10:49:58 |
|      30 | TEST USER05 |       57 | 2016-03-26 23:28:43 |
+---------+-------------+----------+---------------------+
5 rows in set (0.00 sec)
 </pre>


<pre class="terminal"> root@mysql3[testdb] &gt; select * from user;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       30 | 2016-06-08 18:00:35 |
|       9 | TEST USER02 |        7 | 2017-04-27 18:41:06 |
|      16 | TEST USER03 |       75 | 2015-10-03 06:17:01 |
|      23 | TEST USER04 |       24 | 2017-02-18 10:49:58 |
|      30 | TEST USER05 |       57 | 2016-03-26 23:28:43 |
+---------+-------------+----------+---------------------+
5 rows in set (0.00 sec)
 </pre>


<p>ノードをまたいだ排他ロックの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>はブロックされず、最初にコミットされた方は成功し、後にコミットされた方は<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%C3%A5%C9%A5%ED%A5%C3%A5%AF">デッドロック</a>になります。</p>

<h4>共有ロック</h4>


<p>共有ロックについて確認してみます。</p>

<p>まずはノード1で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>(tx1)を開始し、SELECT ... LOCK IN SHARE MODE による特定の行に排他ロックをかけます。</p>

<pre class="terminal"> root@mysql01[testdb] &gt; start transaction;
Query OK, 0 rows affected (0.00 sec)

root@mysql01[testdb] &gt; select * from user where user_id='30' LOCK IN SHARE MODE;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|      30 | TEST USER05 |       57 | 2016-03-26 23:28:43 |
+---------+-------------+----------+---------------------+
1 row in set (0.00 sec)
 </pre>


<p>ノード2で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>(tx2)を開始し、SELECT ... LOCK IN SHARE MODE による特定の行に排他ロックをかけます。共有ロックなのでこれは成功します。</p>

<pre class="terminal"> root@mysql02[testdb] &gt; start transaction;
Query OK, 0 rows affected (0.00 sec)

root@mysql02[testdb] &gt; select * from user where user_id='30' LOCK IN SHARE MODE;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|      30 | TEST USER05 |       57 | 2016-03-26 23:28:43 |
+---------+-------------+----------+---------------------+
1 row in set (0.00 sec)
 </pre>


<p>ノード1でアップデートを実行します。
通常は、ここで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>がブロックされるはずですが、ノードをまたいだ共有ロックをブロックすることができないため、そのまま通ってしまいます。</p>

<pre class="terminal"> root@mysql01[testdb] &gt; update user set created_at = '2013-11-23 09:30:29' where user_id = '30' ;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
 </pre>


<p>ノード2でアップデートを実行します。これも通ってしまいます。</p>

<pre class="terminal"> root@mysql02[testdb] &gt; update user set created_at = '2013-09-10 22:24:21' where user_id = '30' ;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
 </pre>


<p>ノード１でコミットを実行します。</p>

<pre class="terminal"> root@mysql01[testdb] &gt; commit;
Query OK, 0 rows affected (0.00 sec)
 </pre>


<p>ノード2でコミットを実行します。後からコミットした方はエラーになります。</p>

<pre class="terminal"> root@mysql02[testdb] &gt; commit;
ERROR 1180 (HY000): Got error 149 during COMMIT
 </pre>


<pre class="terminal"> root@mysql1[testdb] &gt; select * from user;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       30 | 2016-06-08 18:00:35 |
|       9 | TEST USER02 |        7 | 2017-04-27 18:41:06 |
|      16 | TEST USER03 |       75 | 2015-10-03 06:17:01 |
|      23 | TEST USER04 |       24 | 2017-02-18 10:49:58 |
|      30 | TEST USER05 |       57 | 2013-11-23 09:30:29 |
+---------+-------------+----------+---------------------+
5 rows in set (0.00 sec)
 </pre>


<pre class="terminal"> root@mysql2[testdb] &gt; select * from user;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       30 | 2016-06-08 18:00:35 |
|       9 | TEST USER02 |        7 | 2017-04-27 18:41:06 |
|      16 | TEST USER03 |       75 | 2015-10-03 06:17:01 |
|      23 | TEST USER04 |       24 | 2017-02-18 10:49:58 |
|      30 | TEST USER05 |       57 | 2013-11-23 09:30:29 |
+---------+-------------+----------+---------------------+
5 rows in set (0.00 sec)
 </pre>


<pre class="terminal"> root@mysql3[testdb] &gt; select * from user;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       30 | 2016-06-08 18:00:35 |
|       9 | TEST USER02 |        7 | 2017-04-27 18:41:06 |
|      16 | TEST USER03 |       75 | 2015-10-03 06:17:01 |
|      23 | TEST USER04 |       24 | 2017-02-18 10:49:58 |
|      30 | TEST USER05 |       57 | 2013-11-23 09:30:29 |
+---------+-------------+----------+---------------------+
5 rows in set (0.00 sec)
 </pre>


<p>ノードをまたいだ共有ロックの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>もブロックされず、最初にコミットされた方は成功し、後にコミットされた方は<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%C3%A5%C9%A5%ED%A5%C3%A5%AF">デッドロック</a>になります。</p>

<h4>テーブルロック</h4>


<p>テーブルロックについて確認してみます。
まずノード1でテーブルロックをかけます。</p>

<pre class="terminal"> root@mysql01[testdb] &gt; lock tables user read;
Query OK, 0 rows affected (0.00 sec)

root@mysql01[testdb] &gt; insert into user (user_name, user_age, created_at) values ('TEST USER06', 33 ,'2014-08-12 00:32:21');
ERROR 1099 (HY000): Table 'user' was locked with a READ lock and can't be updated
 </pre>


<p>ノード２で insert を行うと、通常ブロックされるはずですが、正常に実行されてしまいます。</p>

<pre class="terminal"> root@mysql02[testdb] &gt; insert into user (user_name, user_age, created_at) values ('TEST USER06', 33 ,'2014-08-12 00:32:21');
Query OK, 1 row affected (0.01 sec)

root@mysql02[testdb] &gt;  select * from user;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       28 | 2016-06-08 18:00:35 |
|       9 | TEST USER02 |        7 | 2017-04-27 18:41:06 |
|      16 | TEST USER03 |       75 | 2015-10-03 06:17:01 |
|      23 | TEST USER04 |       24 | 2017-02-18 10:49:58 |
|      30 | TEST USER05 |       57 | 2013-11-23 09:30:29 |
|      37 | TEST USER06 |       33 | 2014-08-12 00:32:21 |
+---------+-------------+----------+---------------------+
6 rows in set (0.00 sec)
 </pre>


<p>ノード1はまだテーブルロック取得中のため、この更新の内容は見えません。</p>

<pre class="terminal"> root@mysql01[testdb] &gt; select * from user;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       28 | 2016-06-08 18:00:35 |
|       9 | TEST USER02 |        7 | 2017-04-27 18:41:06 |
|      16 | TEST USER03 |       75 | 2015-10-03 06:17:01 |
|      23 | TEST USER04 |       24 | 2017-02-18 10:49:58 |
|      30 | TEST USER05 |       57 | 2013-11-23 09:30:29 |
+---------+-------------+----------+---------------------+
5 rows in set (0.00 sec)
 </pre>


<p>ロックを解放した時点で見えるようになります。</p>

<pre class="terminal"> root@mysql01[testdb] &gt; unlock tables;
Query OK, 0 rows affected (0.00 sec)

root@mysql01[testdb] &gt; select * from user;
+---------+-------------+----------+---------------------+
| user_id | user_name   | user_age | created_at          |
+---------+-------------+----------+---------------------+
|       2 | TEST USER01 |       28 | 2016-06-08 18:00:35 |
|       9 | TEST USER02 |        7 | 2017-04-27 18:41:06 |
|      16 | TEST USER03 |       75 | 2015-10-03 06:17:01 |
|      23 | TEST USER04 |       24 | 2017-02-18 10:49:58 |
|      30 | TEST USER05 |       57 | 2013-11-23 09:30:29 |
|      37 | TEST USER06 |       33 | 2014-08-12 00:32:21 |
+---------+-------------+----------+---------------------+
6 rows in set (0.00 sec)
 </pre>


<p>Group Replicationではノードをまたぐテーブルロックがサポートされないため、あるノードでテーブルロックを行っていても別ノードからカラムのinsertができてしまいました。</p>

<h3>まとめ</h3>


<p>Multi Master　　な <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Group Replication で複数のノードから<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>を用いた書き込みを行った場合、楽観的ロックにより先にコミットされた方が優先され、後からコミットされた方はコミット時点で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%C3%A5%C9%A5%ED%A5%C3%A5%AF">デッドロック</a>となってしまう動作を簡単に確認しました。
コミット後の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%C3%A5%C9%A5%ED%A5%C3%A5%AF">デッドロック</a>についてはアプリケーション側での処理が必要になるため、これを許容しない場合には、Single Primaryでの運用にした方が良さそうです。</p>

<h4>参考</h4>


<ul>
    <li><a href="https://dev.mysql.com/doc/refman/5.7/en/group-replication-limitations.html" target="_blank" rel="noopener noreferrer">MySQL :: MySQL 5.7 Reference Manual :: 17.7.2 Group Replication Limitations</a></li>
    <li><a href="https://dev.mysql.com/doc/refman/5.7/en/innodb-locking-reads.html" target="_blank" rel="noopener noreferrer">MySQL :: MySQL 5.7 Reference Manual :: 14.5.2.4 Locking Reads</a></li>
    <li><a href="http://nippondanji.blogspot.com/2013/12/innodbrepeatable-readlocking-read.html" target="_blank" rel="noopener noreferrer">漢(オトコ)のコンピュータ道: InnoDBのREPEATABLE READにおけるLocking Readについての注意点</a></li>
<ul></ul>
</ul>

</body>
