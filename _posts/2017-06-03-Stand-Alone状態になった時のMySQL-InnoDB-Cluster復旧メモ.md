---
layout: post
Categories:  ["tech"]
Description:  " 手元の環境でInnoDB Clusterに含まれるMySQLを全て手動で停止させたあとに、 mysqlsh経由でClusterを起動するため、メタデータストアからクラスタ情報を取得しようとすると以下のエラーが発生しました。  mysql-"
Tags: ["MySQL", "tech"]
date: "2017-06-03T16:17:00+09:00"
title: "Stand Alone状態になった時のMySQL InnoDB Cluster復旧メモ"
author: "dshimizu"
archive: ["2017"]
draft: "true"
---

<body>
<p>手元の環境で<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Clusterに含まれる<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>を全て手動で停止させたあとに、 <code>mysqlsh</code>経由でClusterを起動するため、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E1%A5%BF%A5%C7%A1%BC%A5%BF">メタデータ</a>ストアから<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>情報を取得しようとすると以下のエラーが発生しました。</p>

<pre class="terminal"> mysql-js&gt; var cluster = dba.getCluster('ClusterDev')
Dba.getCluster: This function is not available through a session to a standalone instance (RuntimeError)
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>内のどのサーバも切り離されてシングル構成となってしまい、<code>mysqlsh</code>経由から<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>情報を取得できなくなったようです。</p>

<p>詳しいことはよくわかっていませんが、<code>mysqlsh</code>だけでは解決できず、各<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>へのオペレーションも必要でしたので、復旧時にやったことをメモしておきます。
ただ、以下に記載するやり方が正しいのかはわかりません。</p>
</body>

<!-- more -->

<body>
<h3>前提</h3>


<p>元の構成は以下です(<a href="https://blog.dshimizu.jp/article/475" target="_blank" rel="noopener noreferrer">以前の記事</a>の際に構成したものです)。</p>

<p><img src="http://blog.dshimizu.jp/wp-content/uploads/2017/05/mysql-innodb-cluster-configuration-diagram.png" alt="" width="571" height="453" class="alignnone size-full wp-image-493"></p>

<h3>復旧のためにやったこと</h3>


<p>まず各<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>の<code>gtid_executed</code>の値を確認します。
この中で gtid_executed の値が最も新しいものを確認します。</p>

<p><b>mysql1</b></p>

<pre class="terminal"> mysql1 &gt; show global variables like "gtid%";
+---------------------------------------------------+------------------------------------------------------------------------------------------------------+
| Variable_name                                     | Value                                                                                                |
+---------------------------------------------------+------------------------------------------------------------------------------------------------------+
| gtid_executed                                     | 05404f62-992b-11e7-aaae-525400ba38ee:1-11,
72b0d919-99d8-11e7-9dc9-525400ba38ee:1-39:1000011-1000017 |
| gtid_executed_compression_period                  | 1000                                                                                                 |
| gtid_mode                                         | ON                                                                                                   |
| gtid_owned                                        |                                                                                                      |
| gtid_purged                                       |                                                                                                      |
+---------------------------------------------------+------------------------------------------------------------------------------------------------------+
5 rows in set (0.00 sec)
 </pre>


<p><b>mysql2</b></p>

<pre class="terminal"> mysql2 &gt; show global variables like "gtid%";
+---------------------------------------------------+----------------------------------------------------------------------------------------------------+
| Variable_name                                     | Value                                                                                              |
+---------------------------------------------------+----------------------------------------------------------------------------------------------------+
| gtid_executed                                     | 05404f62-992b-11e7-aaae-525400ba38ee:1-3,
72b0d919-99d8-11e7-9dc9-525400ba38ee:1-8:1000007-1000008 |
| gtid_executed_compression_period                  | 1000                                                                                               |
| gtid_mode                                         | ON                                                                                                 |
| gtid_owned                                        |                                                                                                    |
| gtid_purged                                       |                                                                                                    |
+---------------------------------------------------+----------------------------------------------------------------------------------------------------+
5 rows in set (0.00 sec)
 </pre>


<p><b>mysql3</b></p>

<pre class="terminal"> mysql3 &gt; show global variables like "%gtid%";
+---------------------------------------------------+------------------------------------------------------------------------------------+
| Variable_name                                     | Value                                                                              |
+---------------------------------------------------+------------------------------------------------------------------------------------+
| gtid_executed                                     | 05404f62-992b-11e7-aaae-525400ba38ee:1-3,
72b0d919-99d8-11e7-9dc9-525400ba38ee:1-8 |
| gtid_executed_compression_period                  | 1000                                                                               |
| gtid_mode                                         | ON                                                                                 |
| gtid_owned                                        |                                                                                    |
| gtid_purged                                       |                                                                                    |
+---------------------------------------------------+------------------------------------------------------------------------------------+
5 rows in set (0.00 sec)
 </pre>


