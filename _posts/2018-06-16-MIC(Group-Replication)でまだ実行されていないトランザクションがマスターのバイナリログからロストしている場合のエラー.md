---
layout: post
Categories:  ["tech"]
Description:  " Single PrimaryのMySQL InnoDB Cluster(Group Replication)が組まれているサーバのうち、スレーブとなる1台を切り離した後、切り離されて以降のトランザクションが記録されたバイナリログがなくなっ"
Tags: ["MySQL", "tech"]
date: "2018-06-16T11:31:00+09:00"
title: "MIC(Group Replication)でまだ実行されていないトランザクションがマスターのバイナリログからロストしている場合のエラー"
author: "dshimizu"
archive: ["2018"]
draft: "true"
---

<body>
<p>Single Primaryの<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Cluster(Group Replication)が組まれているサーバのうち、スレーブとなる1台を切り離した後、切り離されて以降の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>が記録されたバイナリログがなくなっていた(参照できなかった)場合に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>しようとするとどうなるか試した。</p>
</body>

<!-- more -->

<body>
<h3>Group Replication時のエラー</h3>


<p>以下が発生したエラー。当然Group Replicationは組めていない。</p>

<pre class="terminal"> :
2018-05-11T15:58:34.908319+09:00 0 [ERROR] Plugin group_replication reported: 'Group contains 2 members which is greater than group_replication_auto_increment_increment value of 1. This can lead to an higher rate of transactional aborts.'
2018-05-11T15:58:34.908768+09:00 1215 [Note] Plugin group_replication reported: 'This server is working as secondary member with primary member address mysql01:3306.'
2018-05-11T15:58:34.909234+09:00 0 [Note] Plugin group_replication reported: 'Group membership changed to mysql02:3306, mysql01:3306 on view 15260218991937581:2.'
2018-05-11T15:58:34.909316+09:00 1229 [Note] Plugin group_replication reported: 'Establishing group recovery connection with a possible donor. Attempt 1/10'
2018-05-11T15:58:34.922418+09:00 1229 [Note] 'CHANGE MASTER TO FOR CHANNEL 'group_replication_recovery' executed'. Previous state master_host='', master_port= 3306, master_log_file='', master_log_pos= 4, master_bind=''. New state master_host='mysql01', master_port= 3306, master_log_file='', master_log_pos= 4, master_bind=''.
2018-05-11T15:58:34.934405+09:00 1229 [Note] Plugin group_replication reported: 'Establishing connection to a group replication recovery donor ********-****-****-****-************ at mysql01 port: 3306.'
2018-05-11T15:58:34.934960+09:00 1231 [Warning] Storing MySQL user name or password information in the master info repository is not secure and is therefore not recommended. Please consider using the USER and PASSWORD connection options　for START SLAVE; see the 'START SLAVE Syntax' in the MySQL Manual for more information.
2018-05-11T15:58:34.936904+09:00 1232 [Note] Slave SQL thread for channel 'group_replication_recovery' initialized, starting replication in log 'FIRST' at position 0, relay log './mysql02-relay-bin-group_replication_recovery.000001' position: 4
2018-05-11T15:58:34.966882+09:00 1231 [Note] Slave I/O thread for channel 'group_replication_recovery': connected to master 'mysql_innodb_cluster_r**********@mysql01:3306',replication started in log 'FIRST' at position 4
2018-05-11T15:58:34.974048+09:00 1231 [ERROR] Error reading packet from server for channel 'group_replication_recovery': The slave is connecting using CHANGE MASTER TO MASTER_AUTO_POSITION = 1, but the master has purged binary logs containing GTIDs that the slave requires. (server_errno=1236)
2018-05-11T15:58:34.974079+09:00 1231 [ERROR] Slave I/O for channel 'group_replication_recovery': Got fatal error 1236 from master when reading data from binary log: 'The slave is connecting using CHANGE MASTER TO MASTER_AUTO_POSITION =　1, but the master has purged binary logs containing GTIDs that the slave requires.', Error_code: 1236
2018-05-11T15:58:34.974085+09:00 1231 [Note] Slave I/O thread exiting for channel 'group_replication_recovery', read up to log 'FIRST', position 4
2018-05-11T15:58:34.974115+09:00 1229 [Note] Plugin group_replication reported: 'Terminating existing group replication donor connection and purging the corresponding logs.'
2018-05-11T15:58:34.974159+09:00 1232 [Note] Error reading relay log event for channel 'group_replication_recovery': slave SQL thread was killed
2018-05-11T15:58:35.012419+09:00 1229 [Note] 'CHANGE MASTER TO FOR CHANNEL 'group_replication_recovery' executed'. Previous state master_host='mysql01', master_port= 3306, master_log_file='', master_log_pos= 4, master_bind=''. New state master_host='', master_port= 0, master_log_file='', master_log_pos= 4, master_bind=''.
2018-05-11T15:58:35.024756+09:00 1229 [Note] Plugin group_replication reported: 'Retrying group recovery connection with another donor. Attempt 2/10'
:
 </pre>


