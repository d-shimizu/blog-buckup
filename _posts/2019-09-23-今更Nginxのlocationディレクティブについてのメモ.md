---
layout: post
Categories:  ["tech"]
Description:  " Nginxのlocationディレクティブを頻繁にいじることがなく、いざ設定しようとするとゴタゴタしてしまうので基本的な書き方の一部をメモがてら書いておく。 "
Tags: ["Nginx", "tech"]
date: "2019-09-23T01:43:00+09:00"
title: "今更Nginxのlocationディレクティブについてのメモ"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p>Nginxのlocationディレクティブを頻繁にいじることがなく、いざ設定しようとするとゴタゴタしてしまうので基本的な書き方の一部をメモがてら書いておく。</p>
</body>

<!-- more -->

<body>
<h4>静的コンテンツをキャッシュさせたい</h4>


<p>特定のPATHのものをキャッシュさせたい場合、例えば<code>/static/</code>に静的コンテンツがあり、それらをキャッシュさせたい場合は下記のようになる。</p>

<pre class="terminal"> server {
    :
    location /static/ {
        root           /path/to/public;
        autoindex      on;
        expires        1d;
        add_header Cache-Control "private max-age=86400";
    }
    :
}
 </pre>




<h4>静的コンテンツをキャッシュさせたい(<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C0%B5%B5%AC%C9%BD%B8%BD">正規表現</a>)</h4>


<p><code>/static/</code>の中の画像や<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>、JSなどを<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C0%B5%B5%AC%C9%BD%B8%BD">正規表現</a>で指定してキャッシュさせたい場合は下記のようになる。</p>

<p><code>~</code>で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C0%B5%B5%AC%C9%BD%B8%BD">正規表現</a>の始まりを表し、<code>^</code>で行頭を、<code>()</code>の中が<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C0%B5%B5%AC%C9%BD%B8%BD">正規表現</a>を指す。最初の<code>.*</code>で任意の文字の繰り返しを表し、<code>.(html?|jpe?g|gif|pdf|png|css|js|ico|swf|inc)</code>で各ファイルの拡張子を表す。最後の <code>$</code> は行の終わりを意味する。</p>

<pre class="terminal"> server {
    :
    location ~ ^/static/(.*\.(html?|jpe?g|gif|pdf|png|css|js|ico|swf|inc))$  {
        root           /path/to/public;
        autoindex on;
        expires 30d;
        add_header Cache-Control "private max-age=86400";
    }
    :
}
 </pre>


<p>etagを使う場合、etagの値の生成には一般的に最終更新日時のハッシュが用いられるため、複数台のサーバがある場合は各サーバの画像ファイルの更新日時も統一しておく必要がある。</p>

<h4>ファイルの存在をチェックする(try_files)</h4>


<p><code>try_files</code>自体は<code>try_files file_path ... uri;</code>のような構文で記載して、順番にファイルの存在をチェックして、最初に見つかったファイルでリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを処理する。</p>

<p>例えば下記のような記載で、<code>example.com/images/hogehoge.png</code>というリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トがあった場合に、<code>$uri</code>は<code>/images/hogehoge.png</code>となってファイルの存在チェックをし、それが存在しなければ<code>/images/default.gif</code>にリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トされる。
<code>$uri/</code>のように最後に<code>/</code>をつけると、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リと判定してくれる。</p>

<pre class="terminal"> server {
    :
    server_name   example.com;
    root          /path/to/public;

    location /images/ {
        try_files $uri /images/default.gif;
    }
    :
}
 </pre>




<h4>プロキシさせる(proxy_pass)</h4>


<p><code>/hogehoge</code>へのリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを特定のサーバで処理させたい場合は下記のようにする。</p>

<pre class="terminal"> server {
    root /var/www/example.com;
    location /hogehoge {
        proxy_pass  http://hogehoge.com;
    }
}
 </pre>


<p>複数台のサーバに分散させたい場合は下記のように<code>upstream</code>ディレクティブを使って分散対象のサーバを定義する。</p>

<pre class="terminal"> upstream hogehoge {
    server hogehoge01.com;
    server hogehoge02.com;
}

server {
    root /var/www/example.com;
    location / {
        proxy_pass  http://hogehoge;
    }
}
 </pre>




<h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/fastCGI">fastCGI</a>へリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを投げる</h4>


<p>例えば<a class="keyword" href="http://d.hatena.ne.jp/keyword/php">php</a>-fpm等の<a class="keyword" href="http://d.hatena.ne.jp/keyword/FastCGI">FastCGI</a>のプロセスにリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを投げたい場合は下記のようにする。下記の例では<a class="keyword" href="http://d.hatena.ne.jp/keyword/FastCGI">FastCGI</a>プロセスは<code>8080</code>で応答しているモノとする。</p>

<pre class="terminal"> 
server {
    root /var/www/example.com;
    location / {
        fastcgi_pass  hogehoge.com:8080;
    }
}
 </pre>


<p>複数台のサーバの<a class="keyword" href="http://d.hatena.ne.jp/keyword/FastCGI">FastCGI</a>に分散させたい場合は<code>proxy_pass</code>の時とほぼ同様で<code>upstream</code>ディレクティブを使って分散対象のサーバを定義する。</p>

<pre class="terminal"> upstream hogehoge {
    server hogehoge01.com:8080;
    server hogehoge02.com:8080;
}

server {
    root /var/www/example.com;
    location / {
        proxy_pass  hogehoge;
    }
}
 </pre>




<h4>まとめ</h4>


<p>Nginxのlocationディレクティブの基本的な使い方の一部を書いた。</p>

<h4>参考</h4>




<ul>
  <li>
<a target="_brank" rel="noopener noreferrer" href="https://nginx.org/en/docs/http/ngx_http_core_module.html#var_uri">Module ngx_http_core_module</a><a></a>
</li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://nginx.org/en/docs/http/ngx_http_headers_module.html#expires">Module ngx_http_headers_module</a></li>
  <li>
<a target="_brank" rel="noopener noreferrer" href="https://mogile.web.fc2.com/nginx/admin-guide/serving-static-content.html">静的コンテンツの提供 | NGINX 日本語訳</a><a></a>
</li>
 <li>
<a target="_brank" rel="noopener noreferrer" href="https://mogile.web.fc2.com/nginx_wiki/nginx_wiki201510/start/topics/tutorials/config_pitfalls.html">落とし穴とよくある間違い | NGINX 日本語訳</a><a></a>
</li>
  <li>
<a target="_brank" rel="noopener noreferrer" href="https://heartbeats.jp/hbblog/2012/04/nginx05.html">nginx連載5回目: nginxの設定、その3 - locationディレクティブ - インフラエンジニアway - Powered by HEARTBEATS</a><a></a>
</li>
  <li>
<a target="_brank" rel="noopener noreferrer" href="https://stackoverflow.com/questions/48708361/nginx-request-uri-vs-uri/48709976">NGINX $request_uri vs $uri - Stack Overflow</a><a></a>
</li>
</ul>

</body>