<h4>未実行の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>が記録されたバイナリログが残っている場合</h4>


<p>gtid_executed の値が最も新しいサーバの<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>(この場合は mysql1 )に未実行の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>が記録されたバイナリログが残っていれば、<code>mysqlsh</code>からその<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>に接続して、<code>rebootClusterFromCompleteOutage()</code>を呼び出すことで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>を再構成して、その後にバイナリログを元にデータ同期が始まり、自動的に復旧できるはずです。
<code>rebootClusterFromCompleteOutage()</code>はこれは<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>を復元するためのメソッドのようです。</p>

<pre class="terminal"> mysql-js&gt; shell.connect('icroot@192.168.10.1:24801')
Please provide the password for 'icroot@192.168.10.1:24801':
Creating a Session to 'icroot@192.168.10.1:24801'
Classic Session successfully established. No default schema selected.
 </pre>


<p>[terminal]
<a class="keyword" href="http://d.hatena.ne.jp/keyword/mysql">mysql</a>-js&gt; dba.rebootClusterFromCompleteOutage()
Reconfiguring the default cluster from complete outage...</p>

<p>The cluster was successfully rebooted.</p>

<p>&lt;cluster:cluster name=""&gt;
[/terminal]&lt;/cluster:cluster&gt;</p>

<p>この時、以下のように <code>The current session instance does not belong to the cluster:</code> となってしまった場合は、一度<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shellを終了させて、再度起動して接続させることでうまくいくと思います。
(もしダメな場合は<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>を再起動するとうまくいくかもしれません。)</p>

<pre class="terminal"> mysql-js&gt; dba.rebootClusterFromCompleteOutage()
Reconfiguring the default cluster from complete outage...

The instance 'icroot@192.168.10.1:24801' was part of the cluster configuration.
Would you like to rejoin it to the cluster? [y|N]: y

Dba.rebootClusterFromCompleteOutage: Dba.rebootClusterFromCompleteOutage: The current session instance does not belong to the cluster: 'Cluster Name'. (RuntimeError)
 </pre>


<p>これで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E1%A5%BF%A5%C7%A1%BC%A5%BF">メタデータ</a>にアクセスできます。</p>

<pre class="terminal"> mysql-js&gt; var cluster = dba.getCluster('ClusterDev')
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>のステータスを確認します。</p>

<pre class="terminal"> mysql-js&gt; cluster.status();
{
    "clusterName": "ClusterDev",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "192.168.10.1:24801",
        "status": "OK_NO_TOLERANCE",
        "statusText": "Cluster is NOT tolerant to any failures. 2 members are not active",
        "topology": {
            "192.168.10.1:24801": {
                "address": "192.168.10.1:24801",
                "mode": "R/W",
                "readReplicas": {},
                "role": "HA",
                "status": "ONLINE"
            },
        }
    }
}
 </pre>


<p>mysql2 <code>192.168.10.1:24802</code>を追加します。</p>

<pre class="terminal"> mysql-js&gt; cluster.addInstance('icroot@192.168.10.2:24802')
A new instance will be added to the InnoDB cluster. Depending on the amount of
data on the cluster this might take from a few seconds to several hours.

Please provide the password for 'icroot@192.168.10.2:24802':
Adding instance to the cluster ...

The instance 'icroot@192.168.10.2:24802' was successfully added to the cluster.

 </pre>


<p>ステータスが RECOVERING となり、データ同期が始まります。</p>

<pre class="terminal"> mysql-js&gt; cluster.status()
{

    "clusterName": "ClusterDev",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "192.168.10.1:24801",
        "status": "OK_NO_TOLERANCE",
        "statusText": "Cluster is NOT tolerant to any failures. 2 members are not active",
        "topology": {
            "192.168.10.1:24801": {
                "address": "192.168.10.1:24801",
                "mode": "R/W",
                "readReplicas": {},
                "role": "HA",
                "status": "ONLINE"
            },
            "192.168.10.2:24802": {
                "address": "192.168.10.2:24802",
                "mode": "R/O",
                "readReplicas": {},
                "role": "HA",
                "status": "RECOVERING"
            }
        }
    }
}
 </pre>


<p>mysql2の error.log に以下のようなログが出力されていればデータ同期が正常に開始されています。</p>

