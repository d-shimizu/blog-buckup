---
title: "Aurora MySQL のバイナリログ利用に関して調べたことのメモ"
date: 2023-12-24
slug: "Aurora MySQL のバイナリログ利用に関して調べたことのメモ"
category: blog
tags: [AWS]
---
<h2 id="はじめに">はじめに</h2>

<p>Aurora <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> の BG デプロイを試そうとしたところ、バイナリログの有効化が必要でした。しかし、Aurora <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> ではデフォルトでバイナリログがオフになっています。
では有効化すれば良いのでは、というところですが、パフォーマンスに影響が出る可能性も示唆されていて、ちょっとすんなり有効化して良いものか、と思ったので少し調べてみました。</p>

<h2 id="バイナリログ">バイナリログ</h2>

<ul>
<li><a href="https://dev.mysql.com/doc/refman/8.0/en/binary-log.html">MySQL :: MySQL 8.0 Reference Manual :: 5.4.4 The Binary Log</a>

<blockquote><p>The binary log contains “events” that describe database changes such as table creation operations or changes to table data. It also contains events for statements that potentially could have made changes (for example, a DELETE which matched no rows), unless row-based logging is used. The binary log also contains information about how long each statement took that updated data. The binary log has two important purposes:</p>

<p>For replication, the binary log on a replication source server provides a record of the data changes to be sent to replicas. The source sends the information contained in its binary log to its replicas, which reproduce those transactions to make the same data changes that were made on the source. See Section 17.2, “Replication Implementation”.</p>

<p>Certain data recovery operations require use of the binary log. After a backup has been restored, the events in the binary log that were recorded after the backup was made are re-executed. These events bring databases up to date from the point of the backup. See Section 7.5, “Point-in-Time (Incremental) Recovery”.</p></blockquote></li>
</ul>


<p>上記に書いてある通り、バイナリログには、テーブル作成操作やテー<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%D6%A5%EB%A5%C7%A1%BC">ブルデー</a>タへの変更などのデータベース変更を記述する「イベント」が格納される、とあります。
データを更新した各<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%C6%A1%BC%A5%C8%A5%E1%A5%F3%A5%C8">ステートメント</a>に要した時間に関する情報や、行ベース(binlog_format=ROW)以外の場合、(一致する行のない DELETE などの) <a class="keyword" href="https://d.hatena.ne.jp/keyword/%C0%F8%BA%DF%C5%AA">潜在的</a>に変更を行おうとした<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%C6%A1%BC%A5%C8%A5%E1%A5%F3%A5%C8">ステートメント</a>についてのイベントも格納されるようです。
バイナリログは、<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>やデータ<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EA%A5%AB%A5%D0%A5%EA">リカバリ</a>に利用されます。</p>

<h2 id="Aurora-におけるバイナリログ">Aurora におけるバイナリログ</h2>

<p>ドキュメントによると、Aurora の場合、クラッシュ時に関しては、バイナリログなしでも<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EA%A5%AB%A5%D0%A5%EA">リカバリ</a>されると記載があります。</p>

<ul>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html#Aurora.Overview.Reliability">Amazon Aurora storage and reliability - Amazon Aurora</a>

<blockquote><p>Aurora is designed to recover from an unplanned restart almost instantaneously and continue to serve your application data without the binary log. Aurora recovers asynchronously on parallel threads, so that your database is open and available immediately after an unplanned restart.</p></blockquote></li>
</ul>


<p>おそらくAuroraの仕組み上<a class="keyword" href="https://d.hatena.ne.jp/keyword/REDO">REDO</a>/UNDOログがあれば復旧できるため、ということなのかと思っていますが、ともかく、障害でのデータ<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EA%A5%AB%A5%D0%A5%EA">リカバリ</a>に関しては、バイナリログなしでも良いという選択はできそうです。</p>

<p>一方で、オペミスによるデータロスト、例えば謝って DELETE 文を実行して削除してしまった、と言った場合にはバイナリログがないと復旧は厳しそうです。</p>

<p>また、Aurora と <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> との間、または Aurora と別の Aurora DB <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーとの間の<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>することが必要になった場合は、<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>のためにバイナリログが必要になります。</p>

<h3 id="Aurora-でのデータ復元のための機能">Aurora でのデータ復元のための機能</h3>

<p>この場合のAuroraの機能として、バックトラックやPITRがあります。</p>

<ul>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Managing.Backtrack.html">Backtracking an Aurora DB cluster - Amazon Aurora</a></li>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-pitr.html">Restoring a DB cluster to a specified time - Amazon Aurora</a></li>
</ul>


