---
layout: post
Categories:  ["tech"]
Description:  " 最近 PostgreSQL で動いているシステムに携わるようになって、しばらく MySQL ばかり触っていたので、PostgreSQL の障害調査の時とかに四苦八苦するので、使えそうな SQL とかコマンドの類をメモとして書いておく。 "
Tags: ["PostgreSQL", "tech"]
date: "2020-10-10T23:25:00+09:00"
title: "PostgreSQL のトラブル時に使うクエリなどのメモ書き"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p>最近 <a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a> で動いているシステムに携わるようになって、しばらく <a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a> ばかり触っていたので、<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a> の障害調査の時とかに四苦八苦するので、使えそうな <a class="keyword" href="http://d.hatena.ne.jp/keyword/SQL">SQL</a> とかコマンドの類をメモとして書いておく。</p>
</body>

<!-- more -->

<body>
<h4>現在実行中のクエリを確認する</h4>


<p><code>pg_stat_activity</code>ビューに、<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>の各サーバープロセスのに関する情報を持っているので、これを参照する。</p>

<pre class="terminal"> select
    *
from
    pg_stat_activity
where
    state != 'idle'
order by
    query_start
asc;
 </pre>


<p>また、例えば実行に 30 秒以上かかっているクエリを参照する場合は以下のような感じになる。</p>

<pre class="terminal"> select
    pid
    , client_addr
    , query_start
    , query
from
    pg_stat_activity
where
    state = 'active'
    and
        query_start &lt; ( current_timestamp - interval '30' second )
    and
        pid &lt;&gt; pg_backend_pid()
order by
    query_start;
 </pre>


<p>ロックの状態を確認する。ロック状態にあるものは pg_locks ビューにロックの情報が格納されているので、ここに格納されている pid と pg_stat_activity ビューにある pid を元に内部結合して、ロックされているプロセスの情報を参照する。</p>

<pre class="terminal"> select
    locktype
    , pg_locks.pid
    , mode
    , relation::regclass
    , usename
    , application_name
    , client_addr
    , query_start
    , state_change
    , granted
    , state
    , query
from
    pg_locks
inner join
    pg_stat_activity
    on
        pg_locks.pid = pg_stat_activity.pid
where
    pg_locks.pid &lt;&gt; pg_backend_pid();
 </pre>


<h4>各テーブルのサイズ等の情報を確認する</h4>


<p>各テーブルの使用率を取得する。</p>

<pre class="terminal"> SELECT
    pgn.nspname, relname, pg_size_pretty(relpages::bigint * 8 * 1024) AS size,
CASE 
    WHEN relkind = 't' THEN (SELECT pgd.relname FROM pg_class pgd WHERE pgd.reltoastrelid = pg.oid)
    WHEN nspname = 'pg_toast' AND relkind = 'i' THEN (SELECT pgt.relname FROM pg_class pgt WHERE SUBSTRING(pgt.relname FROM 10) = REPLACE(SUBSTRING(pg.relname FROM 10), '_index', ''))
    ELSE (SELECT pgc.relname FROM pg_class pgc WHERE pg.reltoastrelid = pgc.oid) END::varchar AS refrelname,
        CASE WHEN nspname = 'pg_toast' AND relkind = 'i'
            THEN (SELECT pgts.relname FROM pg_class pgts WHERE pgts.reltoastrelid = (SELECT pgt.oid FROM pg_class pgt WHERE SUBSTRING(pgt.relname FROM 10) = REPLACE(SUBSTRING(pg.relname FROM 10), '_index', '')))
END AS relidxrefrelname, relfilenode, relkind, reltuples::bigint, relpages 
FROM pg_class pg, pg_namespace pgn
WHERE pgn.nspname='public' AND pg.relnamespace = pgn.oid AND pgn.nspname NOT IN ('information_schema', 'pg_catalog') ORDER BY relpages DESC; 
 </pre>


<h4>VACUUM の情報を確認する</h4>


<p>以下で取得可能。</p>

<pre class="terminal"> select
    relname
    , n_live_tup
    , n_dead_tup
    , round(n_dead_tup * 100 / nullif(n_live_tup + n_dead_tup, 0), 2) as dead_ratio
    , last_autovacuum
from
    pg_stat_user_tables;
 </pre>


<h4>実行計画を取得する</h4>


<p>実行計画を取得する。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>を貼って実行するようにして、影響を少なくする。</p>

<pre class="terminal"> begin;

explain analyze ...;

rollback;
 </pre>




<h4>キャッシュヒット率をみる</h4>


<p>以下で取得可能。</p>

<pre class="terminal"> select
    datname
    , blks_hit * 100.0 / (blks_read + blks_hit) AS cache_hit
from
    pg_stat_database
where
    datname = 'db_name_hogehoge';
 </pre>




<h4>実行中のクエリを停止する</h4>


<p>以下で停止したいクエリの pid を取得する。</p>

<pre class="terminal"> select * from pg_stat_activity;
 </pre>


<p>取得した pid に対して以下を実行する。</p>

<pre class="terminal"> SELECT pg_cancel_backend(pid);
 </pre>


<p>上記でも停止しない場合は以下を実行する。</p>

<pre class="terminal"> SELECT pg_terminate_backend(pid);
 </pre>


<h4>参考</h4>


<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.postgresql.jp/document/current/html/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW">28.2. 統計情報コレクタ</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.postgresql.jp/document/current/html/functions-datetime.html">9.9.5. 遅延実行</a></li>
        <li><a href="https://lets.postgresql.jp/documents/technical/statistics/2">稼動統計情報を活用しよう(2) | Let's POSTGRES</a></li>
        <li><a href="https://pgecons-sec-tech.github.io/tech-report/html_wg3_performance/wg3_performance.html">2018年度WG3活動報告書 性能トラブル調査編</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://pgecons-sec-tech.github.io/tech-report/">PostgreSQL エンタープライズ・コンソーシアム  :  活動報告トップページ</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://wiki.postgresql.org/wiki/PostgreSQL_Conference_Europe_Talks_2013">PostgreSQL Conference Europe Talks 2013 - PostgreSQL wiki</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://wiki.postgresql.org/images/a/ab/Pganalyze_Lightning_talk.pdf">pganalyze - Monitoring for Casual DBAs</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://blog.shibayu36.org/entry/2018/04/09/193000">PostgreSQLでSQLチューニングや障害状況調査に使ったクエリ達まとめ - $shibayu36-&gt;blog;
</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://qiita.com/awakia/items/99c3d114aa16099e825d">PostgreSQLで各テーブルの総サイズと平均サイズを知る - Qiita
</a></li>
</ul>

</body>