<pre class="terminal"> 2017-05-261T16:40:06.552022+09:00 136485 [Note] Plugin group_replication reported: 'This server is working as secondary member with primary member address 192.168.10.1:24801.'
2017-05-26T16:40:06.552101+09:00 0 [ERROR] Plugin group_replication reported: 'Group contains 3 members which is greater than group_replication_auto_increment_increment value of 1. This can lead to an higher rate of transactional aborts.'
2017-05-26T16:40:06.552610+09:00 136493 [Note] Plugin group_replication reported: 'Establishing group recovery connection with a possible donor. Attempt 1/10'
2017-05-26T16:40:06.552819+09:00 0 [Note] Plugin group_replication reported: 'Group membership changed to 192.168.10.3:24803, 192.168.10.1:24801, 192.168.10.2:24802 on view 15283536049318228:3.'
2017-05-26T16:40:06.556755+09:00 136493 [Note] 'CHANGE MASTER TO FOR CHANNEL 'group_replication_recovery' executed'. Previous state master_host='', master_port= 0, master_log_file='', master_log_pos= 4, master_bind=''. New state master_host='192.168.10.1', master_port= 24801, master_log_file='', master_log_pos= 4, master_bind=''.
2017-05-26T16:40:06.561195+09:00 136493 [Note] Plugin group_replication reported: 'Establishing connection to a group replication recovery donor ********-****-****-****-******** at 192.168.10.1 port: 24801.'
2017-05-26T16:40:06.561475+09:00 136495 [Warning] Storing MySQL user name or password information in the master info repository is not secure and is therefore not recommended. Please consider using the USER and PASSWORD connection options for START SLAVE; see the 'START SLAVE Syntax' in the MySQL Manual for more information.
2017-05-26T16:40:06.562177+09:00 136496 [Note] Slave SQL thread for channel 'group_replication_recovery' initialized, starting replication in log 'FIRST' at position 0, relay log './mysql3-relay-bin-group_replication_recovery.000001' position: 4
2017-05-26T16:40:06.672847+09:00 136495 [Note] Slave I/O thread for channel 'group_replication_recovery': connected to master 'mysql_innodb_cluster_r**********@192.168.10.1:24801',replication started in log 'FIRST' at position 4
 </pre>


<p>リトライが繰り返されるようだとどこかに問題があると思われます。</p>

<p>mysql3 <code>192.168.10.1:24803</code>も追加します。
2台同時にデータ同期のためにバイナリログのデータが流れるので、ネットワーク帯域に余裕がない場合は1台ずつタイミングをずらして行います。</p>

<pre class="terminal"> mysql-js&gt; cluster.addInstance('icroot@192.168.10.3:24803')
A new instance will be added to the InnoDB cluster. Depending on the amount of
data on the cluster this might take from a few seconds to several hours.

Please provide the password for 'icroot@192.168.10.3:24803':
Adding instance to the cluster ...

The instance 'icroot@192.168.10.3:24803' was successfully added to the cluster.

 </pre>


<p>ステータスが RECOVERING となり、データ同期が始まります。</p>

<pre class="terminal"> mysql-js&gt; cluster.status()
{

    "clusterName": "ClusterDev",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "192.168.10.1:24801",
        "status": "OK_NO_TOLERANCE",
        "statusText": "Cluster is NOT tolerant to any failures. 2 members are not active",
        "topology": {
            "192.168.10.1:24801": {
                "address": "192.168.10.1:24801",
                "mode": "R/W",
                "readReplicas": {},
                "role": "HA",
                "status": "ONLINE"
            },
            "192.168.10.2:24802": {
                "address": "192.168.10.2:24802",
                "mode": "R/O",
                "readReplicas": {},
                "role": "HA",
                "status": "RECOVERING"
            },
            "192.168.10.3:24803": {
                "address": "192.168.10.2:24803",
                "mode": "R/O",
                "readReplicas": {},
                "role": "HA",
                "status": "RECOVERING"
            }
        }
    }
}
 </pre>


<p>しばらく待って全ノードのステータスがONLINEになれば完了です。</p>