<p>いずれも指定した時点に巻き戻すことができますが、その後に更新されたデータは失われてしまいます。バイナリログがあれば、それを適用することで追加のデータ復旧ができることになります。</p>

<h2 id="Aurora-におけるバイナリログ有効化の場合の性能影響">Aurora におけるバイナリログ有効化の場合の性能影響</h2>

<p>Aurora <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a>でバイナリログを有効化した場合、以下のような影響があるとドキュメントには記載されています。</p>

<ul>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_LogAccess.MySQL.BinaryFormat.html">Configuring Aurora MySQL binary logging - Amazon Aurora</a>

<blockquote><p>Important</p>

<p>Setting the binary logging format to row-based can result in very large binary log files. Large binary log files reduce the amount of storage available for a DB cluster and can increase the amount of time to perform a restore operation of a DB cluster.</p>

<p>Statement-based replication can cause inconsistencies between the source DB cluster and a read replica. For more information, see Determination of safe and unsafe statements in binary logging in the <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> documentation.</p>

<p>Enabling binary logging increases the number of write disk I/O operations to the DB cluster. You can monitor IOPS usage with the VolumeWriteIOPs CloudWatch metric.</p></blockquote></li>
</ul>


<p>バイナリログは、デフォルトだとAuroraのストレージ基盤に保存されるようで、それによってIO性能に影響があるようなので、 VolumeWriteIOPs のメトリクスを見て、変化が許容できるかどうかをみる必要があるようです。昨年(2022年5月)下記のようなリリースがあったので、パフォーマンス改善の仕組みも準備されているようです。</p>

<ul>
<li><a href="https://aws.amazon.com/jp/blogs/database/introducing-amazon-aurora-mysql-enhanced-binary-log-binlog/">Introducing Amazon Aurora MySQL enhanced binary log (binlog) | AWS Database Blog</a></li>
</ul>


<p>また、バイナリログの<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EA%A5%AB%A5%D0%A5%EA">リカバリ</a>プロセスにより、再起動時のエンジンの起動時間が長くなる、とあります。</p>

<ul>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html#Aurora.Overview.Reliability">Amazon Aurora storage and reliability - Amazon Aurora</a>

<blockquote><p>Enabling binary logging on Aurora directly affects the recovery time after an unplanned restart, because it forces the DB instance to perform binary log recovery.</p></blockquote></li>
</ul>


<p>バイナリログが無くともAuroraによる<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EA%A5%AB%A5%D0%A5%EA">リカバリ</a>プロセスで復旧可能な形にはなっていると思いますが、バイナリログがある場合は DB <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>で強制的にバイナリログ復旧が実行されるためのようです。</p>

<h2 id="検証">検証</h2>

<p>現時点(Aurora <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> v3)で binlog のOFF/ONでどのくらいのパフォーマンス影響があるか、sysbench を使って検証してみます。
書き込みのみのテストをやってみます。</p>

<pre class="code terminal" data-lang="terminal" data-unlink>$ cat /etc/lsb-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=22.04
DISTRIB_CODENAME=jammy
DISTRIB_DESCRIPTION=&#34;Ubuntu 22.04.3 LTS&#34;</pre>




<pre class="code terminal" data-lang="terminal" data-unlink>$ sysbench --version
sysbench 1.0.20</pre>




<pre class="code terminal" data-lang="terminal" data-unlink>mysql&gt; show variables like &#39;%version&#39;;
+--------------------------+-----------------+
| Variable_name            | Value           |
+--------------------------+-----------------+
| admin_tls_version        | TLSv1.2,TLSv1.3 |
| aurora_version           | 3.04.0          |
| immediate_server_version | 999999          |
| innodb_version           | 8.0.28          |
| original_server_version  | 999999          |
| protocol_version         | 10              |
| tls_version              | TLSv1.2,TLSv1.3 |
| version                  | 8.0.28          |
+--------------------------+-----------------+
8 rows in set (0.01 sec)</pre>


<h3 id="sysbench-インストール">sysbench インストール</h3>

<p>sysbench をインストールします。</p>