<p>MySQL5.7のGroup ReplicationではGTIDを使用した<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>が行われる。
GTIDを使用した<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>を組む場合、 <code>CHANGE MASTER TO</code> でバイナリログポジションを指定しなくても、スレーブが要求するGTIDの情報をもとに、マスターはどのポジションからバイナリログを転送すれば良いのかを自動的に判断する。
実行済みの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>のGTIDの情報は<code>gtid_executed</code>変数(<code>mysql.gtid_executed</code>テーブル)に永続的に保存されるが、実行される<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>自体はバイナリログに記録される。そのため、これが読み込めないと<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>を組む際にエラーとなる。
ちなみにすでにバイナリログから削除された<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>情報は<code>gtid_purged</code>変数(<code>mysql.gtid_purged</code>テーブル)に記録される。</p>

<p>こうなると、他のサーバからmysqldumpなりでフルダンプを取得して<code>reset master</code>を実行した後に、<a class="keyword" href="http://d.hatena.ne.jp/keyword/mysql">mysql</a>テーブルを含めてインポートするしかないように思う…のでやってみたら復旧できた。マスターのデータ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを何等かの方法でコピーするのでも良いと思うがその場合はサーバのUUIDが記載された<code>auto.cnf</code>は削除する必要がある。
ちなみにオフィシャルではEnterprise Backupを用いる方法が記載されていた。</p>

<ul>
    <li><a href="https://dev.mysql.com/doc/refman/5.7/en/group-replication-enterprise-backup.html" target="_brank" rel="noopener noreferrer">MySQL :: MySQL 5.7 Reference Manual :: 17.4.4 Using MySQL Enterprise Backup with Group Replication</a></li>
</ul>


<h3>おわりに</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Cluster(Group Replication)でまだ実行されていない<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>がマスターのバイナリログからロストしている場合のエラーについて書いた。
復旧はお早めに。</p>

<h3>参考</h3>


<ul>
    <li><a href="https://dev.mysql.com/doc/refman/5.7/en/replication-gtids-concepts.html" target="_brank" rel="noopener noreferrer">MySQL :: MySQL 5.7 Reference Manual :: 16.1.3.1 GTID Format and Storage </a></li>
    <li><a href="https://dev.mysql.com/doc/refman/5.6/en/replication-options-gtids.html#sysvar_gtid_executed" target="_brank" rel="noopener noreferrer">MySQL :: MySQL 5.6 Reference Manual :: 17.1.4.5 Global Transaction ID Options and Variables</a></li>
    <li><a href="https://dev.mysql.com/doc/refman/5.7/en/replication-gtids-failover.html" target="_brank" rel="noopener noreferrer">MySQL :: MySQL 5.7 Reference Manual :: 16.1.3.3 Using GTIDs for Failover and Scaleout</a></li>
    <li><a href="https://dev.mysql.com/doc/refman/5.7/en/group-replication-distributed-recovery.html" target="_brank" rel="noopener noreferrer">MySQL :: MySQL 5.7 Reference Manual :: 17.9.5 Distributed Recovery</a></li>
</ul>

</body>
