---
title: "Nginx での PHP-FPM 用の設定方法についておさらいした"
date: 2023-11-13
slug: "Nginx での PHP-FPM 用の設定方法についておさらいした"
category: blog
tags: [Nginx,php-fpm]
---
<h2 id="はじめに">はじめに</h2>

<p>Nginx で <a class="keyword" href="https://d.hatena.ne.jp/keyword/PHP">PHP</a>-FPM を設定するとき、なんとなく触っていたのですが、改めてどういうものか理解していこうと思い、基本的な設定を見直しながら復習がてら記載しました。</p>

<h2 id="PHP-FPM-って"><a class="keyword" href="https://d.hatena.ne.jp/keyword/PHP">PHP</a>-FPM って？</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/PHP">PHP</a> における <a class="keyword" href="https://d.hatena.ne.jp/keyword/FastCGI">FastCGI</a> 実装とのことです。</p>

<ul>
<li><a href="https://www.php.net/manual/ja/install.fpm.php">PHP: FastCGI Process Manager (FPM) - Manual</a></li>
</ul>


<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/FastCGI">FastCGI</a> は、Open Marketという会社によって1990年代中頃に開発されたもののようで、仕様は公開されています。元のページはなくなっているようで、<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AB%A5%A4%A5%D6">アーカイブ</a>状態で残っているようでした。</p>

<ul>
<li><a href="https://fastcgi-archives.github.io/FastCGI_Specification.html">FastCGI Specification</a></li>
</ul>


<p>「1つのリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト毎に1つのプロセス」という形では、実装は簡単であるものの、OSによるプロセス起動停止に伴ってのオーバーヘッドが無視できなくなるであろう問題を解決するために、永続的なプロセスを使用して一連のリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを処理する形としているようです。Webサーバープロセスではなく、<a class="keyword" href="https://d.hatena.ne.jp/keyword/FastCGI">FastCGI</a>のサーバープロセスが別途で起動して処理するようです。</p>

<p>Nginx と組み合わせて利用する場合の構成イメージは下記のような感じかと思います。Nginx と <a class="keyword" href="https://d.hatena.ne.jp/keyword/PHP">PHP</a>-FPM は同一サーバー上でも別々のサーバー上でも実行できます。
Nginxがリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを受け付けたら、必要な情報と共に <a class="keyword" href="https://d.hatena.ne.jp/keyword/php">php</a>-fpm プロセスへリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを流して、<a class="keyword" href="https://d.hatena.ne.jp/keyword/php">php</a>-fpm が <a class="keyword" href="https://d.hatena.ne.jp/keyword/php">php</a> プログラムを実行して必要な情報を Nginx へ返し、Nginxがクライアントへレスポンスを返す、といった流れかと思います。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20231104/20231104232106.png" width="217" height="301" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<h2 id="Nginx--PHP-FPM">Nginx + <a class="keyword" href="https://d.hatena.ne.jp/keyword/PHP">PHP</a>-FPM</h2>

<p>Nginx と <a class="keyword" href="https://d.hatena.ne.jp/keyword/PHP">PHP</a>-FPM を連携する設定を見ていきます。</p>

