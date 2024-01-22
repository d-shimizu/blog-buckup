---
layout: post
Categories:  ["tech"]
Description:  " 肝心の「何故こうなったのか」の原因は判明していないけど、MICの検証をしていて、クラスタを止めたり疑似障害を起こしたりしていりしていた時の事。MySQL Shellを使ってメタデータからクラスタをを取得しようとしたときに以下のようなエラー"
Tags: ["MySQL", "tech"]
date: "2017-11-11T14:41:00+09:00"
title: "MySQL InnoDB Clusterのクラスタ名が変わってしまったのでメタデータを直接更新して復活させた"
author: "dshimizu"
archive: ["2017"]
draft: "true"
---

<body>
<p>肝心の「何故こうなったのか」の原因は判明していないけど、MICの検証をしていて、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>を止めたり疑似障害を起こしたりしていりしていた時の事。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shellを使って<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E1%A5%BF%A5%C7%A1%BC%A5%BF">メタデータ</a>から<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>をを取得しようとしたときに以下のようなエラーが出るようになってしまいました。</p>
</body>

<!-- more -->

<body>
<pre class="terminal"> mysql-js&gt; shell.connect('192.168.***.***:3306')
Please provide the password for 'icroot@192.168.***.***:3306':
Creating a Session to 'icroot@192.168.***.***::3306'
Your MySQL connection id is 45
No default schema selected; type \use  to set one.

mysql-js&gt; var cluster = dba.getCluster('devCluster');
Dba.getCluster: Dba.getCluster: Unable to get cluster. The instance '192.168.***.***:3306' may belong to a different ReplicaSet as the one registered in the Metadata since the value of 'group_replication_group_name' does not match the one registered in the ReplicaSet's Metadata: possible split-brain scenario. Please connect to another member of the ReplicaSet to get the Cluster. (RuntimeError)
 </pre>


<h3>エラー内容と対応策</h3>


<h4>環境</h4>


<ul>
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> 16.04</li>
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Server 5.7.19(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Oracle">Oracle</a>公式<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/deb">deb</a>パッケージ)</li>
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shell 1.0.10(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Oracle">Oracle</a>公式<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/deb">deb</a>パッケージ)</li>
</ul>


<h4>対応</h4>


<p>エラーメッセージの通りで <code>replication_group_name</code> の値が、設定ファイル内の <code>loose-group_replication_group_name</code> にセットしてある値とは別のものに変わってしまっているようでした。</p>

<pre class="terminal"> root@mysql1[mysql_innodb_cluster_metadata] &gt; select * from replicasets;
+---------------+------------+-----------------+---------------+-----------------+--------+--------------------------------------------------------------------------+-------------+
| replicaset_id | cluster_id | replicaset_type | topology_type | replicaset_name | active | attributes                                                               | description |
+---------------+------------+-----------------+---------------+-----------------+--------+--------------------------------------------------------------------------+-------------+
|             1 |          1 | gr              | pm            | default         |      1 | {"group_replication_group_name": "86cecd19-****-4295-8da3-************"} | NULL        |
+---------------+------------+-----------------+---------------+-----------------+--------+--------------------------------------------------------------------------+-------------+
1 row in set (0.00 sec)
 </pre>


<p><code>/etc/mysql/mysql.conf.d/mysqld.cnf</code>内の<code>loose-group_replication_group_name</code>の値</p>

<pre class="terminal"> loose-group_replication_group_name = "59ab44ec-****-48cf-ad33-************"
 </pre>


<p><code>start group_replication</code>は実行できるものの、このままでは<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shellから<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>の管理ができないので、手動で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E1%A5%BF%A5%C7%A1%BC%A5%BF">メタデータ</a>を更新する対応を取りました。</p>

<pre class="terminal"> root@mysql1[mysql_innodb_cluster_metadata] &gt; UPDATE mysql_innodb_cluster_metadata.replicasets SET attributes='{"group_replication_group_name": "59ab44ec-****-48cf-ad33-************"}';
Query OK, 1 row affected (0.03 sec)

root@mysql1[mysql_innodb_cluster_metadata] &gt; select * from replicasets;
+---------------+------------+-----------------+---------------+-----------------+--------+--------------------------------------------------------------------------+-------------+
| replicaset_id | cluster_id | replicaset_type | topology_type | replicaset_name | active | attributes                                                               | description |
+---------------+------------+-----------------+---------------+-----------------+--------+--------------------------------------------------------------------------+-------------+
|             1 |          1 | gr              | pm            | default         |      1 | {"group_replication_group_name": "59ab44ec-****-48cf-ad33-************"} | NULL        |
+---------------+------------+-----------------+---------------+-----------------+--------+--------------------------------------------------------------------------+-------------+
 </pre>


<p>再度<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> Shellを使って<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>情報を取得します。</p>

<pre class="terminal"> mysql-js&gt; var cluster = dba.getCluster('devCluster');
 </pre>


<p>取得できました。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>のステータスを確認してみます。</p>

<pre class="terminal"> mysql-js&gt; cluster.status();
{
    "clusterName": "ClusterDev",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "192.168.***.***:3306",
        "status": "OK",
        "statusText": "Cluster is ONLINE and can tolerate up to ONE failure.",
        "topology": {
            "192.168.***.***:3306": {
                "address": "192.168.***.***:3306",
                "mode": "R/O",
                "readReplicas": {},
                "role": "HA",
                "status": "ONLINE"
            },
            "192.168.***.***:3306": {
                "address": "192.168.***.***:3306",
                "mode": "R/O",
                "readReplicas": {},
                "role": "HA",
                "status": "ONLINE"
            },
            "192.168.34.77:3306": {
                "address": "192.168.***.***:3306",
                "mode": "R/W",
                "readReplicas": {},
                "role": "HA",
                "status": "ONLINE"
            }
        }
    }
}
 </pre>


<p>表示されました。
ざっと見たところ正常に動作しているようです。</p>

<h3>まとめ</h3>


<p>何故か<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a> Clusterの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E1%A5%BF%A5%C7%A1%BC%A5%BF">メタデータ</a>に保存されている<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>名が、設定ファイルの値と変わってしまう事象が発生してしまったので、その復旧について書きました。
復旧の仕方がこれで正しいのかわかりませんが…
何故変わってしまったのか原因がわかったら別途書こうと思います。</p>
</body>
