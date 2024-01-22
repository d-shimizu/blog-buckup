---
title: "Auth0 のチュートリアルをやって Client Credentials Flow の認可の仕組みに触れた"
date: 2022-10-22
slug: "Auth0 のチュートリアルをやって Client Credentials Flow の認可の仕組みに触れた"
category: blog
tags: [Tech,Auth0,認証]
---
<h2 id="はじめに">はじめに</h2>

<p>認証認可について理解が浅かったので、Auth0 の<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C1%A5%E5%A1%BC%A5%C8%A5%EA%A5%A2%A5%EB">チュートリアル</a>をやってみた時の備忘。</p>

<p>基本的には下記のリンク先に書いてある通りのことをやったのみとなる。</p>

<ul>
<li><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fauth0.com%2Fdocs%2Fquickstart%2Fbackend%2Fgolang" title="Auth0 Go API SDK Quickstarts: Authorization" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://auth0.com/docs/quickstart/backend/golang">auth0.com</a></cite></li>
</ul>


<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/RFC">RFC</a> 6749 では、アクセス<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ンを取得するための <code>Authorization Code</code>, <code>Implicit</code>,  <code>Resource Owner Password Credentials</code>, <code>Client Credentials</code> の 4 つのフローが定義されている。
この<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C1%A5%E5%A1%BC%A5%C8%A5%EA%A5%A2%A5%EB">チュートリアル</a>では <code>Client Credentials</code> フローで実行する形となる。</p>

<ul>
<li><a href="https://www.rfc-editor.org/rfc/rfc6749">RFC 6749: The OAuth 2.0 Authorization Framework</a></li>
</ul>