<ul>
<li><a href="https://www.php.net/manual/ja/install.unix.nginx.php">PHP: Nginx 1.4.x (Unix &#x30B7;&#x30B9;&#x30C6;&#x30E0;&#x7528;) - Manual</a></li>
</ul>


<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/PHP">PHP</a> の公式ページだと、デフォルトは以下のようなものであると記載されています。</p>

<pre class="code lang-nginx" data-lang="nginx" data-unlink><span class="synStatement">location</span> / {
    <span class="synType">root</span>   html;
    <span class="synIdentifier">index</span>  index.php index.html index.htm;
}

<span class="synStatement">location</span> ~* \.php$ {
    <span class="synIdentifier">fastcgi_index</span>   index.php;
    <span class="synType">fastcgi_pass</span>    127.0.0.1:9000;
    <span class="synType">include</span>         fastcgi_params;
    <span class="synIdentifier">fastcgi_param</span>   SCRIPT_FILENAME    <span class="synIdentifier">$document_root$fastcgi_script_name</span>;
    <span class="synIdentifier">fastcgi_param</span>   SCRIPT_NAME        <span class="synIdentifier">$fastcgi_script_name</span>;
}
</pre>


<p>最初の location ディレクティブでは、<code>/</code> の場合に、インデックスファイルが <code>index.php</code> , <code>index.html</code>, <code>index.htm</code> の順に利用できることを示してます。<code>/</code> でリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トされた場合、内部的に <code>/index.php</code> として処理される形になります。<code>index.php</code> がなければ <code>index.html</code>を、それがなければ <code>index.htm</code> を利用する形になります。 すべてのパスは <code>/path/to/xxx.html</code> といった<code>/</code> で始まるため、他のlocationディレクティブのパスの条件に一致したもの以外はこのlocationディレクティブが適応されます。</p>

<p>次の location ディレクティブでは、リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トの内容が <code>.php</code> にマッチする場合の処理が記述されています。</p>

<p><code>fastcgi_index</code> は <code>$fastcgi_script_name</code> 変数の値の中のスラッシュで終わる<a class="keyword" href="https://d.hatena.ne.jp/keyword/URI">URI</a>の後に追加されるファイル名を設定します。例えば <code>/</code> のリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トの場合は <code>/index.php</code> に、<code>/info/</code> の場合は <code>/info/index.php</code> になります。</p>

<p><code>fastcgi_pass</code> は<a class="keyword" href="https://d.hatena.ne.jp/keyword/FastCGI">FastCGI</a>サーバのアドレス、または<a class="keyword" href="https://d.hatena.ne.jp/keyword/Unix">Unix</a><a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>ソケットを指定します。Nginxと<a class="keyword" href="https://d.hatena.ne.jp/keyword/PHP">PHP</a>-FPMが同一サーバー上で実行されている場合は<a class="keyword" href="https://d.hatena.ne.jp/keyword/Unix">Unix</a><a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>ソケットで通信した方が処理は速いと思います。Nginxと<a class="keyword" href="https://d.hatena.ne.jp/keyword/PHP">PHP</a>-FPMが別サーバーである場合は<a class="keyword" href="https://d.hatena.ne.jp/keyword/TCP">TCP</a>を利用することになると思います。<a class="keyword" href="https://d.hatena.ne.jp/keyword/Unix">Unix</a><a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>ソケットを利用する場合は <a class="keyword" href="https://d.hatena.ne.jp/keyword/PHP">PHP</a>-FPM が<a class="keyword" href="https://d.hatena.ne.jp/keyword/Unix">Unix</a><a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>ソケットを使うように設定する必要があります。</p>

<p><code>include fastcgi_params</code> は、Nginx の <code>fastcgi_params</code> ファイル内にあるパラメーターを読み込みます。 <a class="keyword" href="https://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> だと <code>/etc/nginx/fastcgi_params</code> にあると思います。ここで読み込まれたパラメーターが <a class="keyword" href="https://d.hatena.ne.jp/keyword/php">php</a>-fpm へ渡されます。これらの渡された値は <a class="keyword" href="https://d.hatena.ne.jp/keyword/PHP">PHP</a> の <code>$_SERVER</code> 変数で参照できます。</p>

<p><code>fastcgi_param</code> は <code>include fastcgi_params</code> で読み込んだ値に追加・上書きでパラメーターを設定します。
<code>fastcgi_param   SCRIPT_FILENAME    $document_root$fastcgi_script_name;</code> では、<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>ファイルの<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リフルパスが設定されます。具体的には、 <code>$document_root</code> は、Nginx の server ブロック内に設定されている root ディレクティブの値になります。<code>$fastcgi_script_name</code> では、リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト<a class="keyword" href="https://d.hatena.ne.jp/keyword/URI">URI</a>、または、もし<a class="keyword" href="https://d.hatena.ne.jp/keyword/URI">URI</a>がスラッシュで終わる場合は <code>fastcgi_index</code> ディレクティブで設定されたindexファイルを追加したリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト<a class="keyword" href="https://d.hatena.ne.jp/keyword/URI">URI</a> になります。例えば、リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トが<code>https://example.com/info/test.php</code> の場合は <code>$fastcgi_script_name</code> に <code>/info/test/php</code> が、<code>https://example.com/</code> の場合は <code>fastcgi_index</code> の値である <code>index.php</code> 付与されて <code>/index.php</code> が設定されます。
<code>fastcgi_param   SCRIPT_NAME        $fastcgi_script_name;</code> はリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トファイルのパスを含めた値が設定されます。</p>

<h2 id="まとめ">まとめ</h2>

<p>Nginx で <a class="keyword" href="https://d.hatena.ne.jp/keyword/PHP">PHP</a>-FPM を設定するとき、なんとなく触っていたので、基本的な設定を見直しながら復習がてら記載しました。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://www.nginx.com/resources/wiki/start/topics/examples/phpfcgi/">PHP FastCGI Example | NGINX</a></li>
<li><a href="https://nginx.org/en/docs/http/ngx_http_fastcgi_module.html#parameters">Module ngx_http_fastcgi_module</a></li>
<li><a href="https://mogile.web.fc2.com/nginx/http/ngx_http_fastcgi_module.html#fastcgi_index">ngx_http_fastcgi_module &#x30E2;&#x30B8;&#x30E5;&#x30FC;&#x30EB; &#x65E5;&#x672C;&#x8A9E;&#x8A33;</a></li>
<li><a href="https://mogile.web.fc2.com/nginx_wiki/start/topics/tutorials/config_pitfalls/">&#x843D;&#x3068;&#x3057;&#x7A74;&#x3068;&#x3088;&#x304F;&#x3042;&#x308B;&#x9593;&#x9055;&#x3044; | NGINX</a></li>
<li><a href="https://mogile.web.fc2.com/nginx_wiki/start/topics/examples/phpfcgi/">PHP FastCGI &#x306E;&#x4F8B; | NGINX</a></li>
<li><a href="https://www.php.net/manual/en/reserved.variables.server.php">PHP: $_SERVER - Manual</a></li>
<li><a href="https://www.php.net/manual/ja/install.unix.nginx.php">PHP: Nginx 1.4.x (Unix &#x30B7;&#x30B9;&#x30C6;&#x30E0;&#x7528;) - Manual</a></li>
<li><a href="https://fastcgi-archives.github.io/">FastCGI.com Archives</a></li>
<li><a href="https://www.ietf.org/rfc/rfc3875">https://www.ietf.org/rfc/rfc3875</a></li>
<li><a href="https://heartbeats.jp/hbblog/2012/04/nginx05.html">nginx&#x9023;&#x8F09;5&#x56DE;&#x76EE;: nginx&#x306E;&#x8A2D;&#x5B9A;&#x3001;&#x305D;&#x306E;3 - location&#x30C7;&#x30A3;&#x30EC;&#x30AF;&#x30C6;&#x30A3;&#x30D6; - &#x30A4;&#x30F3;&#x30D5;&#x30E9;&#x30A8;&#x30F3;&#x30B8;&#x30CB;&#x30A2;way - Powered by HEARTBEATS</a></li>
</ul>


