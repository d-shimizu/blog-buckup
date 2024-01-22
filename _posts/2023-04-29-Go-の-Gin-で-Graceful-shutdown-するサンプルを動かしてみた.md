---
title: "Go の Gin で Graceful shutdown するサンプルを動かしてみた"
date: 2023-04-29
slug: "Go の Gin で Graceful shutdown するサンプルを動かしてみた"
category: blog
tags: [Go,Gin Web Framework]
---
<h2 id="はじめに">はじめに</h2>

<p>Go の Gin を使ってみて、 Graceful shutdown するにはどうするだろうと調べたメモです。</p>

<h2 id="目次">目次</h2>

<ul class="table-of-contents">
    <li><a href="#はじめに">はじめに</a></li>
    <li><a href="#目次">目次</a></li>
    <li><a href="#環境">環境</a></li>
    <li><a href="#サンプル">サンプル</a></li>
    <li><a href="#試す">試す</a></li>
    <li><a href="#まとめ">まとめ</a></li>
    <li><a href="#参考">参考</a></li>
</ul>

<h2 id="環境">環境</h2>

<p>go 1.20 で試してます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% go version
go version go1.20 linux/amd64</pre>


<h2 id="サンプル">サンプル</h2>

<p>Gin のドキュメントにサンプルの記載がありました。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgin-gonic.com%2Fdocs%2Fexamples%2Fgraceful-restart-or-stop%2F" title="Graceful restart or stop" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://gin-gonic.com/docs/examples/graceful-restart-or-stop/">gin-gonic.com</a></cite></p>

<p>また、<a class="keyword" href="https://d.hatena.ne.jp/keyword/GitHub">GitHub</a> にもサンプルがありました。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Fgin-gonic%2Fexamples%2Ftree%2Fmaster%2Fgraceful-shutdown" title="examples/graceful-shutdown at master · gin-gonic/examples" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://github.com/gin-gonic/examples/tree/master/graceful-shutdown">github.com</a></cite></p>

<p>ほぼサンプル通りですが適当な<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ配下に以下のような <code>main.go</code> を作成&amp;配置して試してみます。</p>

<pre class="code lang-go" data-lang="go" data-unlink><span class="synStatement">package</span> main

<span class="synStatement">import</span> (
    <span class="synConstant">&quot;context&quot;</span>
    <span class="synConstant">&quot;log&quot;</span>
    <span class="synConstant">&quot;net/http&quot;</span>
    <span class="synConstant">&quot;os&quot;</span>
    <span class="synConstant">&quot;os/signal&quot;</span>
    <span class="synConstant">&quot;syscall&quot;</span>
    <span class="synConstant">&quot;time&quot;</span>

    <span class="synConstant">&quot;github.com/gin-gonic/gin&quot;</span>
)

<span class="synStatement">func</span> main() {
    <span class="synComment">//</span>
    ctx, stop := signal.NotifyContext(context.Background(), syscall.SIGINT, syscall.SIGTERM)
    <span class="synStatement">defer</span> stop()

    router := gin.Default()
    router.GET(<span class="synConstant">&quot;/hello&quot;</span>, helloHandler)

    srv := &amp;http.Server{
        Addr:    <span class="synConstant">&quot;:8080&quot;</span>,
        Handler: router,
    }


    <span class="synComment">// ゴルーチンで http サーバーを起動させる</span>
    <span class="synStatement">go</span> <span class="synType">func</span>() {
        <span class="synStatement">if</span> err := srv.ListenAndServe(); err != <span class="synStatement">nil</span> &amp;&amp; err != http.ErrServerClosed {
            log.Fatalf(<span class="synConstant">&quot;listen: %s</span><span class="synSpecial">\n</span><span class="synConstant">&quot;</span>, err)
        }
    }()
    &lt;-ctx.Done()

    stop()
    log.Println(<span class="synConstant">&quot;shutting down gracefully, press Ctrl+C again to force&quot;</span>)

    <span class="synComment">// Background()コンテキストを生成する。</span>
    <span class="synComment">// 指定された時間内(ここでは5秒)に操作が完了しなかった場合にWithTimeout()でタイムアウト処理を行う</span>
    ctx, cancel := context.WithTimeout(context.Background(), <span class="synConstant">5</span>*time.Second)
    <span class="synStatement">defer</span> cancel()
    <span class="synStatement">if</span> err := srv.Shutdown(ctx); err != <span class="synStatement">nil</span> {
        log.Fatal(<span class="synConstant">&quot;Server Shutdown:&quot;</span>, err)
    }

    log.Println(<span class="synConstant">&quot;Server exiting&quot;</span>)
}