<pre class="terminal"> mysql-js&gt; cluster.status();
{
    "clusterName": "ClusterDev",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "192.168.28.251:24801",
        "status": "OK_NO_TOLERANCE",
        "statusText": "Cluster is NOT tolerant to any failures. 1 member is not active",
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


<h4>未実行の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>が記録されたバイナリログが残っていない場合</h4>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>が切り離されて以降のすべてのバイナリログが残っていない場合、実行すべき<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>そのものの情報がなくデータ同期が行えないので、最新のデータを保持している<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>(この場合は mysql1 )から mysqldump等でバックアップを取得して、それを他のサーバにインポートする必要があります。
(たぶん壊れ方によっては <a class="keyword" href="http://d.hatena.ne.jp/keyword/mysql">mysql</a>_<a class="keyword" href="http://d.hatena.ne.jp/keyword/innodb">innodb</a>_cluster_metadata のデータがおかしくなってそうなので、その時に全データダンプ取得して他のサーバにインポートするとさらにおかしくなりそうなので、その時は必要なデータを個別にバックアップするか metadata も消して一から構築し直すことになります)</p>

<p>この場合はバックアップを取得します。</p>

<p>※5.7.18では、Group Replication中だとSavepoint がサポートされていないため失敗します。</p>

<ul>
    <li><a href="https://bugs.mysql.com/bug.php?id=81494" target="_brank" rel="noopener noreferrer">MySQL Bugs: #81494: mysqldump can't get all databases backup with Group Replication</a></li>
</ul>


<pre class="terminal"> $ mysqldump --all-databases --lock-all-tables -uroot --triggers --routines --events -p &gt; backup.sql
 </pre>


<p>このダンプデータをほかの<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>(この場合は mysql2, mysql3 )にインポートします。
<code>reset master</code>を実行してバイナリログやGTIDもリセットします。</p>

<pre class="terminal"> mysql2&gt; reset master;

mysql3&gt; source /tmp/backup.sql
 </pre>


<pre class="terminal"> mysql3&gt; reset master;

mysql3&gt; source /tmp/backup.sql
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shellから<code>rebootClusterFromCompleteOutage</code>メソッドを呼び出します。
この時、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーに参加していた<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>の一部を再度<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーに参加するか問われますので、yes(y)を選択します。</p>

<pre class="terminal"> mysql-js&gt; shell.connect('icroot@192.168.10.1:24801')
Please provide the password for 'icroot@192.168.10.1:24801':
Creating a Session to 'icroot@192.168.10.1:24801'
Classic Session successfully established. No default schema selected.
 </pre>


<pre class="terminal"> mysql-js&gt; dba.rebootClusterFromCompleteOutage()
Reconfiguring the default cluster from complete outage...

The instance '192.168.10.1:24802' was part of the cluster configuration.
Would you like to rejoin it to the cluster? [y|N]: y

The instance '192.168.10.1:24803' was part of the cluster configuration.
Would you like to rejoin it to the cluster? [y|N]: y


The cluster was successfully rebooted.


 </pre>


<p>これで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E1%A5%BF%A5%C7%A1%BC%A5%BF">メタデータ</a>ストアから<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E1%A5%BF%A5%C7%A1%BC%A5%BF">メタデータ</a>を取得できます。</p>

<pre class="terminal"> mysql-js&gt; var cluster = dba.getCluster('ClusterDev')
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>のステータスを確認します。
Read-Onlyの<code>192.168.10.1:24802</code>, <code>192.168.10.1:24803</code>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>が<code>MISSING</code>となっていますのでこれを復旧させる必要があります。</p>

<pre class="terminal"> mysql-js&gt; cluster.status();
{
    "clusterName": "ClusterDev",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "192.168.10.1:24801",
        "status": "OK_NO_TOLERANCE",
        "statusText": "Cluster is NOT tolerant to any failures. 2 members are not active",
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
                "status": "(MISSING)"
            },
            "192.168.10.1:24803": {
                "address": "192.168.10.1:24803",
                "mode": "R/O",
                "readReplicas": {},
                "role": "HA",
                "status": "(MISSING)"
            }
        }
    }
}
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>をrejoinしようとしましたが以下のエラーが発生して失敗しました。</p>

<pre class="terminal"> mysql-js&gt; cluster.rejoinInstance('icroot@192.168.10.1:24802');
Rejoining the instance to the InnoDB cluster. Depending on the original
problem that made the instance unavailable, the rejoin operation might not be
successful and further manual steps will be needed to fix the underlying
problem.

Please monitor the output of the rejoin operation and take necessary action if
the instance cannot rejoin.

Please provide the password for 'icroot@192.168.10.1:24802':
Rejoining instance to the cluster ...

Cluster.rejoinInstance: Access denied for user 'root'@'192.168.10.1' (using password: YES) (MySQL Error 1045)
 </pre>


<p>rejoinInstance（）はrootアカウントを使用して<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>が作成されたことを前提としていた実装になっていたため、ユーザーが 'root'以外のアカウントを使用した場合、コマンドは失敗するようです。
すでにパッチが作成されているようですが、ここでは別な方法を取ることにします。</p>

