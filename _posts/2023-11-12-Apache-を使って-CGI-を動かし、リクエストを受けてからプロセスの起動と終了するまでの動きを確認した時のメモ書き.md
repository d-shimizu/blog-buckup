---
title: "Apache を使って CGI を動かし、リクエストを受けてからプロセスの起動と終了するまでの動きを確認した時のメモ書き"
date: 2023-11-12
slug: "Apache を使って CGI を動かし、リクエストを受けてからプロセスの起動と終了するまでの動きを確認した時のメモ書き"
category: blog
tags: [Apache]
---
<h2 id="はじめに">はじめに</h2>

<p>ふと <a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a> を動かしたことが無かったように思ったので、<a class="keyword" href="https://d.hatena.ne.jp/keyword/Apache">Apache</a>でどうやって動かすのかを調べながら、リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを受けてからプロセスの起動と終了するまでの動きを Docker で動かして確認してみました。</p>

<h2 id="環境">環境</h2>

<p>以下の環境で動かしてみました。</p>

<pre class="code shell" data-lang="shell" data-unlink>% sw_vers
ProductName:        macOS
ProductVersion:     13.6.1
BuildVersion:       22G313</pre>




<pre class="code shell" data-lang="shell" data-unlink>% docker version
Client:
 Cloud integration: v1.0.29
 Version:           20.10.21
 API version:       1.41
 Go version:        go1.18.7
 Git commit:        baeda1f
 Built:             Tue Oct 25 18:01:18 2022
 OS/Arch:           darwin/amd64
 Context:           default
 Experimental:      true

Server: Docker Desktop 4.15.0 (93002)
 Engine:
  Version:          20.10.21
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.18.7
  Git commit:       3056208
  Built:            Tue Oct 25 18:00:19 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.10
  GitCommit:        770bd0108c32f3fb5c73ae1264f7e503fe7b2661
 runc:
  Version:          1.1.4
  GitCommit:        v1.1.4-0-g5fd4c4d
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0</pre>


<h2 id="Common-Gateway-Interface-CGI">Common <a class="keyword" href="https://d.hatena.ne.jp/keyword/Gateway">Gateway</a> Interface (<a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>)</h2>

<p>Common <a class="keyword" href="https://d.hatena.ne.jp/keyword/Gateway">Gateway</a> Interface (<a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>)は、ざっくりいうと、静的な情報しか返せないWebサーバー(HTTPサーバー)でも、外部のプログラム（主には<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8%B8%C0%B8%EC">スクリプト言語</a>で書かれたプログラム）を呼び出すことで動的なコンテンツを生成して返すための仕様。</p>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/RFC">RFC</a> 3875で定義されています。</p>

<ul>
<li><a href="https://datatracker.ietf.org/doc/html/rfc3875">RFC 3875 - The Common Gateway Interface (CGI) Version 1.1</a></li>
</ul>


<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>の仕様に従って作られたプログラムは<a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>プログラムと呼ばれます。
<a class="keyword" href="https://d.hatena.ne.jp/keyword/RFC">RFC</a> 3875によれば、<a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>プログラムは実行可能なバイナリファイル以外に、実行権限が与えられた<a class="keyword" href="https://d.hatena.ne.jp/keyword/Perl">Perl</a>や<a class="keyword" href="https://d.hatena.ne.jp/keyword/bash">bash</a>などの<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>ファイルのほか、<a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>の仕様に従っていれば共有ライブラリや、Webサーバー内のサブルーチンも<a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>プログラムに含まれるようです。
<a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>プログラムは<a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>の仕様に従って、<a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを受け取り<a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>レスポンスを返さなければなりません。ただ、大抵はライブラリ等がこの役割を担ってくれると思うのでそんなに意識する必要はないかもしれません。</p>

<ul>
<li><a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>の呼び出し方 <a href="https://datatracker.ietf.org/doc/html/rfc3875#section-3">https://datatracker.ietf.org/doc/html/rfc3875#section-3</a></li>
<li><a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト <a href="https://datatracker.ietf.org/doc/html/rfc3875#section-4">https://datatracker.ietf.org/doc/html/rfc3875#section-4</a></li>
<li><a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>レスポンス <a href="https://datatracker.ietf.org/doc/html/rfc3875#section-6">https://datatracker.ietf.org/doc/html/rfc3875#section-6</a></li>
<li>セキュリティ <a href="https://datatracker.ietf.org/doc/html/rfc3875#section-9">https://datatracker.ietf.org/doc/html/rfc3875#section-9</a></li>
</ul>


