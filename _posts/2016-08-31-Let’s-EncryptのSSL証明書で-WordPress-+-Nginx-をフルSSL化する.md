---
layout: post
Categories:  ["tech"]
Description:  " はじめに  無料で利用可能な Let’s Encrypt の証明書を使って WordPress + Nginx のサイトをフルSSL化してみます。 "
Tags: ["Let’s Encrypt", "SSL", "tech", "Ubuntu"]
date: "2016-08-31T02:58:00+09:00"
title: "Let’s EncryptのSSL証明書で WordPress + Nginx をフルSSL化する"
author: "dshimizu"
archive: ["2016"]
draft: "true"
---

<body>
<h3>はじめに</h3>


<p>無料で利用可能な <a class="keyword" href="http://d.hatena.ne.jp/keyword/Let%A1%C7s%20Encrypt">Let’s Encrypt</a> の証明書を使って <a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a> + Nginx のサイトをフル<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSL">SSL</a>化してみます。</p>
</body>

<!-- more -->

<body>
<h3><a class="keyword" href="http://d.hatena.ne.jp/keyword/Let%A1%C7s%20Encrypt">Let’s Encrypt</a></h3>


<h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Let%A1%C7s%20Encrypt">Let’s Encrypt</a> とは</h4>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Let%A1%C7s%20Encrypt">Let’s Encrypt</a> は無料で使える<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSL">SSL</a>/<a class="keyword" href="http://d.hatena.ne.jp/keyword/TLS">TLS</a>証明書を発行する<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C7%A7%BE%DA%B6%C9">認証局</a>（CA）です。
企業や<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C8%F3%B1%C4%CD%F8%C3%C4%C2%CE">非営利団体</a>などの様々な組織のサポートを受けながら、　Internet Security Research Group (略称：ISRG)　という団体によって運営されています。
2016年4月から正式サービスとして開始されています。</p>

<h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Let%A1%C7s%20Encrypt">Let’s Encrypt</a> の仕組み</h4>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Let%A1%C7s%20Encrypt">Let’s Encrypt</a> の認証・証明書取得のクライアントプログラム（<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%A7%A5%EB%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">シェルスクリプト</a>）があるので、それを実行するのみです。</p>

<p>初回実行時は以下の様な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>認証が実施されます。</p>

<ol>
    <li>証明書をインストールしたいWebサーバから、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>名（<a class="keyword" href="http://d.hatena.ne.jp/keyword/FQDN">FQDN</a>）の情報をLet's Encryptの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C7%A7%BE%DA%B6%C9">認証局</a>に伝え、認証してもらう。</li>
    <li>Let's Encrypt 側からワンタイム<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ン(認証用の一時的な番号)と、認証用ファイルとそれを配置すべきPATH情報(${ドキュメントルート}/.well-known)がWebサーバに送られ、クライアントプログラムによってファイルが配置される。</li>
    <li>Webサーバ側はクライアントプログラムが生成した<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C8%EB%CC%A9%B8%B0">秘密鍵</a>で、ワンタイム<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ンへ署名し、公開鍵とともにLet's Encryptのサーバへ送信する。</li>
    <li>Let's Encrypt側から「http://＜<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>名＞/.well-known/<a class="keyword" href="http://d.hatena.ne.jp/keyword/acme">acme</a>-challenge/認証用ファイル名」が参照され、認証用ファイルが取得される。</li>
    <li>Let's Encrypt側でワンタイム<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ンに対する署名とウェブサーバからダウンロードした認証用のファイルの妥当性を検証され、認証に成功すると、公開鍵によって識別されたクライアントプログラムに対して、以後の証明書の発行や更新、失効といった作業を自動的に実施できるようになる。</li>
</ol>


<h3>Let's Encrypt証明書の取得</h3>


<h4>動作環境</h4>


<p>今回は <a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> 16.04 LTS 上で動作している Nginx + <a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a> 環境に対して証明書の適用を実施します。</p>

<table>
<tbody>
<tr>
<th>OS</th>
<td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> 16.04 LTS(4.4.0-21-generic)</td>
</tr>
<tr>
<th>Webサーバ</th>
<td>nginx/1.10.0</td>
</tr>
<tr>
<th>その他</th>
<td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a>, php7.0-fpm</td>
</tr>
</tbody>
</table>


<p>Let's Encryptの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C7%A7%BE%DA%B6%C9">認証局</a>との通信のため、外部へhttp,<a class="keyword" href="http://d.hatena.ne.jp/keyword/https">https</a>で通信できる必要があります。</p>