<p><a href="https://bugs.mysql.com/bug.php?id=81494" target="_brank" rel="noopener noreferrer"></a><a href="https://bugs.mysql.com/bug.php?id=81494" target="_brank" rel="noopener noreferrer"></a></p>

<ul>
<a href="https://bugs.mysql.com/bug.php?id=81494" target="_brank" rel="noopener noreferrer">
</a>
    <li>
<a href="https://bugs.mysql.com/bug.php?id=81494" target="_brank" rel="noopener noreferrer"></a><a href="https://bugs.mysql.com/bug.php?id=85567" target="_blank" rel="noopener noreferrer">MySQL Bugs: #85567: cluster.rejoinInstance() doesn't use the user in the connection string parameter</a>
</li>
</ul>


<p>一旦、対象<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>に対して<code>removeInstance</code>を発行して<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>から切り離します。</p>

<pre class="terminal"> mysql-js&gt; cluster.removeInstance('icroot@192.168.10.1:24802');
The instance will be removed from the InnoDB cluster. Depending on the
instance being the Seed or not, the Metadata session might become invalid.
If so, please start a new session to the Metadata Storage R/W instance.

Cluster.removeInstance: The instance 'icroot@192.168.10.1:24802' does not belong to the ReplicaSet: 'default'. (RuntimeError)
 </pre>


<p>再度対象<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>に追加します。</p>

<pre class="terminal"> mysql-js&gt; cluster.addInstance('icroot@192.168.10.1:24802');
A new instance will be added to the InnoDB cluster. Depending on the amount of
data on the cluster this might take from a few seconds to several hours.

Please provide the password for 'icroot@localhost:24802':
Adding instance to the cluster ...

The instance 'icroot@192.168.10.1:24802' was successfully added to the cluster.
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>のステータスを確認します。
<code>192.168.10.1:24802</code>がONLINEになっていることが確認できます。</p>

<pre class="terminal"> mysql-js&gt; cluster.status();
{
    "clusterName": "ClusterDev",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "192.168.28.251:24801",
        "status": "OK_NO_TOLERANCE",
        "statusText": "Cluster is NOT tolerant to any failures. 1 member is not active",
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
                "status": "(MISSING)"
            }
        }
    }
}
 </pre>


<p>同様に<code>192.168.10.1:24803</code>に対しても<code>removeInstance</code>, <code>addInstance</code>を発行します。</p>

<pre class="terminal"> The instance will be removed from the InnoDB cluster. Depending on the
instance being the Seed or not, the Metadata session might become invalid.
If so, please start a new session to the Metadata Storage R/W instance.

The instance 'icroot@192.168.28.251:24803' was successfully removed from the cluster.
 </pre>


<pre class="terminal"> mysql-js&gt; cluster.addInstance('icroot@192.168.28.251:24803');
A new instance will be added to the InnoDB cluster. Depending on the amount of
data on the cluster this might take from a few seconds to several hours.

Please provide the password for 'icroot@192.168.28.251:24803':
Adding instance to the cluster ...

The instance 'icroot@192.168.28.251:24803' was successfully added to the cluster.
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>内の全ての<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>がオンラインになっていることを確認します。</p>

<pre class="terminal"> mysql-js&gt; cluster.status();
{
    "clusterName": "ClusterDev",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "192.168.28.251:24801",
        "status": "OK_NO_TOLERANCE",
        "statusText": "Cluster is NOT tolerant to any failures. 1 member is not active",
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


<h3>まとめ</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>そのものが止まった時に復旧させた際の手順を書きました。
今思えば <a class="keyword" href="http://d.hatena.ne.jp/keyword/mysql">mysql</a>_<a class="keyword" href="http://d.hatena.ne.jp/keyword/innodb">innodb</a>_cluster_metadata の中の情報も取得しておけばよかったなぁと思うので、次同じことが起こったら確認してみようと思います。</p>

<h3>参考</h3>


<ul>
    <li><a href="https://dev.mysql.com/doc/dev/mysqlsh-api-javascript/classmysqlsh_1_1dba_1_1_dba.html" target="_blank" rel="noopener noreferrer">MySQL Shell: Dba Class Reference</a></li>
    <li><a href="https://forums.mysql.com/read.php?177,653826,654972#msg-654972" target="_blank" rel="noopener noreferrer">MySQL :: Re: InnoDB Cluster: Navigating the Cluster</a></li>
</ul>

</body>