<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a> 処理の流れの概要は次のような形かと思います。
リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トが発生する度にアプリケーションのプロセスが起動して結果を返したらプロセスが終了する形で、リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トが多くなる程その処理が毎回繰り返されるので、今はほぼ使われなくなっていて、代わりにプロセスが常時起動している <a class="keyword" href="https://d.hatena.ne.jp/keyword/FastCGI">FastCGI</a> とかになっていると思います。</p>

<pre class="code text" data-lang="text" data-unlink>-----------           -----------            -----------
| Web     | -&gt; 要求 -&gt; | Web     | -&gt; 起動 -&gt; | プログラム |
| ブラウザ  | &lt;- 結果 &lt;- | サーバ－  | &lt;- 結果 &lt;- |          |
-----------           -----------            -----------</pre>


<h2 id="動かす">動かす</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/Apache">Apache</a> を使って、<a class="keyword" href="https://d.hatena.ne.jp/keyword/Perl">Perl</a> のプログラムを <a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a> で動かしてみます。</p>

<h3 id="Docker-Compose-の作成">Docker Compose の作成</h3>

<p>以下のようなDockerComposeを作成します。</p>

<pre class="code lang-yaml" data-lang="yaml" data-unlink><span class="synIdentifier">version</span><span class="synSpecial">:</span> <span class="synConstant">'3'</span>

<span class="synIdentifier">services</span><span class="synSpecial">:</span>
  <span class="synIdentifier">httpd</span><span class="synSpecial">:</span>
    <span class="synIdentifier">image</span><span class="synSpecial">:</span> httpd:2.4-alpine
    <span class="synIdentifier">volumes</span><span class="synSpecial">:</span>
      <span class="synStatement">- </span><span class="synIdentifier">type</span><span class="synSpecial">:</span> bind
        <span class="synIdentifier">source</span><span class="synSpecial">:</span> apache2/conf/httpd.conf
        <span class="synIdentifier">target</span><span class="synSpecial">:</span> /usr/local/apache2/conf/httpd.conf
      <span class="synStatement">- </span><span class="synIdentifier">type</span><span class="synSpecial">:</span> bind
        <span class="synIdentifier">source</span><span class="synSpecial">:</span> ./apache2/conf/extra/httpd-vhosts.conf
        <span class="synIdentifier">target</span><span class="synSpecial">:</span> /usr/local/apache2/conf/extra/httpd-vhosts.conf
      <span class="synStatement">- </span><span class="synIdentifier">type</span><span class="synSpecial">:</span> bind
        <span class="synIdentifier">source</span><span class="synSpecial">:</span> ./apache2/vhosts/localhost
        <span class="synIdentifier">target</span><span class="synSpecial">:</span> /usr/local/apache2/vhosts/localhost
    <span class="synIdentifier">ports</span><span class="synSpecial">:</span>
      <span class="synStatement">- </span><span class="synConstant">&quot;20080:80&quot;</span>
</pre>


<h3 id="Apache-の設定"><a class="keyword" href="https://d.hatena.ne.jp/keyword/Apache">Apache</a> の設定</h3>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/Apache">Apache</a>の設定ファイルを準備します。
単なる動作確認用なのであまり深いことは考えず、ローカルでは docker-compose.<a class="keyword" href="https://d.hatena.ne.jp/keyword/yaml">yaml</a> の定義に合わせてファイルを配置し、ボリュームマウントさせています。</p>

<p><code>httpd.conf</code> を以下のようなもので作成します。元の <code>httpd.conf</code> から <code>Include conf/extra/httpd-vhosts.conf</code> の<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B3%A5%E1%A5%F3%A5%C8%A5%A2%A5%A6%A5%C8">コメントアウト</a>を外して有効化した形です。ここではファイルは <code>httpd-vhosts.conf</code> にしてますが新規に作成する何かでも何でも良いと思います。</p>

