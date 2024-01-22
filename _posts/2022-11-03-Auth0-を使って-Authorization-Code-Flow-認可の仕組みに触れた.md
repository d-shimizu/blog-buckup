---
title: "Auth0 を使って Authorization Code Flow 認可の仕組みに触れた"
date: 2022-11-03
slug: "Auth0 を使って Authorization Code Flow 認可の仕組みに触れた"
category: blog
tags: [Tech,Auth0,認証]
---
<h2 id="はじめに">はじめに</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/RFC">RFC</a> 6749 で定義されている認可フローは 4 つのあるので、そのうちの 1 つである Authorization Code Flow を Auth0 を使って触れた。</p>

<h2 id="Authorization-Code-Flow">Authorization Code Flow</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/RFC">RFC</a> 6749 の <code>4.1. Authorization Code Grant</code>  に記述されているフロー。 <br/>
認可サーバーの <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> エンドポイントへリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トし、レスポンスとして有効期間の短い認可コードを受けとり、その認可コードを使って認可サーバーのエンドポイントへアクセスし、そのレスポンスとしてアクセス<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ンを取得する、というもの。</p>

<h2 id="環境設定">環境設定</h2>

<h3 id="Auth0-環境設定">Auth0 環境設定</h3>

<p>Auth0 に、Auth0 を利用するアプリケーションを登録する。
ここではアプリケーションタイプを「SPA」にしているけど動かすだけなら多分何でも良い。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20221223/20221223165705.png" width="799" height="725" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<p>ただし、 <code>Allowed Callback URLs</code> を指定する必要がある。 認可フロー時のパラメータとして指定する <code>audience</code> の値がここで許可されたものと一致する必要がある。末尾の <code>/</code> の有無が違っただけで <a class="keyword" href="https://d.hatena.ne.jp/keyword/Access">Access</a> Denied となるので、ここで  <code>/</code> を付与している場合は認可フロー時の  <code>audience</code> パラメータでも指定する。<br/>
<a class="keyword" href="https://d.hatena.ne.jp/keyword/https">https</a> を受けられるものなら何でも良いはずで、今回は <code>https://localhost:3000/</code> としておく。<br/>
※後述で、ローカルでアプリケーションを起動して <code>https://localhost:3000/</code> を受けられるようにしておく。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20221223/20221223173434.png" width="1089" height="680" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<h3 id="ローカルの-http-サーバー環境設定">ローカルの http サーバー環境設定</h3>

<p>自己証明書作成を作成する。</p>

<pre class="code shell" data-lang="shell" data-unlink>% openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 -subj &#34;/C=JP&#34; -nodes -keyout server.key -out server.crt</pre>


<p>適当に下記のような <code>main.go</code> を作成する。</p>

<pre class="code lang-go" data-lang="go" data-unlink><span class="synStatement">package</span> main

<span class="synStatement">import</span> (
    <span class="synConstant">&quot;fmt&quot;</span>
    <span class="synConstant">&quot;log&quot;</span>
    <span class="synConstant">&quot;net/http&quot;</span>
)

<span class="synStatement">type</span> String <span class="synType">string</span>

<span class="synStatement">func</span> (s String) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    fmt.Fprint(w, s)
}

<span class="synStatement">func</span> main() {
    http.Handle(<span class="synConstant">&quot;/&quot;</span>, String(<span class="synConstant">&quot;Hello World.&quot;</span>))

    router := http.NewServeMux()

    router.Handle(<span class="synConstant">&quot;/&quot;</span>, http.HandlerFunc(<span class="synType">func</span>(w http.ResponseWriter, r *http.Request) {
        w.Header().Set(<span class="synConstant">&quot;Content-Type&quot;</span>, <span class="synConstant">&quot;application/json&quot;</span>)
        w.WriteHeader(http.StatusOK)
        w.Write([]<span class="synType">byte</span>(<span class="synConstant">`{&quot;status&quot;:&quot;200 OK&quot;}`</span>))
    }))

    srv := &amp;http.Server{
        Addr:    <span class="synConstant">&quot;localhost:3000&quot;</span>,
        Handler: router,
    }
    <span class="synStatement">if</span> err := srv.ListenAndServeTLS(<span class="synConstant">&quot;server.crt&quot;</span>, <span class="synConstant">&quot;server.key&quot;</span>); err != <span class="synStatement">nil</span> {
        log.Fatalf(<span class="synConstant">&quot;There was an error with the http server: %v&quot;</span>, err)
    }
}
</pre>