<pre class="code terminal" data-lang="terminal" data-unlink>$ sudo apt update
Hit:1 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
Hit:3 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu jammy-backports InRelease
Get:4 https://download.docker.com/linux/ubuntu jammy InRelease [48.8 kB]
Get:5 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [1263 kB]
Get:6 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [1020 kB]
Get:7 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:8 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [1051 kB]
Get:9 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [823 kB]
Fetched 4434 kB in 3s (1451 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
6 packages can be upgraded. Run &#39;apt list --upgradable&#39; to see them.

$ sudo apt install sysbench
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages were automatically installed and are no longer required:
  linux-aws-6.2-headers-6.2.0-1015 linux-headers-6.2.0-1015-aws linux-image-6.2.0-1015-aws linux-modules-6.2.0-1015-aws
Use &#39;sudo apt autoremove&#39; to remove them.
The following additional packages will be installed:
  libluajit-5.1-2 libluajit-5.1-common libpq5
The following NEW packages will be installed:
  libluajit-5.1-2 libluajit-5.1-common libpq5 sysbench
0 upgraded, 4 newly installed, 0 to remove and 6 not upgraded.
Need to get 545 kB of archives.
After this operation, 1556 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 libluajit-5.1-common all 2.1.0~beta3+dfsg-6 [44.3 kB]
Get:2 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 libluajit-5.1-2 amd64 2.1.0~beta3+dfsg-6 [238 kB]
Get:3 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu jammy-updates/main amd64 libpq5 amd64 14.10-0ubuntu0.22.04.1 [143 kB]
Get:4 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu jammy/universe amd64 sysbench amd64 1.0.20+ds-2 [120 kB]
Fetched 545 kB in 0s (2318 kB/s)
Selecting previously unselected package libluajit-5.1-common.
(Reading database ... 175717 files and directories currently installed.)
Preparing to unpack .../libluajit-5.1-common_2.1.0~beta3+dfsg-6_all.deb ...
Unpacking libluajit-5.1-common (2.1.0~beta3+dfsg-6) ...
Selecting previously unselected package libluajit-5.1-2:amd64.
Preparing to unpack .../libluajit-5.1-2_2.1.0~beta3+dfsg-6_amd64.deb ...
Unpacking libluajit-5.1-2:amd64 (2.1.0~beta3+dfsg-6) ...
Selecting previously unselected package libpq5:amd64.
Preparing to unpack .../libpq5_14.10-0ubuntu0.22.04.1_amd64.deb ...
Unpacking libpq5:amd64 (14.10-0ubuntu0.22.04.1) ...
Selecting previously unselected package sysbench.
Preparing to unpack .../sysbench_1.0.20+ds-2_amd64.deb ...
Unpacking sysbench (1.0.20+ds-2) ...
Setting up libpq5:amd64 (14.10-0ubuntu0.22.04.1) ...
Setting up libluajit-5.1-common (2.1.0~beta3+dfsg-6) ...
Setting up libluajit-5.1-2:amd64 (2.1.0~beta3+dfsg-6) ...
Setting up sysbench (1.0.20+ds-2) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.5) ...
Scanning processes...
Scanning linux images...

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.</pre>


<h3 id="sysbench用のデータベースとユーザー作成">sysbench用のデータベースとユーザー作成</h3>

<p>Aurora <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> へ sysbench 用のデータベースとユーザーを作成します。</p>

<pre class="code terminal" data-lang="terminal" data-unlink>$ mysql -u admin -h ams-sample-amazon-aurora-mysql-stack.cluster-********.ap-northeast-1.rds.amazonaws.com -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 533
Server version: 8.0.28 Source distribution

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type &#39;help;&#39; or &#39;\h&#39; for help. Type &#39;\c&#39; to clear the current input statement.


mysql&gt; CREATE DATABASE sbtest;
Query OK, 1 row affected (0.00 sec)


mysql&gt; CREATE USER sbtest@&#39;%&#39; IDENTIFIED BY &#39;Password1!&#39;;
Query OK, 0 rows affected (0.01 sec)

mysql&gt; GRANT ALL PRIVILEGES ON sbtest.* TO sbtest@&#39;%&#39;
    -&gt; ;
Query OK, 0 rows affected (0.00 sec)


mysql&gt; exit
Bye</pre>




<pre class="code terminal" data-lang="terminal" data-unlink>~$ sysbench --mysql-host=ams-sample-amazon-aurora-mysql-stack.cluster-********.ap-northeast-1.rds.amazonaws.com  --mysql-user=sbtest --mysql-password=&#39;Password1!&#39; --mysql-db=sbtest --tables=3 --table_size=10000 oltp_common prepare
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Creating table &#39;sbtest1&#39;...
Inserting 10000 records into &#39;sbtest1&#39;
Creating a secondary index on &#39;sbtest1&#39;...
Creating table &#39;sbtest2&#39;...
Inserting 10000 records into &#39;sbtest2&#39;
Creating a secondary index on &#39;sbtest2&#39;...
Creating table &#39;sbtest3&#39;...
Inserting 10000 records into &#39;sbtest3&#39;
Creating a secondary index on &#39;sbtest3&#39;...</pre>