<h2 id="Client-Credentials-Flow">Client Credentials Flow</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/RFC">RFC</a> 6749 の <code>4.4. Client Credentials Grant</code> の項目で定義されているフロー。
認可サーバーここでは Auth0)の <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> エンドポイント(にリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを投げて、そのレスポンスでアクセス<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ンを受け取る。
ユーザー認証自体は行われず、リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト元となるクライアントの認証のみ行うような形となるやり方になる。</p>

<h2 id="環境">環境</h2>

<ul>
<li>Auth0 アカウント (Auth0 のアカウントを作成しておく)</li>
<li>Go 1.19 (認可に利用するテスト用の <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> 用)</li>
</ul>


<h2 id="Auth0-の設定">Auth0 の設定</h2>

<p>Auth0 の APIs に、自分が作成する <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> アプリケーションを示す情報を登録する。
値は何でも良いけど  <code>Identifier</code> の値は、認可リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トで利用することになり、後の変更不可なので注意。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20221222/20221222122328.png" width="1200" height="633" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<p><code>APIs</code> の項目に遷移し、 <code>Permission</code> を定義する。
<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C1%A5%E5%A1%BC%A5%C8%A5%EA%A5%A2%A5%EB">チュートリアル</a>の通りに、 <code>Permission</code> に <code>read:messages</code> を、 <code>Description</code> に <code>read messages</code> としておく。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20221222/20221222122352.png" width="1200" height="635" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<p><code>Settings</code> で、 <code>RBAC Settings</code> の項目から、 <code>Enable RBAC</code> と <code>Add Permissions in the Access Token</code> を有効化し、画面下の <code>Save</code> ボタンをクリックする。これでアクセス<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ンに <code>Permissions</code> の項目が含まれるようになる。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20221223/20221223142606.png" width="790" height="1200" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<p><code>Applications</code> の項目に遷移し、 <code>APIs</code> タブの画面で、<code>Permissions</code> から、先ほど作成した <code>read:messages</code> にチェックを入れ、画面下の <code>Update</code> ボタンをクリックして更新する。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20221223/20221223142712.png" width="1200" height="892" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<h2 id="API-環境作成"><a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> 環境作成</h2>

<h3 id="モジュール用ディレクトリ作成">モジュール用<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ作成</h3>

<p>モジュール用<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成する。</p>

<pre class="code shell" data-lang="shell" data-unlink>% mkdir auth0-go</pre>


<p>その<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リに移動して、 <code>go mod init</code> を実行する。</p>

<pre class="code shell" data-lang="shell" data-unlink>% cd auth0-go</pre>


<h3 id="モジュールの初期化">モジュールの初期化</h3>

<p> <code>go mod init</code> を実行してモジュールを初期化する。
必要に応じて引数としてモジュール名(ここでは <code>localhost/auth0-go</code> としているが任意)を指定する。</p>

<pre class="code shell" data-lang="shell" data-unlink>% go mod init localhost/auth0-go</pre>


<h3 id="env-ファイル作成">.env ファイル作成</h3>

<p>Auth0 の各種パラメータを定義した <code>.env</code> ファイルを作成する。</p>

<pre class="code dotfiles" data-lang="dotfiles" data-unlink>% cat .env
# Auth0 テナント ドメインの URL。
# カスタム ドメインを使用している場合は、そのドメインを設定する。
AUTH0_DOMAIN=&#39;********.{Region}.auth0.com&#39;

# API の AUDIENCE
AUTH0_AUDIENCE=&#39;https://localhost:3000/&#39;  # &lt;- Auth0の</pre>


<h3 id="自己証明書作成">自己証明書作成</h3>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/%C8%EB%CC%A9%B8%B0">秘密鍵</a>/<a class="keyword" href="https://d.hatena.ne.jp/keyword/CSR">CSR</a>と証明書を作成する。</p>

<pre class="code" data-lang="" data-unlink>% openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 -subj &#34;/C=JP&#34; -nodes -keyout server.key -out server.crt</pre>


<h3 id="アプリケーション作成">アプリケーション作成</h3>

<p><code>auth0-go</code> 直下に <code>middleware</code> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成し、 <code>jwt.go</code> を作成する。
Auth0 のライブラリを使ってアクセス<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ンを検証する処理を記述する。</p>

<p>以下では JWKS (<a class="keyword" href="https://d.hatena.ne.jp/keyword/Json">Json</a> Web Key Set)を使うようにしている。
JWKS についてはまだあまり詳しく理解できていないが、 <code>https://{Auth0テナント名}.us.auth0.com/.well-known/jwks.json</code> の情報をもとに <code>KeyFunc</code> の値を生成して、この値を JWT の検証に利用するもの、と思っている。</p>

<p>JWKS を使わない場合は Auth0 の APIs の Signing Secrets の値を KeyFunc へセットするようにし、JWT の検証を行う。</p>

<p><code>KeyFunc</code> というのが一般的な用語なのかもわかってないけど <code>go-jwt-middleware</code> のパッケージでは <code>KeyFunc</code> という名称のものを利用している。</p>

<pre class="code lang-go" data-lang="go" data-unlink><span class="synStatement">package</span> middleware

<span class="synStatement">import</span> (
    <span class="synConstant">&quot;context&quot;</span>
    <span class="synConstant">&quot;log&quot;</span>
    <span class="synConstant">&quot;net/http&quot;</span>
    <span class="synConstant">&quot;net/url&quot;</span>
    <span class="synConstant">&quot;os&quot;</span>
    <span class="synConstant">&quot;strings&quot;</span>
    <span class="synConstant">&quot;time&quot;</span>

    jwtmiddleware <span class="synConstant">&quot;github.com/auth0/go-jwt-middleware/v2&quot;</span>
    <span class="synConstant">&quot;github.com/auth0/go-jwt-middleware/v2/jwks&quot;</span>
    <span class="synConstant">&quot;github.com/auth0/go-jwt-middleware/v2/validator&quot;</span>
)

<span class="synComment">// CustomClaims contains custom data we want from the token.</span>
<span class="synStatement">type</span> CustomClaims <span class="synStatement">struct</span> {
    Scope <span class="synType">string</span> <span class="synConstant">`json:&quot;scope&quot;`</span>
}

<span class="synComment">// Validate does nothing for this example, but we need</span>
<span class="synComment">// it to satisfy validator.CustomClaims interface.</span>
<span class="synStatement">func</span> (c CustomClaims) Validate(ctx context.Context) <span class="synType">error</span> {
    <span class="synStatement">return</span> <span class="synStatement">nil</span>
}

<span class="synStatement">func</span> (c CustomClaims) HasScope(expectedScope <span class="synType">string</span>) <span class="synType">bool</span> {
    result := strings.Split(c.Scope, <span class="synConstant">&quot; &quot;</span>)
    <span class="synStatement">for</span> i := <span class="synStatement">range</span> result {
        <span class="synStatement">if</span> result[i] == expectedScope {
            <span class="synStatement">return</span> <span class="synStatement">true</span>
        }
    }

    <span class="synStatement">return</span> <span class="synStatement">false</span>
}

<span class="synComment">// EnsureValidToken is a middleware that will check the validity of our JWT.</span>
<span class="synStatement">func</span> EnsureValidToken() <span class="synType">func</span>(next http.Handler) http.Handler {
    issuerURL, err := url.Parse(<span class="synConstant">&quot;https://&quot;</span> + os.Getenv(<span class="synConstant">&quot;AUTH0_DOMAIN&quot;</span>) + <span class="synConstant">&quot;/&quot;</span>)
    <span class="synStatement">if</span> err != <span class="synStatement">nil</span> {
        log.Fatalf(<span class="synConstant">&quot;Failed to parse the issuer url: %v&quot;</span>, err)
    }

    provider := jwks.NewCachingProvider(issuerURL, <span class="synConstant">5</span>*time.Minute)

    jwtValidator, err := validator.New(
        provider.KeyFunc,
        validator.RS256,
        issuerURL.String(),
        []<span class="synType">string</span>{os.Getenv(<span class="synConstant">&quot;AUTH0_AUDIENCE&quot;</span>)},
        validator.WithCustomClaims(
            <span class="synType">func</span>() validator.CustomClaims {
                <span class="synStatement">return</span> &amp;CustomClaims{}
            },
        ),
        validator.WithAllowedClockSkew(time.Minute),
    )
    <span class="synStatement">if</span> err != <span class="synStatement">nil</span> {
        log.Fatalf(<span class="synConstant">&quot;Failed to set up the jwt validator&quot;</span>)
    }

    errorHandler := <span class="synType">func</span>(w http.ResponseWriter, r *http.Request, err <span class="synType">error</span>) {
        log.Printf(<span class="synConstant">&quot;Encountered error while validating JWT: %v&quot;</span>, err)

        w.Header().Set(<span class="synConstant">&quot;Content-Type&quot;</span>, <span class="synConstant">&quot;application/json&quot;</span>)
        w.WriteHeader(http.StatusUnauthorized)
        w.Write([]<span class="synType">byte</span>(<span class="synConstant">`{&quot;message&quot;:&quot;Failed to validate JWT.&quot;}`</span>))
    }

    middleware := jwtmiddleware.New(
        jwtValidator.ValidateToken,
        jwtmiddleware.WithErrorHandler(errorHandler),
    )

    <span class="synStatement">return</span> <span class="synType">func</span>(next http.Handler) http.Handler {
        <span class="synStatement">return</span> middleware.CheckJWT(next)
    }
}
</pre>


<p><code>auth0-go</code> 直下に <code>main.go</code> を作成する。
テスト用に３本の<a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> ( <code>/api/public</code>, <code>/api/private</code>, <code>/api/private-socped</code> ) を定義している。</p>

<pre class="code lang-go" data-lang="go" data-unlink><span class="synStatement">package</span> main

<span class="synStatement">import</span> (
    <span class="synConstant">&quot;fmt&quot;</span>
    <span class="synConstant">&quot;log&quot;</span>
    <span class="synConstant">&quot;net/http&quot;</span>

    jwtmiddleware <span class="synConstant">&quot;github.com/auth0/go-jwt-middleware/v2&quot;</span>
    <span class="synConstant">&quot;github.com/auth0/go-jwt-middleware/v2/validator&quot;</span>
    <span class="synConstant">&quot;github.com/joho/godotenv&quot;</span>

    <span class="synConstant">&quot;localhost/auth0-go/middleware&quot;</span>
)

<span class="synStatement">func</span> main() {
    <span class="synStatement">if</span> err := godotenv.Load(); err != <span class="synStatement">nil</span> {
        log.Fatalf(<span class="synConstant">&quot;Error loading the .env file: %v&quot;</span>, err)
    }

    router := http.NewServeMux()

    <span class="synComment">// This route is always accessible.</span>
    router.Handle(<span class="synConstant">&quot;/api/public&quot;</span>, http.HandlerFunc(<span class="synType">func</span>(w http.ResponseWriter, r *http.Request) {
        w.Header().Set(<span class="synConstant">&quot;Content-Type&quot;</span>, <span class="synConstant">&quot;application/json&quot;</span>)
        w.WriteHeader(http.StatusOK)
        w.Write([]<span class="synType">byte</span>(<span class="synConstant">`{&quot;message&quot;:&quot;Hello from a public endpoint! You don't need to be authenticated to see this.&quot;}`</span>))
    }))

    <span class="synComment">// This route is only accessible if the user has a valid access_token.</span>
    router.Handle(<span class="synConstant">&quot;/api/private&quot;</span>, middleware.EnsureValidToken()(
        http.HandlerFunc(<span class="synType">func</span>(w http.ResponseWriter, r *http.Request) {
            <span class="synComment">// CORS Headers.</span>
            w.Header().Set(<span class="synConstant">&quot;Access-Control-Allow-Credentials&quot;</span>, <span class="synConstant">&quot;true&quot;</span>)
            w.Header().Set(<span class="synConstant">&quot;Access-Control-Allow-Origin&quot;</span>, <span class="synConstant">&quot;https://localhost:3000&quot;</span>)
            w.Header().Set(<span class="synConstant">&quot;Access-Control-Allow-Headers&quot;</span>, <span class="synConstant">&quot;Authorization&quot;</span>)

            w.Header().Set(<span class="synConstant">&quot;Content-Type&quot;</span>, <span class="synConstant">&quot;application/json&quot;</span>)
            w.WriteHeader(http.StatusOK)
            w.Write([]<span class="synType">byte</span>(<span class="synConstant">`{&quot;message&quot;:&quot;Hello from a private endpoint! You need to be authenticated to see this.&quot;}`</span>))
        }),
    ))

    router.Handle(<span class="synConstant">&quot;/api/private-scoped&quot;</span>, middleware.EnsureValidToken()(
        http.HandlerFunc(<span class="synType">func</span>(w http.ResponseWriter, r *http.Request) {
            w.Header().Set(<span class="synConstant">&quot;Content-Type&quot;</span>, <span class="synConstant">&quot;application/json&quot;</span>)

            token := r.Context().Value(jwtmiddleware.ContextKey{}).(*validator.ValidatedClaims)
            fmt.Println(token)

            claims := token.CustomClaims.(*middleware.CustomClaims)
            fmt.Println(claims)
            <span class="synStatement">if</span> !claims.HasScope(<span class="synConstant">&quot;read:messages&quot;</span>) {
                w.WriteHeader(http.StatusForbidden)
                w.Write([]<span class="synType">byte</span>(<span class="synConstant">`{&quot;message&quot;:&quot;Insufficient scope.&quot;}`</span>))
                <span class="synStatement">return</span>
            }

            w.WriteHeader(http.StatusOK)
            w.Write([]<span class="synType">byte</span>(<span class="synConstant">`{&quot;message&quot;:&quot;Hello from a private endpoint! You need to be authenticated to see this.&quot;}`</span>))
        }),
    ))

    log.Print(<span class="synConstant">&quot;Server listening on https://localhost:3000&quot;</span>)

    srv := &amp;http.Server{
        Addr:    <span class="synConstant">&quot;localhost:3000&quot;</span>,
        Handler: router,
    }
    <span class="synStatement">if</span> err := srv.ListenAndServeTLS(<span class="synConstant">&quot;server.crt&quot;</span>, <span class="synConstant">&quot;server.key&quot;</span>); err != <span class="synStatement">nil</span> {
        log.Fatalf(<span class="synConstant">&quot;There was an error with the http server: %v&quot;</span>, err)
    }
}
</pre>


<h2 id="Client-Credentials-Flow-を試す">Client Credentials Flow を試す。</h2>

<h3 id="Access-Token-Request"><a class="keyword" href="https://d.hatena.ne.jp/keyword/Access">Access</a> Token Request</h3>

<p>Auth0 のマネジメント <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> (<code>/oauth/token</code>)へリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トし、アクセス<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ンを取得する。
<code>client_id</code> と <code>client_secret</code> パラメータは、 <code>Applications</code> の <code>Settings</code> 画面から値を確認してセットする。</p>

<pre class="code shell" data-lang="shell" data-unlink>% curl --request POST \
  --url https://********.{Region}.auth0.com/oauth/token \
  --header &#39;content-type: application/json&#39; \
  --data &#39;{&#34;client_id&#34;:&#34;xxxxxxxx&#34;,&#34;client_secret&#34;:&#34;xxxxxxxx&#34;,&#34;audience&#34;:&#34;https://localhost:3000/&#34;,&#34;grant_type&#34;:&#34;client_credentials&#34;}&#39;</pre>


<h3 id="Access-Token-Response"><a class="keyword" href="https://d.hatena.ne.jp/keyword/Access">Access</a> Token Response</h3>

<p>以下のような形で結果が返って来れば成功となる。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>&quot;<span class="synStatement">access_token</span>&quot;:&quot;<span class="synConstant">****...****</span>&quot;,&quot;<span class="synStatement">expires_in</span>&quot;:<span class="synConstant">86400</span>,&quot;<span class="synStatement">token_type</span>&quot;:&quot;<span class="synConstant">Bearer</span>&quot;<span class="synSpecial">}</span>%
</pre>


<p>取得したアクセス<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ンを用いて、作った <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> へリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トする。</p>

<pre class="code shell" data-lang="shell" data-unlink>~ %  curl --request GET -k --url https://localhost:3000/api/private-scoped --header &#39;Authorization: bearer ****...****&#39;</pre>


<p>下記のような結果が返されれば正常なリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トが実行できている。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>&quot;<span class="synStatement">message</span>&quot;:&quot;<span class="synConstant">Hello from a private endpoint! You need to be authenticated to see this.</span>&quot;<span class="synSpecial">}</span>%
</pre>


<p>アクセス<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ンが不正なリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを送ってみる。</p>

<pre class="code shell" data-lang="shell" data-unlink>~ %  curl --request GET -k --url https://localhost:3000/api/private --header &#39;Authorization: bearer ****...****z&#39;</pre>


<p>JWT の検証でエラーが返される。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>&quot;<span class="synStatement">message</span>&quot;:&quot;<span class="synConstant">Failed to validate JWT.</span>&quot;<span class="synSpecial">}</span>%
</pre>


<p><code>Applications</code> の項目に遷移し、 <code>APIs</code> タブの画面で、<code>Permissions</code> から、先ほど作成した <code>read:messages</code> にチェックを外し、画面下の <code>Update</code> ボタンをクリックして更新した状態で、再度アクセス<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ンを取得し、そのアクセス<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ンを使ってリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トする。</p>

<pre class="code shell" data-lang="shell" data-unlink>~ %  curl --request GET -k --url https://localhost:3000/api/private-scoped --header &#39;Authorization: bearer ****...****&#39;</pre>


<p>以下のようなエラーが返され、きちんと Permission の検証が行われている正常な動作を確認できる。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>&quot;<span class="synStatement">message</span>&quot;:&quot;<span class="synConstant">Insufficient scope.</span>&quot;<span class="synSpecial">}</span>%
</pre>