<p>http Server を起動させる。</p>

<pre class="code bash" data-lang="bash" data-unlink>% go run main.go</pre>


<h2 id="Authorization-Code-Flow-の処理">Authorization Code Flow の処理</h2>

<h3 id="Authorization-Request">Authorization Request</h3>

<p>ブラウザで、Auth0 の <code>/authorize</code> エンドポイントへリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トする。URL 全体としては下記のようなものになる。
リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トパラメータの <code>audience</code> はコールバックURLに指定したものと同じにする必要がある。<code>client_id</code> は上述で作成したアプリケーションの値を用いる。</p>

<pre class="code" data-lang="" data-unlink>https://{Auth0テナントドメイン}.auth0.com/authorize?audience=https://localhost:3000/&amp;scope=openid profile email&amp;response_type=code&amp;client_id=********&amp;redirect_uri=https://localhost:3000/</pre>


<p>初回は下記のような画面が出てくるので、サインアップする。サインアップすると Auth0 側にユーザーが作成される。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20221223/20221223175027.png" width="649" height="843" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<h3 id="Authorization-Response">Authorization Response</h3>

<p>ブラウザの URL で下記のようなものが返される。このコードをメモする。この値は多分 10 分程度が有効期限と思われる。</p>

<pre class="code" data-lang="" data-unlink>https://localhost:3000/?code=Df********aP</pre>


<h3 id="Access-Token-Request"><a class="keyword" href="https://d.hatena.ne.jp/keyword/Access">Access</a> Token Request</h3>

<p>取得したコードの値を用いて、Auth0 の <code>/oauth/token</code> エンドポイントへリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トする。</p>

<p><code>code</code> パラメータは、上記で取得したものを、<code>client_id</code>, <code>client_secret</code> パラメータは上述で作成したアプリケーションの値を用いる。</p>

<pre class="code shell" data-lang="shell" data-unlink>% curl --request POST \
      --url &#39;https://{Auth0テナントドメイン}.auth0.com/oauth/token&#39; \
      --header &#39;content-type: application/x-www-form-urlencoded&#39; \
      --data &#39;grant_type=authorization_code&amp;client_id=********&amp;client_secret=********&amp;code=Df********aP&amp;redirect_uri=https://localhost:3000/&#39;</pre>


<h3 id="Access-Token-Response"><a class="keyword" href="https://d.hatena.ne.jp/keyword/Access">Access</a> Token Response</h3>

<p>レスポンスとして下記のようなものが返されれば成功となる。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>&quot;<span class="synStatement">access_token</span>&quot;:&quot;<span class="synConstant">********</span>&quot;,&quot;<span class="synStatement">scope</span>&quot;:&quot;<span class="synConstant">openid profile email</span>&quot;,&quot;<span class="synStatement">expires_in</span>&quot;:<span class="synConstant">86400</span>,&quot;<span class="synStatement">token_type</span>&quot;:&quot;<span class="synConstant">Bearer</span>&quot;<span class="synSpecial">}</span>%
</pre>


<h2 id="まとめ">まとめ</h2>

<p>Auth0 を使って Authorization Code Flow の流れを実践してみた。
文字ベースで見ていた仕様の実際の処理の流れのイメージが理解できたのでよかった。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://qiita.com/TakahikoKawasaki/items/200951e5b5929f840a1f#%E3%82%AF%E3%83%A9%E3%82%A4%E3%82%A2%E3%83%B3%E3%83%88%E3%82%AF%E3%83%AC%E3%83%87%E3%83%B3%E3%82%B7%E3%83%A3%E3%83%AB%E3%82%BA%E3%83%95%E3%83%AD%E3%83%BC--authlete">OAuth 2.0 &#x5168;&#x30D5;&#x30ED;&#x30FC;&#x306E;&#x56F3;&#x89E3;&#x3068;&#x52D5;&#x753B; #OAuth - Qiita</a></li>
<li><a href="https://www.rfc-editor.org/rfc/rfc6749#section-4.1">RFC 6749: The OAuth 2.0 Authorization Framework</a></li>
<li><a href="https://dev.classmethod.jp/articles/auth0-access-token-id-token-difference/">Auth0 &#x3092;&#x4F7F;&#x3063;&#x3066; ID Token &#x3068; Access Token &#x306E;&#x9055;&#x3044;&#x3092;&#x3056;&#x3063;&#x304F;&#x308A;&#x7406;&#x89E3;&#x3059;&#x308B; | DevelopersIO</a></li>
</ul>


