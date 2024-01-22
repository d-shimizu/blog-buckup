---
layout: post
Categories:  ["tech"]
Description:  " MySQL InnoDB Clusterをいじっていて、1台ぶっ壊れたのでリストアしようとしたらタイトルの件でハマりました。ちなみに¥MySQL InnoDB Clusterが悪い訳ではなない。 "
Tags: ["MySQL", "tech"]
date: "2017-07-09T14:24:00+09:00"
title: "MySQL InnoDB Clusterを作成した時に設定ファイルにdisabled_storage_engines=myisamが自動で追記されていてフルダンプのリストア時にmysql.usersがリストアできなかった件"
author: "dshimizu"
archive: ["2017"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Clusterをいじっていて、1台ぶっ壊れたのでリストアしようとしたらタイトルの件でハマりました。
ちなみに¥<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Clusterが悪い訳ではなない。</p>
</body>

<!-- more -->

<body>
<h4>What is disabled_storage_engines?</h4>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> 5.7.8でいろいろな変数が追加されましたが <code>disabled_storage_engines</code> もその一つで、これは名前の通り指定したストレージエンジンではテーブルを作成できないようにするもの。</p>

<p>と言っても設定ファイルや変数に明示的に指定しなければデフォルト値は空なんですが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Cluser作成時に <code>mysqlsh</code> から <code>dba.configureLocalInstance</code> を使用してClusterを作成した際に、 <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>のコンフィグファイルに <code>disabled_storage_engines=myisam</code> が自動で付与されていました。</p>

<p>これに気づかず、生きている<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>から以下のように <code>mysqldump</code> で <code>--all-databases</code> オプションを付けてデータを取得して・・・</p>

<pre class="terminal"> $ mysqldump -u root --all-databases --lock-all-tables --triggers --routines --events -p &gt; dump.sql
 </pre>


<p>それをそのまま壊れた <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> へ流し込みました。</p>

<pre class="terminal"> mysql&gt; reset master;
Query OK, 0 rows affected (0.02 sec)
 
mysql&gt; source dump.sql
 
mysql&gt; start group_replication;
 </pre>


<p>ここで <code>start group_replication;</code>がコケます。</p>

<p>以下のように <code>mysql.user</code> テーブルがないと怒られてログインすらできなくなってしまいました。わっしょい。</p>

<pre class="terminal"> 1146 (42S02): Table 'mysql.user' doesn't exist (RuntimeError)
 </pre>


<p>取得した <code>dump.sql</code> の中では以下のような記述がありますので、見事に <code>user</code> テーブルが消え去りました。</p>

<pre class="terminal"> DROP TABLE IF EXISTS `user`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `user` (
  `Host` char(60) COLLATE utf8_bin NOT NULL DEFAULT '',
  `User` char(32) COLLATE utf8_bin NOT NULL DEFAULT '',
:
 </pre>


<p>こうなると<code>mysqld_safe</code> で頑張って復旧させるか、該当<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Serverを初期化して作り直すしかなさそうです。</p>

<h3>まとめ</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Clusterの検証ということであまり確認せずに適当に全データベースのダンプを使ったリストアを行おうとしてハマりました。
こういう時は <code>mysqldump</code> ではなく <code>mysqlpump</code> で <code>--users</code> オプションを使うという選択もあります。</p>

<h4>参考</h4>


<ul>
    <li><a href="https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_disabled_storage_engines" target="_blank" rel="noopener noreferrer">MySQL :: MySQL 5.7 Reference Manual :: 5.1.5 Server System Variables</a></li>
    <li><a href="https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_users" target="_blank" rel="noopener noreferrer">MySQL :: MySQL 5.7 Reference Manual :: 4.5.6 mysqlpump — A Database Backup Program</a></li>
</ul>

</body>