<h4>Let's Encrypt のクライアントソフトウェア <a class="keyword" href="http://d.hatena.ne.jp/keyword/Certbot">Certbot</a> の取得</h4>


<p>Let's Encrypt は、クライアントソフトウェア <a class="keyword" href="http://d.hatena.ne.jp/keyword/Certbot">Certbot</a> を用いて、<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSL">SSL</a>/<a class="keyword" href="http://d.hatena.ne.jp/keyword/TLS">TLS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B5%A1%BC%A5%D0%BE%DA%CC%C0%BD%F1">サーバ証明書</a>の取得・更新作業を自動化できる仕組みになっています。
まずはこれを取得します。</p>

<pre class="terminal"> $ sudo curl https://dl.eff.org/certbot-auto -o /usr/local/bin/certbot-auto
 </pre>


<p>実行権限を付与します。</p>

<pre class="terminal"> $ sudo chmod 700 /usr/local/bin/certbot-auto
 </pre>


<pre class="terminal"> $ certbot-auto certonly \             # 証明書を取得
  --webroot \                         # 現在稼働中のWebサーバを使って証明書を取得
  --webroot-path ${ドキュメントルート} \    # ドキュメント・ルートのパス
  -d ドメイン名 \                        # 認証するドメイン名
  --email メールアドレス          　　　　　　　　　　   # メールアドレス登録（証明書期限切れの通知用）
 </pre>


<p>不足パッケージがある場合、ここでパッケージのインストールが始まります。</p>

<p>その後以下の様な画面が出ます。
<code>Agree</code> を選択します。</p>

<pre class="terminal"> ┌──────────────────────────────────────────────────────────────────────┐
│ Please read the Terms of Service at                                  │
│ https://letsencrypt.org/documents/LE-SA-v1.1.1-August-1-2016.pdf.    │
│ You must agree in order to register with the ACME server at          │
│ https://acme-v01.api.letsencrypt.org/directory                       │
│                                                                      │
│                                                                      │
│                                                                      │
│                                                                      │
│                                                                      │
│                                                                      │
│                                                                      │
│                                                                      │
│                                                                      │
│                                                                      │
│                                                                      │
│                                                                      │
├──────────────────────────────────────────────────────────────────────┤
│                     &lt;Agree &gt;           &lt;Cancel&gt;                      │
└──────────────────────────────────────────────────────────────────────┘
 </pre>


<p>自動的に <code>certbot-auto</code> が <a class="keyword" href="http://d.hatena.ne.jp/keyword/Let%A1%C7s%20Encrypt">Let’s Encrypt</a> の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C7%A7%BE%DA%B6%C9">認証局</a>と通信を行い、証明書を自動生成します。
以下の画面が出れば終了です。</p>

<pre class="terminal"> IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/blog.dshimizu.jp/fullchain.pem. Your cert
   will expire on 2016-12-02. To obtain a new or tweaked version of
   this certificate in the future, simply run certbot-auto again. To
   non-interactively renew *all* of your certificates, run
   "certbot-auto renew"
 - If you lose your account credentials, you can recover through
   e-mails sent to shimizu.daisuke.sd@gmail.com.
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
 </pre>


<p>Webroot を指定した場合、 ${ドキュメントルート}/.well-known/<a class="keyword" href="http://d.hatena.ne.jp/keyword/acme">acme</a>-challenge に一時ファイル（ワンタイム<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ン）を作成し、Let's Encrypt の認証サーバから HTTP リク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トによって取得することで認証が行われます。
その際、Webサーバには下記のような<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A5%AF%A5%BB%A5%B9%A5%ED%A5%B0">アクセスログ</a>が記録されます。</p>

<pre class="terminal"> 66.133.109.36 - - [31/Aug/2016:hh:mm:ss +0900] "GET /.well-known/acme-challenge/XeJCJUeYYC9SZWi5tvorvnhwhXf8iKB4FRO6LP_vEhY HTTP/1.1" 200 87 "-" "Mozilla/5.0 (compatible; Let's Encrypt validation server; +https://www.letsencrypt.org)"
 </pre>


<p>処理が終わると、 <code>/etc/letsencript</code> 以下の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リに証明書ファイルが自動生成されています。</p>

<pre class="terminal"> $ sudo ls -l /etc/letsencrypt/live/{ドメイン名}
total 2
lrwxrwxrwx 1 root root 40  8月 31 hh:mm cert.pem -&gt; ../../archive/{ドメイン名}/cert1.pem
lrwxrwxrwx 1 root root 41  8月 31 hh:mm chain.pem -&gt; ../../archive/{ドメイン名}/chain1.pem
lrwxrwxrwx 1 root root 45  8月 31 hh:mm fullchain.pem -&gt; ../../archive/{ドメイン名}/fullchain1.pem
lrwxrwxrwx 1 root root 43  8月 31 hh:mm privkey.pem -&gt; ../../archive/{ドメイン名}/privkey1.pem
 </pre>


