---
layout: post
Categories:  ["tech"]
Description:  " 久しぶりに WordPress に大きな脆弱性が公開されました。WordPress 4.7 と 4.7.1 の REST API に認証を回避してコンテンツを書き換えられる脆弱性が存在します。攻撃は容易で、攻撃が成立するとコンテンツの書き"
Tags: ["Nginx", "tech", "WordPress"]
date: "2017-02-11T23:15:00+09:00"
title: "Nginx で WordPress REST API へのアクセス制限"
author: "dshimizu"
archive: ["2017"]
draft: "true"
---

<body>
<p>久しぶりに <a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a> に大きな<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C0%C8%BC%E5%C0%AD">脆弱性</a>が公開されました。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a> 4.7 と 4.7.1 の <a class="keyword" href="http://d.hatena.ne.jp/keyword/REST%20API">REST API</a> に認証を回避してコンテンツを書き換えられる<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C0%C8%BC%E5%C0%AD">脆弱性</a>が存在します。攻撃は容易で、攻撃が成立するとコンテンツの書き換えが実行可能なため影響は大きいです。</p>
</body>

<!-- more -->

<body>
<ul>
    <li><a href="https://blog.sucuri.net/2017/02/content-injection-vulnerability-wordpress-rest-api.html" target="_blank" rel="noopener noreferrer">Content Injection Vulnerability in WordPress 4.7 and 4.7.1</a></li>
    <li><a href="https://make.wordpress.org/core/2017/02/01/disclosure-of-additional-security-fix-in-wordpress-4-7-2/" target="_blank" rel="noopener noreferrer">Make WordPress Core</a></li>
    <li><a href="https://www.ipa.go.jp/security/ciadr/vul/20170206-wordpress.html" target="_blank" rel="noopener noreferrer">WordPress の脆弱性対策について：IPA 独立行政法人 情報処理推進機構</a></li>
</ul>


<p>技術的な検証と考察については以下の記事を参考にしてください。</p>

<ul>
    <li><a href="http://blog.tokumaru.org/2017/02/wordpress-4.7.1-Privilege-Escalation.html" target="_blank" rel="noopener noreferrer">WordPress 4.7.1 の権限昇格脆弱性について検証した | 徳丸浩の日記</a></li>
</ul>


<h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/REST%20API">REST API</a>へのアクセス制限</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/REST%20API">REST API</a> は <a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a> 4.6 までは<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>で利用可能でしたが、　<a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a> 4.7 から標準機能として搭載されました。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a>をアップデートすると今回の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C0%C8%BC%E5%C0%AD">脆弱性</a>を回避できますが、それでも現状<a class="keyword" href="http://d.hatena.ne.jp/keyword/REST%20API">REST API</a>へのアクセス自体はどこからでもできてしまう状態で、<a class="keyword" href="http://d.hatena.ne.jp/keyword/REST%20API">REST API</a>を無効化するにも<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>が必要なので、Nginx側でアクセス制限してしまいます。</p>

<p>以下は Nginx で /wp-<a class="keyword" href="http://d.hatena.ne.jp/keyword/json">json</a> へのアクセスを IP で制限する設定例です。</p>

<pre class="terminal">     location ~ wp-json {
        # REST APIへアクセスするIPを許可
        allow xxx.xxx.xxx.xxx;
        allow 127.0.0.1;
        # 上記以外全部拒否
        deny all;
        try_files $uri @wordpress;
        fastcgi_split_path_info ^(.+\.php)(.*)$;
        fastcgi_pass php-fpm-socket;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
        index index.php;
    }

    location @wordpress {
        fastcgi_index index.php;
        fastcgi_pass php-fpm-socket;
        rewrite ^ /index.php last;
        include       fastcgi_params;
    }
 </pre>


<h3>まとめ</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a> 4.7 では <a class="keyword" href="http://d.hatena.ne.jp/keyword/REST%20API">REST API</a> の機能が標準搭載されてます。<a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a> 4.7.2 でもデフォルトだと参照自体はどこからでも可能です。
特に利用することがない場合は<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>で無効にするなり、アクセス制限するなりしておいた方が良いと思います。</p>
</body>