<h3 id="sysbench-実行">sysbench 実行</h3>

<p>sysbenchで書き込みのテスト<code>oltp_write_only</code>を実行します。
まずbinlogなし(binlog_format=OFF)の状態で実行します。この場合、binlog_formatはROWとなっていました。</p>

<pre class="code terminal" data-lang="terminal" data-unlink>mysql&gt; show variables like &#39;log_bin&#39;;
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| log_bin       | OFF   |
+---------------+-------+
1 row in set (0.00 sec)

mysql&gt; show variables like &#39;binlog_format&#39;;
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| binlog_format | ROW   |
+---------------+-------+
1 row in set (0.00 sec)</pre>




<pre class="code terminal" data-lang="terminal" data-unlink>$ sysbench --db-driver=mysql --mysql-host=ams-sample-amazon-aurora-mysql-stack.cluster-********.ap-northeast-1.rds.amazonaws.com  --mysql-user=sbtest --mysql-password=&#39;Password1!&#39; --mysql-db=sbtest oltp_write_only --threads=64 --time=300 run
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 64
Initializing random number generator from current time


Initializing worker threads...

Threads started!

SQL statistics:
    queries performed:
        read:                            0
        write:                           1576438
        other:                           789415
        total:                           2365853
    transactions:                        392789 (1309.05 per sec.)
    queries:                             2365853 (7884.72 per sec.)
    ignored errors:                      3837   (12.79 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          300.0539s
    total number of events:              392789

Latency (ms):
         min:                                    8.69
         avg:                                   48.88
         max:                                  515.66
         95th percentile:                       89.16
         sum:                             19200860.56

Threads fairness:
    events (avg/stddev):           6137.3281/110.83
    execution time (avg/stddev):   300.0134/0.01</pre>


<p>続いて、binlog あり(binlog_format=MIXED)の状態で実行します。
2023/12時点では、binlog_format=MIXEDがAurora <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a>の推奨のようなのでMIXEDにしています。</p>

<pre class="code terminal" data-lang="terminal" data-unlink>mysql&gt; show variables like &#39;log_bin&#39;;
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| log_bin       | ON    |
+---------------+-------+
1 row in set (0.01 sec)

mysql&gt; show variables like &#39;binlog_format&#39;;
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| binlog_format | MIXED |
+---------------+-------+
1 row in set (0.01 sec)
</pre>




<pre class="code terminal" data-lang="terminal" data-unlink>$ sysbench --db-driver=mysql --mysql-host=ams-sample-amazon-aurora-mysql-stack.cluster-********.ap-northeast-1.rds.amazonaws.com  --mysql-user=sbtest --mysql-password=&#39;Password1!&#39; --mysql-db=sbtest oltp_write_only --threads=64 --time=300 run
sysbench 1.0.20 (using system LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 64
Initializing random number generator from current time


Initializing worker threads...

Threads started!

SQL statistics:
    queries performed:
        read:                            0
        write:                           1530155
        other:                           766233
        total:                           2296388
    transactions:                        381247 (1270.62 per sec.)
    queries:                             2296388 (7653.41 per sec.)
    ignored errors:                      3739   (12.46 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          300.0462s
    total number of events:              381247

Latency (ms):
         min:                                   10.67
         avg:                                   50.36
         max:                                  524.95
         95th percentile:                       90.78
         sum:                             19201314.51

Threads fairness:
    events (avg/stddev):           5956.9844/100.13
    execution time (avg/stddev):   300.0205/0.01</pre>


<p>両方ともで <code>ignored errors</code> がそれなりに発生しており、これについては何故か確認できてません。
<code>transactions</code>, <code>queries</code> をみると大体、binlog OFFの場合に比べて、ONの場合だと97%程度のようでした。アクセスパターンによってまた変わってくるかもしれませんが、多少はパフォーマンスに影響はあるようでした。</p>

<h2 id="まとめ">まとめ</h2>

<p>Aurora <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a>でバイナリログを有効化して良いものか迷ったので、どんな影響がありそうか調べてみました。性能面では、簡単に sysbench を試した限りでは、ONの場合の方が性能は多少(2-3%程度)は落ちるようですがこれは参考値として、各環境における本番ワークロードでどの程度影響があるかは念の為見ておいた方が良さそうです。
ただ、binlog I/O cache や、 Aurora <a class="keyword" href="https://d.hatena.ne.jp/keyword/MySQL">MySQL</a> 3 では enhanced binlog という機能もリリースされており、バイナリログを別ストレージノードに書き込むことで性能向上を図る機能もあるようなので(ただし制約もあり)、これらを組み合わせることで性能向上が見込めるようです。</p>

<p>また、バイナリログがAuroraストレージに保存されるようなので、これが料金面にも多少影響しそうなので、保存期間等も検討する必要がありそうです。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://dev.mysql.com/doc/refman/8.0/en/binary-log.html">MySQL :: MySQL 8.0 Reference Manual :: 5.4.4 The Binary Log</a></li>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html#AuroraMySQL.Replication.MySQL.RetainBinlogs">Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster (binary log replication) - Amazon Aurora</a></li>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html">Amazon Aurora storage and reliability - Amazon Aurora</a></li>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.AuroraHighAvailability.html#Aurora.Managing.FaultTolerance">High availability for Amazon Aurora - Amazon Aurora</a></li>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.MySQL80.html#ReducedRestartTime">Aurora MySQL version 3 compatible with MySQL 8.0 - Amazon Aurora</a></li>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.AuroraMySQL.Compare-v2-v3.html#AuroraMySQL.mysql80-binlog">Comparison of Aurora MySQL version 2 and Aurora MySQL version 3 - Amazon Aurora</a></li>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Managing.Backtrack.html">Backtracking an Aurora DB cluster - Amazon Aurora</a></li>
<li><a href="https://aws.amazon.com/jp/blogs/database/introducing-amazon-aurora-mysql-enhanced-binary-log-binlog/">Introducing Amazon Aurora MySQL enhanced binary log (binlog) | AWS Database Blog</a></li>
<li><a href="https://aws.amazon.com/jp/blogs/database/introducing-binlog-i-o-cache-in-amazon-aurora-mysql-to-improve-binlog-performance/">Introducing binlog I/O cache in Amazon Aurora MySQL to improve binlog performance | AWS Database Blog</a></li>
<li><a href="https://aws.amazon.com/jp/about-aws/whats-new/2023/05/aurora-mysql-performance-failover-recovery-time-binlog-enabled/">Aurora MySQL &#x304C;&#x3001;binlog &#x304C;&#x6709;&#x52B9;&#x306A;&#x5834;&#x5408;&#x306E;&#x30D1;&#x30D5;&#x30A9;&#x30FC;&#x30DE;&#x30F3;&#x30B9;&#x3068;&#x30D5;&#x30A7;&#x30A4;&#x30EB;&#x30AA;&#x30FC;&#x30D0;&#x30FC;&#x306E;&#x5FA9;&#x65E7;&#x6642;&#x9593;&#x3092;&#x6539;&#x5584;</a></li>
<li><a href="https://pages.awscloud.com/rs/112-TZM-766/images/D2-05.pdf">https://pages.awscloud.com/rs/112-TZM-766/images/D2-05.pdf</a></li>
<li><a href="https://repost.aws/ja/knowledge-center/aurora-mysql-increase-binlog-retention">Aurora MySQL &#x4E92;&#x63DB; DB &#x30AF;&#x30E9;&#x30B9;&#x30BF;&#x30FC;&#x3067;&#x306E;&#x30D0;&#x30A4;&#x30CA;&#x30EA;&#x30ED;&#x30B0;&#x306E;&#x4FDD;&#x6301;&#x671F;&#x9593;&#x3092;&#x5897;&#x3084;&#x3059; | AWS re:Post</a></li>
<li><a href="https://aws.amazon.com/jp/blogs/news/running-sysbench-on-rds-mysql-rds-mariadb-and-amazon-aurora-mysql-via-ssl-tls-2/">SSL/TLS &#x3092;&#x7D4C;&#x7531;&#x3057;&#x3066; RDS MySQL, RDS MariaDB, and Amazon Aurora MySQL &#x306B; sysbench &#x3092;&#x5B9F;&#x884C;&#x3059;&#x308B; | Amazon Web Services &#x30D6;&#x30ED;&#x30B0;</a></li>
<li><a href="https://developers.cyberagent.co.jp/blog/archives/29925/">Aurora MySQL &#x306E;&#x30D0;&#x30C3;&#x30AF;&#x30A2;&#x30C3;&#x30D7;&#x306F;&#x672C;&#x5F53;&#x306B;&#x305D;&#x308C;&#x3067;&#x3044;&#x3044;&#x306E;&#x3060;&#x308D;&#x3046;&#x304B;&#xFF1F; | CyberAgent Developers Blog</a></li>
</ul>


