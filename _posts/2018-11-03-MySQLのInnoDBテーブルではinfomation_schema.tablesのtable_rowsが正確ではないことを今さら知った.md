---
layout: post
Categories:  ["tech"]
Description:  " いろいろネット上で調べていると既知のもののようで何を今さら...という感じではあるが、MySQLで全テーブルのレコード数を一括で取得するのに手軽にやれないかと思ってinfomation_schema.tablesのtable_rowsを参"
Tags: ["MySQL", "tech"]
date: "2018-11-03T19:54:00+09:00"
title: "MySQLのInnoDBテーブルではinfomation_schema.tablesのtable_rowsが正確ではないことを今さら知った"
author: "dshimizu"
archive: ["2018"]
draft: "true"
---

<body>
<p>いろいろネット上で調べていると既知のもののようで何を今さら...という感じではあるが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>で全テーブルのレコード数を一括で取得するのに手軽にやれないかと思って<code>infomation_schema.tables</code>の<code>table_rows</code>を参照したら、マスター/スレーブ間で件数が全然違って焦った。ちなみにテーブルの行数をcountで取得したら一致した。何故じゃ...と思ったが結論から言うとそういう仕様だった。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://dev.mysql.com/doc/refman/5.7/en/tables-table.html">MySQL :: MySQL 5.7 Reference Manual :: 24.25 The INFORMATION_SCHEMA TABLES Table</a></li>
</ul>

</body>

<!-- more -->

<body>
<h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a>のテーブルで行数を数えるには</h3>


<p>やってみたのは以下のような感じで<code>information_schema.tables</code>の<code>table_rows</code>を参照してみたが、これだと<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a>では結構なブレが発生する。</p>

<pre class="terminal"> &gt; select
  table_name
  , table_rows
from
  information_schema.tables
where
  table_schema = 'sampledb';
 </pre>


<p>ちなみに<code>show</code>コマンドも内部では<code>information_schema.tables</code>を参照しているので同じく正確な値は取れない。</p>

<pre class="terminal"> &gt; show table status from sampledb;
 </pre>


<p>ということで正確な件数を取得するには以下のように<code>select count(*)</code>を使うしかない、と思われる。</p>

<pre class="terminal"> &gt; select count(*) from sampledb.sampletable
 </pre>


<p>全テーブルの件数を一括で取得するには<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>を作成するしかなさそう。</p>

<p>ちなみに話は変わるけどMySQL5.6なら<code>mysql_config_editor</code>を使って暗号化された接続情報ファイルを作成できる。これを使うことでパスワードを<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>内に書く必要はなくなる。MySQL5.5なら<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>とかにパスワードとかの接続情報を書くしかないと思う。</p>

<pre class="terminal"> $ mysql_config_editor set --login-path=test --host=localhost --user=test --password
Enter password:

$ mysql_config_editor print --all
[test]
user = test
password = *****
host = localhost
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>は以下のような感じ。</p>

<ul>
    <li><code>get-table-count.sh</code></li>
</ul>
<pre class="terminal"> #!/bin/bash

# DB接続情報情報(～MySQL5.5)
DB_USER=
DB_PASSWORD=hogehoge
DB_NAME=sampledb
DB_HOST=localhost

# DB接続情報情報(MySQL5.6～)
MYSQL_LOGIN_PATH=test

# テーブル一覧取得(～MySQL5.5)
#TABLES=(`mysql -u ${DB_USER} -p${DB_PASSWORD} -D ${DB_NAME} -N -e "show tables"`)

# テーブル一覧取得(MySQL5.6～)
TABLES=(`mysql --login-path=${MYSQL_LOGIN_PATH} -N -e "show tables"`)

#echo ${TABLES}

# show tables に出力されるテーブルの件数を一括で取得するためのSQL文生成
for TABLE in ${TABLES[@]};
do
    # テーブル1個ずつ件数を取得して結合するSQL
    SQL="${SQL} SELECT '${TABLE}' AS table_name, COUNT(*) AS table_row_cnt FROM ${TABLE} UNION ALL"
done

# 上記の方法で最後に作成したSQL文にも UNION ALL が付与されてしまうのでそれだけ削除
SQL="$(echo $SQL | sed -e 's/ UNION ALL$//')"
#echo ${SQL}

# テーブルの件数を一括で取得するSQL 文実行(～MySQL5.5)
#mysql  -u ${DB_USER} -p${DB_PASSWORD} -D ${DB_NAME} -e "${SQL}"

# テーブルの件数を一括で取得するSQL 文実行(MySQL5.6～)
mysql --login-path=${MYSQL_LOGIN_PATH} -e "${SQL}"
 </pre>



<p>実行してみる。</p>

<pre class="terminal"> ./get-table-count.sh
 </pre>


<h3>まとめ</h3>


<p>少なくとも<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>の5.5,5.6,5.7では<a class="keyword" href="http://d.hatena.ne.jp/keyword/InnoDB">InnoDB</a>の各テーブルの件数を取得するときにinfomation_schema.tablesのtable_rowsの値は正確ではないので参照してはいけない旨を書いた。
一括で取得するには各テーブルに対して<code>select count(*)</code>を実行するしかない模様。</p>

<h3>参考</h3>


<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://dev.mysql.com/doc/refman/5.5/en/tables-table.html">MySQL 5.5 Reference Manual :: 20.21 The INFORMATION_SCHEMA TABLES Table</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://gihyo.jp/dev/serial/01/mysql-road-construction-news/0057">第57回　mysql_config_editorを試してみよう：MySQL道普請便り｜gihyo.jp … 技術評論社</a></li>
</ul>

</body>