<span class="synStatement">func</span> helloHandler(c *gin.Context) {
    <span class="synComment">// テストのため 3 秒待機させる</span>
    time.Sleep(<span class="synConstant">3</span> * time.Second)
    c.String(http.StatusOK, <span class="synConstant">&quot;Welcome Gin Server&quot;</span>)
}
</pre>


<p>作成後にGoモジュールの初期化を行います。</p>

<pre class="code shell" data-lang="shell" data-unlink>% go mod init gin-example
go: creating new go.mod: module gin-example
go: to add module requirements and sums:
    go mod tidy</pre>


<p>必要なモジュールをインストールしまうs。</p>

<pre class="code shell" data-lang="shell" data-unlink>% go mod tidy</pre>


<h2 id="試す">試す</h2>

<p>起動します。</p>

<p>以下のような出力がなされます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% go run main.go
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in &#34;debug&#34; mode. Switch to &#34;release&#34; mode in production.
 - using env:   export GIN_MODE=release
 - using code:  gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /hello                    --&gt; main.helloHandler (3 handlers)</pre>


<p>別ターミナルでリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トしてみます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% curl localhost:8080/hello
Welcome Gin Server%   # 3秒後に出力される</pre>


<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/curl">curl</a> 実行直後にCtrl + Cによる中断を行います。
<code>go run main.go</code> の出力は以下のように5秒待ってからシャットダウンされました。</p>

<pre class="code shell" data-lang="shell" data-unlink>[GIN] 2023/04/27 - 16:22:57 | 200 | 10.000212977s |       127.0.0.1 | GET      &#34;/hello&#34;
^C2023/04/27 16:22:23 Shutdown Server ...    # Ctrl + Cによる中断 

2023/04/27 16:22:28 timeout of 5 seconds.
2023/04/27 16:22:28 Server exiting</pre>


<p>リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト時に10秒待つようにしてみます。</p>

<pre class="code shell" data-lang="shell" data-unlink>func helloHandler(c *gin.Context) {
         // テストのため 10 秒待機させる
    time.Sleep(10 * time.Second)
    c.String(http.StatusOK, &#34;Welcome Gin Server&#34;)
}</pre>


<p>別ターミナルでリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トしてみます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% curl localhost:8080/hello
curl: (52) Empty reply from server    # 先に中断が実行されるため5秒後に左のような出力がなされる</pre>


<p>この場合リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トが中断されました。</p>

<pre class="code" data-lang="" data-unlink>[GIN] 2023/04/27 - 16:22:57 | 200 | 10.000212977s |       127.0.0.1 | GET      &#34;/hello&#34;
^C2023/04/27 16:23:09 Shutdown Server ...
2023/04/27 16:23:14 Server Shutdown:context deadline exceeded
exit status 1</pre>


<h2 id="まとめ">まとめ</h2>

<p>Go の Gin で Graceful shutdown するサンプルを動かしてみました。
net/http でも試してみたいと思います。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://gin-gonic.com/docs/examples/graceful-restart-or-stop/">Graceful restart or stop | Gin Web Framework</a></li>
<li><a href="https://github.com/gin-gonic/examples/tree/master/graceful-shutdown">examples/graceful-shutdown at master &middot; gin-gonic/examples &middot; GitHub</a></li>
</ul>


