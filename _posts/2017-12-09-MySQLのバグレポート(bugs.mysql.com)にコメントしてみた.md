---
layout: post
Categories:  ["tech"]
Description:  " MySQL InnoDB Clusterの検証をしていて発生したエラーの事象の1つについてこちらに書きましたが、その事象がこのバグレポに記載されている内容と類似の事象のように思いました。2017/9/5 以降コメントが途絶えていたので、初"
Tags: ["MySQL", "tech"]
date: "2017-12-09T14:58:00+09:00"
title: "MySQLのバグレポート(bugs.mysql.com)にコメントしてみた"
author: "dshimizu"
archive: ["2017"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Clusterの検証をしていて発生したエラーの事象の1つについて<a href="https://blog.dshimizu.jp/article/655" target="_brank" rel="noopener noreferrer">こちら</a>に書きましたが、その事象がこのバグレポに記載されている内容と類似の事象のように思いました。
2017/9/5 以降コメントが途絶えていたので、初めての<a class="keyword" href="http://d.hatena.ne.jp/keyword/OSS">OSS</a>への貢献として自分で確認できた内容をコメントしてみました。</p>

<ul>
    <li><a href="https://bugs.mysql.com/bug.php?id=87300" target="brank" rel="noopener noreferrer">MySQL Bugs: #87300: mysql-shell dba.checkInstanceConfiguration()</a></li>
</ul>

</body>

<!-- more -->

<body>
<h3>概要</h3>


<p>問題の概要としては<code>dba.checkInstanceConfiguration()</code>や<code>dba.configurelocalinstance()</code>を使った場合、コマンド実行権限のみ確認され、<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>アカウントに必要な権限がチェックされない、というもののようです。</p>

<p>例えば、<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Cluster 初期構築時、<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shellから <code>dba.configureLocalInstance()</code>を実行しますが、その際に以下のように<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Cluster管理用のユーザを作成するかどうかを確認されます。</p>

<pre class="terminal"> mysql-js&gt; dba.configureLocalInstance('root@localhost:3306')
Please provide the password for 'root@localhost:3306':

Detecting the configuration file...
Found configuration file at standard location: /etc/mysql/mysql.conf.d/mysqld.cnf
Do you want to modify this file? [Y|n]:  [Y|n]: y
MySQL user 'root' cannot be verified to have access to other hosts in the network.

1) Create root@% with necessary grants
2) Create account with different name
3) Continue without creating account
4) Cancel
Please select an option [1]: 2
Please provide an account name (e.g: icroot@%) to have it created with the necessary
privileges or leave empty and press Enter to cancel.
Account Name: icroot
Password for new account:
Confirm password:
Validating instance...

The instance 'localhost:3306' is valid for Cluster usage
You can now use it in an InnoDB Cluster.

{
    "status": "ok"
}
 </pre>


<p>ここでユーザを作成した場合、このユーザを使ってClusterを新規作成するコマンドを実行した際に以下のようなエラーが発生します。</p>

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


<p>これは <code>dba.configureLocalInstance()</code> で作成されるユーザでは<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Clusterに対するオペレーションする権限が足りないためです。
必要な権限は以下リンクの User Privileges に記載されています(徐々に権限の内容が変わっている模様)。
<a href="https://dev.mysql.com/doc/refman/5.7/en/mysql-innodb-cluster-production-deployment.html" target="_brank" rel="noopener noreferrer">MySQL :: MySQL 5.7 Reference Manual :: 20.2.5 Production Deployment of InnoDB Cluster</a></p>

<p><strong>必要な権限</strong></p>

<pre class="terminal"> GRANT ALL PRIVILEGES ON mysql_innodb_cluster_metadata.* TO your_user@'%' WITH GRANT OPTION;
GRANT RELOAD, SHUTDOWN, PROCESS, FILE, SUPER, REPLICATION SLAVE, REPLICATION CLIENT, CREATE USER ON *.* TO your_user@'%' WITH GRANT OPTION;
GRANT SELECT, INSERT, UPDATE, DELETE ON `mysql`.* TO 'icroot'@'%' WITH GRANT OPTION;
GRANT SELECT ON `performance_schema`.* TO 'icroot'@'%' WITH GRANT OPTION;
GRANT SELECT ON *.* TO your_user@'%' WITH GRANT OPTION;
 </pre>