<h4>Nginx の設定</h4>


<p>Nginxの設定ファイルには <code>ssl_certificate</code>,<code>ssl_certificate_key</code>に letsencrypt の証明書設置先の <code>Path</code> を指定します。
他は 80ポートへのリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トは <a class="keyword" href="http://d.hatena.ne.jp/keyword/https">https</a> へ転送し、 443 ポートで <a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a> の　<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a> を処理できるようにします。</p>

<p>ざっくり以下のような記述があれば良いと思います。
※管理画面へのアクセス制御などは考慮していないのでご注意ください。</p>

<pre class="terminal"> server {
    listen 80;
    index index.html index.htm index.php;
    server_name {ドメイン名};

    root ${ドキュメントルート};
    index index.html;

    access_log ${Nginxログディレクトリ}/access.log;
    error_log ${Nginxログディレクトリ}/error.log;

    location / {
        return 301 https://blog.dshimizu.jp$request_uri;
    }
}

server {
    listen 443;
    ssl on;

    index index.html index.htm index.php;
    server_name {ドメイン名};

    ssl_certificate         /etc/letsencrypt/live/{ドメイン名}/cert.pem;
    ssl_certificate_key     /etc/letsencrypt/live/{ドメイン名}/privkey.pem;

    root ${ドキュメントルート};
    index index.html;

    access_log ${Nginxログディレクトリ}/ssl_access.log;
    error_log ${Nginxログディレクトリ}/ssl_error.log;

    client_max_body_size 20M;

    location / {
        try_files $uri $uri/ @wordpress;
    }

    location ~ \.php$ {
        try_files $uri @wordpress;
        fastcgi_split_path_info ^(.+\.php)(.*)$;
        fastcgi_pass php-fpm-socket;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location @wordpress {
        fastcgi_index index.php;
        fastcgi_pass php-fpm-socket;
        rewrite ^ /index.php last;
        include       fastcgi_params;
    }

}
 </pre>


<p>設定を反映させるため、Nginxを reload します。</p>

<pre class="terminal"> $ sudo systemctl reload nginx
 </pre>


<h4>確認</h4>


<p>ブラウザから「<a href="https://&lt;">https://&lt;</a>ホスト名&gt;/」を開きし、正常に <a class="keyword" href="http://d.hatena.ne.jp/keyword/HTTPS">HTTPS</a> で通信できることを確認します。</p>

<h3>おわりに</h3>


<p>今後もますますセキュリティには気を配る必要がありますが、インターネット上の通信をセキュアにするには <a class="keyword" href="http://d.hatena.ne.jp/keyword/SSL">SSL</a>/<a class="keyword" href="http://d.hatena.ne.jp/keyword/TLS">TLS</a> 通信が必要不可欠です。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Let%A1%C7s%20Encrypt">Let’s Encrypt</a> であれば、オンライン上で無料かつ簡単に証明書を作成できます。
ぜひ一度利用されてみると良いかもしれません。</p>

<h3>参考</h3>


<ul>
    <li><a href="https://letsencrypt.org/" target="_blank" rel="noopener noreferrer">Let's Encrypt</a></li>
    <li><a href="https://letsencrypt.jp/" target="_blank" rel="noopener noreferrer">Let's Encrypt 総合ポータル(非公式解説サイト)
</a></li>
    <li><a href="http://www.atmarkit.co.jp/ait/articles/1606/02/news049.html" target="_blank" rel="noopener noreferrer">Tech Basics／Keyword：無償かつ自動でSSL（TLS）証明書を発行できる「Let's Encrypt」とは？ - ＠IT</a></li>
<a href="http://www.atmarkit.co.jp/ait/articles/1606/02/news049.html" target="_blank" rel="noopener noreferrer">
</a>
    <li>
<a href="http://www.atmarkit.co.jp/ait/articles/1606/02/news049.html" target="_blank" rel="noopener noreferrer"></a><a href="http://knowledge.sakura.ad.jp/knowledge/5573/" target="_blank" rel="noopener noreferrer">Let’s EncryptのSSL証明書で、安全なウェブサイトを公開 - さくらのナレッジ</a>
</li>
<a href="http://knowledge.sakura.ad.jp/knowledge/5573/" target="_blank" rel="noopener noreferrer">
</a>
</ul>

</body>