<pre class="code lang-conf" data-lang="conf" data-unlink>ServerRoot <span class="synConstant">&quot;/usr/local/apache2&quot;</span>
Listen 80
LoadModule mpm_event_module modules/mod_mpm_event.so
LoadModule authn_file_module modules/mod_authn_file.so
LoadModule authn_core_module modules/mod_authn_core.so
LoadModule authz_host_module modules/mod_authz_host.so
LoadModule authz_groupfile_module modules/mod_authz_groupfile.so
LoadModule authz_user_module modules/mod_authz_user.so
LoadModule authz_core_module modules/mod_authz_core.so
LoadModule access_compat_module modules/mod_access_compat.so
LoadModule auth_basic_module modules/mod_auth_basic.so
LoadModule reqtimeout_module modules/mod_reqtimeout.so
LoadModule filter_module modules/mod_filter.so
LoadModule mime_module modules/mod_mime.so
LoadModule log_config_module modules/mod_log_config.so
LoadModule env_module modules/mod_env.so
LoadModule headers_module modules/mod_headers.so
LoadModule setenvif_module modules/mod_setenvif.so
LoadModule version_module modules/mod_version.so
LoadModule unixd_module modules/mod_unixd.so
LoadModule status_module modules/mod_status.so
LoadModule autoindex_module modules/mod_autoindex.so
&lt;IfModule !mpm_prefork_module&gt;
&lt;/IfModule&gt;
&lt;IfModule mpm_prefork_module&gt;
&lt;/IfModule&gt;
LoadModule dir_module modules/mod_dir.so
LoadModule alias_module modules/mod_alias.so
&lt;IfModule unixd_module&gt;
User www-data
Group www-data
&lt;/IfModule&gt;
ServerAdmin you@example.com
&lt;Directory /&gt;
    AllowOverride none
    Require all denied
&lt;/Directory&gt;
DocumentRoot <span class="synConstant">&quot;/usr/local/apache2/htdocs&quot;</span>
&lt;Directory <span class="synConstant">&quot;/usr/local/apache2/htdocs&quot;</span>&gt;
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
&lt;/Directory&gt;
&lt;IfModule dir_module&gt;
    DirectoryIndex index.html
&lt;/IfModule&gt;
&lt;Files <span class="synConstant">&quot;.ht*&quot;</span>&gt;
    Require all denied
&lt;/Files&gt;
ErrorLog /proc/self/fd/2
LogLevel warn
&lt;IfModule log_config_module&gt;
    LogFormat <span class="synConstant">&quot;%h %l %u %t \&quot;%r\&quot; %&gt;s %b \&quot;%{Referer}i\&quot; \&quot;%{User-Agent}i\&quot;&quot;</span> combined
    LogFormat <span class="synConstant">&quot;%h %l %u %t \&quot;%r\&quot; %&gt;s %b&quot;</span> common
    &lt;IfModule logio_module&gt;
      LogFormat <span class="synConstant">&quot;%h %l %u %t \&quot;%r\&quot; %&gt;s %b \&quot;%{Referer}i\&quot; \&quot;%{User-Agent}i\&quot; %I %O&quot;</span> combinedio
    &lt;/IfModule&gt;
    CustomLog /proc/self/fd/1 common
&lt;/IfModule&gt;
&lt;IfModule alias_module&gt;
    ScriptAlias /cgi-bin/ <span class="synConstant">&quot;/usr/local/apache2/cgi-bin/&quot;</span>
&lt;/IfModule&gt;
&lt;IfModule cgid_module&gt;
&lt;/IfModule&gt;
&lt;Directory <span class="synConstant">&quot;/usr/local/apache2/cgi-bin&quot;</span>&gt;
    AllowOverride None
    Options None
    Require all granted
&lt;/Directory&gt;
&lt;IfModule headers_module&gt;
    RequestHeader unset Proxy early
&lt;/IfModule&gt;
&lt;IfModule mime_module&gt;
    TypesConfig conf/mime.types
    AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz
&lt;/IfModule&gt;
Include conf/extra/httpd-vhosts.conf
&lt;IfModule proxy_html_module&gt;
Include conf/extra/proxy-html.conf
&lt;/IfModule&gt;
&lt;IfModule ssl_module&gt;
SSLRandomSeed startup builtin
SSLRandomSeed connect builtin
&lt;/IfModule&gt;
</pre>


<p>ローカル確認用に <a class="keyword" href="https://d.hatena.ne.jp/keyword/localhost">localhost</a>:80 のバーチャルホストを設定します。</p>

