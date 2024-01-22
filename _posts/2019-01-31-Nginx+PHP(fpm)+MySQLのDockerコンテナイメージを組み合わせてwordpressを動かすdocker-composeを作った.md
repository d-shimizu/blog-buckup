---
layout: post
Categories:  ["tech"]
Description:  " Dockerの勉強も兼ねて、WordpressをDockerを使って動かしてみようと思考していて、Wordpressのコンテナイメージを用いれば手っ取り早いけど、SSL化しようとするとNginxなどでリバースプロキシを使わないといけなそう"
Tags: ["Docker", "MySQL", "Nginx", "php", "tech", "WordPress"]
date: "2019-01-31T00:36:00+09:00"
title: "Nginx+PHP(fpm)+MySQLのDockerコンテナイメージを組み合わせてwordpressを動かすdocker-composeを作った"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p>Dockerの勉強も兼ねて、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Wordpress">Wordpress</a>をDockerを使って動かしてみようと思考していて、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Wordpress">Wordpress</a>のコンテナイメージを用いれば手っ取り早いけど、<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSL">SSL</a>化しようとするとNginxなどでリバースプロキシを使わないといけなそうで、その場合はX-Forwarded-Protoヘッダを用いて...などとなると結構面倒そうだった。</p>

<p>ので、Nginxと<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>(<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>-FPM)と<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>のDockerイメージに加えて、Let's Encryptの証明書を追加できるようにしたdocker-composeを作ってみた。</p>
</body>

<!-- more -->

<body>
<ul>
    <li><a href="https://github.com/d-shimizu/docker-compose-wordpress" target="_brank" rel="noopener noreferrer">GitHub - d-shimizu/docker-compose-wordpress
</a></li>
</ul>


<p>Nginxと<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>(<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>-FPM)と<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>の3つのコンテナに分けたのは、1コンテナ1プロセスに則ってみただけ。これが良いのかはわからない。Nginxと<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>-FPMは同居しても良かったかもしれない。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Wordpress">Wordpress</a>は別途ホストOS側で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%A7%A5%EB%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">シェルスクリプト</a>を使ってダウンロードして、<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>(FPM)コンテナにボリュームマウントし、その領域をNginxコンテナにも共有するようにしている。</p>

<p>Nginxコンテナは、初回起動時は自己証明書を作成してLet's Encryptの証明書を配置する<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リへコピーしてボリュームマウントした状態で起動させておき、コンテナ起動後にホストOS側で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%A7%A5%EB%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">シェルスクリプト</a>を使ってLet's Encryptの証明書を取得して、差し替えるようにしている。
ホストOS側でLet's Encryptの証明書の更新してNignxコンテナを再起動するcronを登録しておく必要がある。</p>

<p>Nginxの設定ファイルserver_nameやLet's Encryptの証明書取得<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>内に記載されている<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>のところをうまく変数化したいがまだうまくいっていないので修正したいところ。</p>

<h3>まとめ</h3>


<p>Nginx+<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>(fpm)+<a class="keyword" href="http://d.hatena.ne.jp/keyword/MySQL">MySQL</a>のDockerコンテナイメージに、Let's Encryptの証明書を利用できるようにしたdocker-composeを作ってみた。
このやり方が良いのかはわからないけど、いろいろと機能を使いながらやってみたのでDockerの勉強にはなった。</p>
</body>