<p><strong>実際に付与される権限</strong></p>

<pre class="terminal"> GRANT ALL PRIVILEGES ON `mysql_innodb_cluster_metadata`.* TO 'icroot'@'%' WITH GRANT OPTION
GRANT RELOAD, SHUTDOWN, PROCESS, FILE, SUPER, REPLICATION SLAVE, REPLICATION CLIENT, CREATE USER ON *.* TO 'icroot'@'%' WITH GRANT OPTION
GRANT SELECT, INSERT, UPDATE, DELETE ON `mysql`.* TO 'icroot'@'%' WITH GRANT OPTION
GRANT SELECT ON `performance_schema`.* TO 'icroot'@'%' WITH GRANT OPTION
 </pre>


<p>全テーブルに対する<code>select</code>権限(<code>GRANT SELECT ON <em>.</em> TO your_user@'%' WITH GRANT OPTION;</code>)が付与されません。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>のコードの修正をできれば最高なんでしょうけど残念ながらそれをするには能力不足なので、発生原因と暫定回避策について拙い英語で書いてみました。
※当初は<code>sys.*</code>への参照権限があれば良いということがドキュメントに書かれていたと思うので(すが記憶が曖昧)以下のようにコメントしましたが、現状のドキュメントでは全DBの全テーブルにselect権限を付与する必要がある、となっています。</p>

<pre class="text"> [19 Sep 2017 16:40] Daisuke Shimizu
I think that you probably need the following user accounts.

GRANT SELECT ON sys.* TO your_user@'%' WITH GRANT OPTION;

When using "dba.configureLocalInstance ()" to create InnoDB cluster administration account, I think that the InnoDB cluster administration account may not have privilege to "sys schema".
 </pre>


<p>11/9にコメントがあり、次期バージョンの<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shellの1.0.11と<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>の8.0.4ではアカウントに必要な権限が付与されるように修正されるとのことでした。</p>

<pre class="text"> [9 Nov 11:24] David Moss
Posted by developer:
 
Thank you for your feedback, this has been fixed in upcoming versions and the following was added to the 1.0.11  / 8.0.4 changelog:
When using the dba.checkInstanceConfiguration()  and dba.configurelocalinstance() commands, the account being used was not being checked if it had enough privileges to actually execute the command. The fix ensures that account has the required privileges before proceeding. This also required a change of the privileges given to clusterAdmin users.
 </pre>


<ul>
    <li><a href="https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-4.html" target="_brank" rel="noopener noreferrer">MySQL :: MySQL 8.0 Release Notes :: Changes in MySQL 8.0.4 (Not yet released, Release Candidate)</a></li>
→まだリリースされておらず未記入
    <li><a href="https://dev.mysql.com/doc/relnotes/mysql-shell/1.0/en/mysql-shell-news-1-0-11.html" target="_brank" rel="noopener noreferrer">MySQL :: MySQL Shell Release Notes :: Changes in MySQL Shell 1.0.11 (2017-11-30, General Availabi... </a></li>
こちらには以下の記載があります。
</ul>
<pre class="text"> When using the dba.checkInstanceConfiguration() and dba.configurelocalinstance() commands, the account being used was not being checked if it had enough privileges to actually execute the command. The fix ensures that account has the required privileges before proceeding. This also required a change of the privileges given to clusterAdmin users. (Bug #87300, Bug #26609909)
 </pre>



<h3>まとめ</h3>


<p>初めて<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>のバグレポート(bugs.<a class="keyword" href="http://d.hatena.ne.jp/keyword/mysql">mysql</a>.com)にコメントしてみたときのことを書きました。
全く大したことはやっていませんがバグ報告の議論に参加したと言うことでほーーーーーーーーんのちょっとだけ<a class="keyword" href="http://d.hatena.ne.jp/keyword/OSS">OSS</a>に貢献するということができた(つもりになれて)良かったです。いずれはコードで貢献できれば…とは思ってますが、とりあえず第一歩として前進できました。</p>

<h3>参考</h3>


<ul>
    <li><a href="https://qiita.com/shunsuke227ono/items/94dd6e707d34a1da2617" target="_brank" rel="noopener noreferrer">意外ととっつきやすいOSS開発参加方法まとめ - Qiita</a></li>
</ul>

</body>