<p><code>conf/extra/httpd-vhosts.conf</code> を新規に作成して設定を行います。
<a class="keyword" href="https://d.hatena.ne.jp/keyword/Apache">Apache</a> で <a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a> を動かすためには <code>mod_cgi</code> が必要なため、これをロードします。
コンテナ上の <code>/usr/local/apache2/vhosts/localhost/cgi-bin</code> に <a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>を配置するため、<code>.cgi</code> や .pl` を<a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a><a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>として認識するように設定します。</p>

<pre class="code lang-conf" data-lang="conf" data-unlink>LoadModule cgi_module modules/mod_cgi.so

&lt;VirtualHost *:80&gt;
    ServerName localhost
    DocumentRoot <span class="synConstant">&quot;/usr/local/apache2/vhosts/localhost/htdocs&quot;</span>

    ScriptAlias /cgi-bin/ /usr/local/apache2/vhosts/localhost/cgi-bin/

    &lt;Directory <span class="synConstant">&quot;/usr/local/apache2/vhosts/localhost/htdocs&quot;</span>&gt;
        Options None
        AllowOverride None
        Require all granted
        DirectoryIndex index.html
    &lt;/Directory&gt;

    &lt;Directory <span class="synConstant">&quot;/usr/local/apache2/vhosts/localhost/cgi-bin&quot;</span>&gt;
        AllowOverride None
            Options +ExecCGI
        Require all granted
        AddHandler cgi-script .cgi .pl
    &lt;/Directory&gt;
&lt;/VirtualHost&gt;
</pre>


<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>となる <code>vhosts/localhost/cgi-bin/cgi-test.pl</code> を作成します。</p>

<pre class="code lang-perl" data-lang="perl" data-unlink><span class="synPreProc">#!/usr/bin/perl</span>

<span class="synStatement">print</span> <span class="synConstant">&quot;Content-type: text/html</span><span class="synSpecial">\n\n</span><span class="synConstant">&quot;</span>;
<span class="synStatement">print</span> <span class="synConstant">&quot;Hello, World!!</span><span class="synSpecial">\n\n</span><span class="synConstant">&quot;</span>;

<span class="synComment"># cgi としてプロセスが起動していることを確認するために 10 秒スリープ</span>
<span class="synStatement">sleep</span>(<span class="synConstant">10</span>)
</pre>


<h3 id="起動">起動</h3>

<p>コンテナを起動します。</p>

<pre class="code shell" data-lang="shell" data-unlink>% docker compose up -d
[+] Running 2/2
 ⠿ Network apache-cgi-exec-sample_default    Created                                                                                                                                                                                                                                                                                                                 0.5s
 ⠿ Container apache-cgi-exec-sample-httpd-1  Started</pre>


<p>コンテナ起動後に <code>http://localhost:20080/</code> でアクセス可能になるので、<code>http://localhost:20080/cgi-bin/cgi-test.pl</code>にアクセスしてみると<a class="keyword" href="https://d.hatena.ne.jp/keyword/Perl">Perl</a>のプログラムの実行結果が表示されています。</p>

<p>コンテナにログインしてプロセスを見ると、<code>cgi-test.pl</code> がプロセスとして起動し、sleep 処理の10秒間は実行中の状態で、処理が終わるとプロセスが消える、という<a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a>の一連の動作が確認できました。</p>

<pre class="code shell" data-lang="shell" data-unlink>% docker exec -ti apache-cgi-exec-sample-httpd-1 /bin/sh

/usr/local/apache2 # ps auxf
PID   USER     TIME  COMMAND
    1 root      0:00 httpd -DFOREGROUND
    7 www-data  0:00 httpd -DFOREGROUND
    8 www-data  0:00 httpd -DFOREGROUND
   10 www-data  0:00 httpd -DFOREGROUND
   95 root      0:00 /bin/sh
  102 www-data  0:00 {first.pl} /usr/bin/perl /usr/local/apache2/vhosts/localhost/cgi-bin/cgi-test.pl
  103 www-data  0:00 httpd -DFOREGROUND
  131 root      0:00 ps auxf</pre>


<h2 id="まとめ">まとめ</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/Apache">Apache</a> で <a class="keyword" href="https://d.hatena.ne.jp/keyword/CGI">CGI</a> プログラムを実行して、リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを受けてからプロセスの起動と終了するまでの動きを見てみました。
簡単ですが、改めて動きを確認し理解が深まりました。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://datatracker.ietf.org/doc/html/rfc3875">RFC 3875 - The Common Gateway Interface (CGI) Version 1.1</a></li>
<li><a href="https://httpd.apache.org/docs/2.4/ja/howto/cgi.html">Apache Tutorial: CGI &#x306B;&#x3088;&#x308B;&#x52D5;&#x7684;&#x30B3;&#x30F3;&#x30C6;&#x30F3;&#x30C4; - Apache HTTP &#x30B5;&#x30FC;&#x30D0; &#x30D0;&#x30FC;&#x30B8;&#x30E7;&#x30F3; 2.4</a></li>
</ul>