<h2 id="まとめ">まとめ</h2>

<p>Auth0 の<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C1%A5%E5%A1%BC%A5%C8%A5%EA%A5%A2%A5%EB">チュートリアル</a>をやって Client Credentials Flow の認可の仕組みに触れた。
認可の仕組みにいろいろなやり方があることをわかっていなかったので、その辺りの理解が深まったのと、実際に Client Credentials Flow で動かしてみて処理の流れが掴めたのはよかった。</p>

<h2 id="参考">参考</h2>

<ul>
<li><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fauth0.com%2Fdocs%2Fquickstart%2Fbackend%2Fgolang" title="Auth0 Go API SDK Quickstarts: Authorization" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><a href="https://auth0.com/docs/quickstart/backend/golang">Auth0 Go API SDK Quickstarts: Authorization</a></li>
<li><a href="https://auth0.com/docs/quickstart/backend/golang/interactive">Auth0 Go API SDK Quickstarts: Add authorization to a Go API</a></li>
<li><a href="https://auth0.com/docs/get-started/architecture-scenarios/server-application-api/api-implementation-nodejs">Server Apps + API: Node.js Implementation for the API</a></li>
<li><a href="https://auth0.com/docs/secure/tokens/access-tokens/get-management-api-access-tokens-for-production">Get Management API Access Tokens for Production</a></li>
<li><a href="https://auth0.com/blog/id-token-access-token-what-is-the-difference/">ID Token and Access Token: What Is the Difference?</a></li>
<li><a href="https://auth0.com/docs">Auth0</a></li>
<li><a href="https://www.ibm.com/docs/ja/sva/10.0.0?topic=applications-jwks">JWKS</a></li>
<li><a href="https://pkg.go.dev/github.com/auth0/go-jwt-middleware/v2@v2.1.0/jwks">jwks package - github.com/auth0/go-jwt-middleware/v2/jwks - Go Packages</a></li>
<li><a href="https://github.com/MicahParks/compatibility-keyfunc">GitHub - MicahParks/compatibility-keyfunc: Create a jwt.Keyfunc for JWT parsing with a JWKS or given cryptographic keys in Golang. LEGACY FORKS ONLY. Use https://github.com/MicahParks/keyfunc for github.com/golang-jwt/jwt</a></li>
<li><a href="https://github.com/auth0/go-jwt-middleware/blob/v2.0.1/validator/validator.go">go-jwt-middleware/validator/validator.go at v2.0.1 &middot; auth0/go-jwt-middleware &middot; GitHub</a></li>
<li><a href="https://openid-foundation-japan.github.io/rfc7517.ja.html">JSON Web Key (JWK)</a></li>
<li><a href="https://pkg.go.dev/github.com/golang-jwt/jwt/v4#section-readme">jwt package - github.com/golang-jwt/jwt/v4 - Go Packages</a></li>
<li><a href="https://tech-lab.sios.jp/archives/8026">&#x591A;&#x5206;&#x308F;&#x304B;&#x308A;&#x3084;&#x3059;&#x3044;OAuth | SIOS Tech. Lab</a></li>
</ul>


