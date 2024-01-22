---
title: "Go の Live Reload を実行してくれる air というツールを試した"
date: 2023-03-18
slug: "Go の Live Reload を実行してくれる air というツールを試した"
category: blog
tags: [Go,Tech]
---
<h2 id="はじめに">はじめに</h2>

<p>書籍 <a href="https://www.amazon.co.jp/%E8%A9%B3%E8%A7%A3Go%E8%A8%80%E8%AA%9EWeb%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E9%96%8B%E7%99%BA-%E6%B8%85%E6%B0%B4-%E9%99%BD%E4%B8%80%E9%83%8E/dp/4863543727">詳解Go言語Webアプリケーション開発</a> を読んでいたら <a class="keyword" href="https://d.hatena.ne.jp/keyword/air">air</a> というLive Reload のツールが登場しました。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Fcosmtrek%2Fair%2F" title="GitHub - cosmtrek/air: ☁️ Live reload for Go apps" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://github.com/cosmtrek/air/">github.com</a></cite></p>

<p>書籍ではハンズオン形式で触ってみただけだったので、もう少し触ってみました。</p>

<h2 id="air-を使う"><a class="keyword" href="https://d.hatena.ne.jp/keyword/air">air</a> を使う</h2>

<h3 id="インストール">インストール</h3>

<p><code>go instal</code> で <code>air</code> のバイナリをインストールします。</p>

<pre class="code shell" data-lang="shell" data-unlink>% go install github.com/cosmtrek/air@latest</pre>


<p><code>GOPATH/bin</code> 配下にインストールされました。</p>

<pre class="code shell" data-lang="shell" data-unlink>% go env GOPATH
/home/dshimizu/go

% which air
/home/dshimizu/go/bin/air</pre>


<p>ヘルプを見てみます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% air -h
Usage of air:

If no command is provided air will start the runner with the provided flags

Commands:
  init  creates a .air.toml file with default settings to the current directory

Flags:
  -build.args_bin string
         (default &#34;DEFAULT&#34;)
  -build.bin string
         (default &#34;DEFAULT&#34;)
  -build.cmd string
         (default &#34;DEFAULT&#34;)
  -build.delay string
         (default &#34;DEFAULT&#34;)
  -build.exclude_dir string
         (default &#34;DEFAULT&#34;)
  -build.exclude_file string
         (default &#34;DEFAULT&#34;)
  -build.exclude_regex string
         (default &#34;DEFAULT&#34;)
  -build.exclude_unchanged string
         (default &#34;DEFAULT&#34;)
  -build.follow_symlink string
         (default &#34;DEFAULT&#34;)
  -build.full_bin string
         (default &#34;DEFAULT&#34;)
  -build.include_dir string
         (default &#34;DEFAULT&#34;)
  -build.include_ext string
         (default &#34;DEFAULT&#34;)
  -build.include_file string
         (default &#34;DEFAULT&#34;)
  -build.kill_delay string
         (default &#34;DEFAULT&#34;)
  -build.log string
         (default &#34;DEFAULT&#34;)
  -build.rerun string
         (default &#34;DEFAULT&#34;)
  -build.rerun_delay string
         (default &#34;DEFAULT&#34;)
  -build.send_interrupt string
         (default &#34;DEFAULT&#34;)
  -build.stop_on_error string
         (default &#34;DEFAULT&#34;)
  -c string
        config path
  -color.app string
         (default &#34;DEFAULT&#34;)
  -color.build string
         (default &#34;DEFAULT&#34;)
  -color.main string
         (default &#34;DEFAULT&#34;)
  -color.runner string
         (default &#34;DEFAULT&#34;)
  -color.watcher string
         (default &#34;DEFAULT&#34;)
  -d    debug mode
  -log.main_only string
         (default &#34;DEFAULT&#34;)
  -log.time string
         (default &#34;DEFAULT&#34;)
  -misc.clean_on_exit string
         (default &#34;DEFAULT&#34;)
  -root string
         (default &#34;DEFAULT&#34;)
  -screen.clear_on_rebuild string
         (default &#34;DEFAULT&#34;)
  -screen.keep_scroll string
         (default &#34;DEFAULT&#34;)
  -testdata_dir string
         (default &#34;DEFAULT&#34;)
  -tmp_dir string
         (default &#34;DEFAULT&#34;)
  -v    show version</pre>


<p>たくさんのオプションがありました。</p>

<p><code>-v</code> でバージョンが出てくるかと思いましたが出てきませんが一旦スルーします。</p>

<pre class="code shell" data-lang="shell" data-unlink>% air -v

  __    _   ___
 / /\  | | | |_)
/_/--\ |_| |_| \_ , built with Go
</pre>


<h3 id="起動してみる">起動してみる</h3>

<p>適当にプロジェクト<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成します。</p>

<pre class="code shell" data-lang="shell" data-unlink>% mkdir workspace/air-sample

% go mod init air-sample</pre>


<p>適当に以下のような <code>main.go</code> を作成します。</p>

<pre class="code lang-go" data-lang="go" data-unlink><span class="synStatement">package</span> main

<span class="synStatement">import</span> (
    <span class="synConstant">&quot;net/http&quot;</span>
)

<span class="synStatement">func</span> main() {
    server := http.Server{
        Addr:    <span class="synConstant">&quot;127.0.0.1:8080&quot;</span>,
        Handler: <span class="synStatement">nil</span>,
    }
    server.ListenAndServe()
}
</pre>


<p><code>air</code> コマンドを実行してみます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% air

  __    _   ___
 / /\  | | | |_)
/_/--\ |_| |_| \_ , built with Go

watching .
!exclude tmp
building...
running...</pre>


<p>正常に実行できました。
この時に、上述で作成した <code>main.go</code> がビルドされて起動します。
デフォルトだと、プロジェクト<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ配下の <code>tmp/</code> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ(今回だと <code>ari-sample/tmp</code>)にバイナリが配置されて、これが実行されます。</p>

<p>下記のような感じでプロセスが起動されていることが確認できます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% ps auxf | grep main
dshimizu 1467716  0.0  0.0   4504   712 pts/0    S+   07:36   0:00  |           \_ grep --color=auto main
dshimizu 1467699  0.0  0.0   2484   516 pts/4    Ss+  07:36   0:00                  \_ /bin/sh -c /home/dshimizu/workspcace/air-sample/tmp/main
dshimizu 1467700  0.0  0.1 1084628 4972 pts/4    Sl+  07:36   0:00                      \_ /home/dshimizu/workspcace/air-sample/tmp/main</pre>


<p><code>air</code> コマンドを実行しているターミナルはこのままにして、並行して <code>main.go</code> を、下記のような形で更新してみます。</p>

<pre class="code lang-go" data-lang="go" data-unlink><span class="synStatement">package</span> main

<span class="synStatement">import</span> (
    <span class="synConstant">&quot;net/http&quot;</span>
)

<span class="synStatement">type</span> HelloHandler <span class="synStatement">struct</span>{}

<span class="synStatement">func</span> (h HelloHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    w.Write([]<span class="synType">byte</span>(<span class="synConstant">&quot;Hello World&quot;</span>))
}

<span class="synStatement">func</span> main() {
    hello := HelloHandler{}

    server := http.Server{
        Addr:    <span class="synConstant">&quot;127.0.0.1:8080&quot;</span>,
        Handler: <span class="synStatement">nil</span>,
    }

    http.Handle(<span class="synConstant">&quot;/hello&quot;</span>, hello)

    server.ListenAndServe()
}
</pre>


<p>この時、 <code>air</code> コマンドを実行しているターミナルで、ファイルを変更して保存するたびに、自動でビルドし直されます。
その際、ファイルが更新されるたびに下記のような出力がなされます。ファイルに文法エラー等があるとエラーが出力されますが、正常な状態になれば、 <code>main.go has changed</code>,<br/>
<code>building...</code>, <code>running...</code> という出力になり、最新の状態で自動で起動し直されていることが確認できます。</p>

<pre class="code shell" data-lang="shell" data-unlink>main.go has changed
building...
# ari-sample
./main.go:8:11: undefined: HelloHandler
failed to build, error: exit status 1
:
main.go has changed
building...
running...</pre>


<p>プロセスが再起動されていることが確認できます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% ps auxf | grep main
dshimizu 1467716  0.0  0.0   4504   712 pts/0    S+   07:39   0:00  |           \_ grep --color=auto main
dshimizu 1467699  0.0  0.0   2484   516 pts/4    Ss+  07:39   0:00                  \_ /bin/sh -c /home/dshimizu/workspcace/air-sample/tmp/main
dshimizu 1467700  0.0  0.1 1084628 4972 pts/4    Sl+  07:39   0:00                      \_ /home/dshimizu/workspcace/air-sample/tmp/main</pre>


<h3 id="設定ファイルを作成変更してみる">設定ファイルを作成/変更してみる</h3>

<p>プロジェクト直下に <code>.air.toml</code> ファイルを配置して、デフォルトの挙動を変更できます。</p>

<p>下記のコマンドで、<code>.air.toml</code>  を生成できます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% air init</pre>


<p>v1.42 の <a class="keyword" href="https://d.hatena.ne.jp/keyword/air">air</a> だと、下記のような内容のものが生成されます。
<code>main.go</code> の配置先等に応じて <code>cmd</code> や <code>bin</code> あたりを変えたり、除外したいもの、含めたいものに応じて <code>exclude_xxx</code>, <code>include_xxx</code> あたりを変更することになりそうです。</p>

<pre class="code lang-config" data-lang="config" data-unlink>root <span class="synStatement">=</span> <span class="synConstant">&quot;.&quot;</span>
testdata_dir <span class="synStatement">=</span> <span class="synConstant">&quot;testdata&quot;</span>
tmp_dir <span class="synStatement">=</span> <span class="synConstant">&quot;tmp&quot;</span>

<span class="synSpecial">[</span>build<span class="synSpecial">]</span>
  args_bin <span class="synStatement">=</span> <span class="synSpecial">[]</span>
  bin <span class="synStatement">=</span> <span class="synConstant">&quot;./tmp/main&quot;</span>
  cmd <span class="synStatement">=</span> <span class="synConstant">&quot;go build -o ./tmp/main .&quot;</span>
  delay <span class="synStatement">=</span> <span class="synConstant">0</span>
  exclude_dir <span class="synStatement">=</span> <span class="synSpecial">[</span><span class="synConstant">&quot;assets&quot;</span><span class="synSpecial">,</span> <span class="synConstant">&quot;tmp&quot;</span><span class="synSpecial">,</span> <span class="synConstant">&quot;vendor&quot;</span><span class="synSpecial">,</span> <span class="synConstant">&quot;testdata&quot;</span><span class="synSpecial">]</span>
  exclude_file <span class="synStatement">=</span> <span class="synSpecial">[]</span>
  exclude_regex <span class="synStatement">=</span> <span class="synSpecial">[</span><span class="synConstant">&quot;_test.go&quot;</span><span class="synSpecial">]</span>
  exclude_unchanged <span class="synStatement">=</span> false
  follow_symlink <span class="synStatement">=</span> false
  full_bin <span class="synStatement">=</span> <span class="synConstant">&quot;&quot;</span>
  include_dir <span class="synStatement">=</span> <span class="synSpecial">[]</span>
  include_ext <span class="synStatement">=</span> <span class="synSpecial">[</span><span class="synConstant">&quot;go&quot;</span><span class="synSpecial">,</span> <span class="synConstant">&quot;tpl&quot;</span><span class="synSpecial">,</span> <span class="synConstant">&quot;tmpl&quot;</span><span class="synSpecial">,</span> <span class="synConstant">&quot;html&quot;</span><span class="synSpecial">]</span>
  include_file <span class="synStatement">=</span> <span class="synSpecial">[]</span>
  kill_delay <span class="synStatement">=</span> <span class="synConstant">&quot;0s&quot;</span>
  log <span class="synStatement">=</span> <span class="synConstant">&quot;build-errors.log&quot;</span>
  rerun <span class="synStatement">=</span> false
  rerun_delay <span class="synStatement">=</span> <span class="synConstant">500</span>
  send_interrupt <span class="synStatement">=</span> false
  stop_on_error <span class="synStatement">=</span> false

<span class="synSpecial">[</span>color<span class="synSpecial">]</span>
  app <span class="synStatement">=</span> <span class="synConstant">&quot;&quot;</span>
  build <span class="synStatement">=</span> <span class="synConstant">&quot;yellow&quot;</span>
  main <span class="synStatement">=</span> <span class="synConstant">&quot;magenta&quot;</span>
  runner <span class="synStatement">=</span> <span class="synConstant">&quot;green&quot;</span>
  watcher <span class="synStatement">=</span> <span class="synConstant">&quot;cyan&quot;</span>

<span class="synSpecial">[</span>log<span class="synSpecial">]</span>
  main_only <span class="synStatement">=</span> false
  time <span class="synStatement">=</span> false

<span class="synSpecial">[</span>misc<span class="synSpecial">]</span>
  clean_on_exit <span class="synStatement">=</span> false

<span class="synSpecial">[</span>screen<span class="synSpecial">]</span>
  clear_on_rebuild <span class="synStatement">=</span> false
  keep_scroll <span class="synStatement">=</span> true
</pre>


<p>試しに <code>go build</code> せずに <code>go run</code> で動くかをやってみました。</p>

<pre class="code lang-diff" data-lang="diff" data-unlink><span class="synStatement">@@ -4,8 +4,9 @@</span>
 [build]
   args_bin = []
<span class="synSpecial">-  bin = &quot;./tmp/main&quot;</span>
<span class="synSpecial">-  cmd = &quot;go build -o ./tmp/main .&quot;</span>
<span class="synIdentifier">+  #bin = &quot;./tmp/main&quot;</span>
<span class="synIdentifier">+  #cmd = &quot;go build -o ./tmp/main .&quot;</span>
<span class="synIdentifier">+  cmd = &quot;go run main.go&quot;</span>
   delay = 0
   exclude_dir = [&quot;assets&quot;, &quot;tmp&quot;, &quot;vendor&quot;, &quot;testdata&quot;]
   exclude_file = []
</pre>


<p><code>running...</code> の出力が出ませんでしたが、プログラムは起動しているようでした。
なお、デフォルトで <code>.air.toml</code> を読み込んでくれるようですが、 <code>air -c ファイル名</code> のコマンドでファイル名を指定することもできるようです。</p>

<pre class="code shell" data-lang="shell" data-unlink>% air

  __    _   ___
 / /\  | | | |_)
/_/--\ |_| |_| \_ , built with Go

watching .
!exclude tmp
building...</pre>


<p>プロセスは下記のような状態でした。</p>

<pre class="code shell" data-lang="shell" data-unlink>% ps auxf | grep main
dshimizu 1468191  0.0  0.0   2484   572 pts/2    Ss+  08:07   0:00  |               \_ /bin/sh -c go run main.go
dshimizu 1468192  0.0  0.6 1240428 26156 pts/2   Sl+  08:07   0:00  |                   \_ go run main.go
dshimizu 1468231  0.0  0.3 1010640 13216 pts/2   Sl+  08:07   0:00  |                       \_ /tmp/go-build692725095/b001/exe/main
dshimizu 1469281  0.0  0.0   4504   644 pts/3    S+   08:14   0:00              \_ grep --color=auto main</pre>


<p><code>main.go</code> を変更してみました。</p>

<pre class="code lang-go" data-lang="go" data-unlink><span class="synStatement">package</span> main

<span class="synStatement">import</span> (
    <span class="synConstant">&quot;net/http&quot;</span>
)

<span class="synStatement">type</span> HelloHandler <span class="synStatement">struct</span>{}

<span class="synStatement">func</span> (h HelloHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    w.Write([]<span class="synType">byte</span>(<span class="synConstant">&quot;Hello World!!&quot;</span>))  <span class="synComment">// !! をつけた</span>
}

<span class="synStatement">func</span> main() {
    hello := HelloHandler{}

    server := http.Server{
        Addr:    <span class="synConstant">&quot;127.0.0.1:8080&quot;</span>,
        Handler: <span class="synStatement">nil</span>,
    }

    http.Handle(<span class="synConstant">&quot;/hello&quot;</span>, hello)

    server.ListenAndServe()
}
</pre>


<p>ファイルの更新は検知してくれたようで、ここでは <code>building...</code>, <code>running...</code> と出力はされました。</p>

<pre class="code shell" data-lang="shell" data-unlink>% air

  __    _   ___
 / /\  | | | |_)
/_/--\ |_| |_| \_ , built with Go

watching .
!exclude tmp
building...


main.go has changed
main.go has changed
building...
running...</pre>


<p>しかしプロセスの状態は変更ないようでした。</p>

<pre class="code shell" data-lang="shell" data-unlink>% ps auxf | grep main
dshimizu 1468191  0.0  0.0   2484   572 pts/2    Ss+  08:07   0:00  |               \_ /bin/sh -c go run main.go
dshimizu 1468192  0.0  0.6 1240428 26156 pts/2   Sl+  08:07   0:00  |                   \_ go run main.go
dshimizu 1468231  0.0  0.3 1010640 13216 pts/2   Sl+  08:07   0:00  |                       \_ /tmp/go-build692725095/b001/exe/main
dshimizu 1469281  0.0  0.0   4504   644 pts/3    S+   08:14   0:00              \_ grep --color=auto main</pre>


<p>リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを投げてみましたが、 <code>Hello World!!</code> とはなっていませんでした。<code>go run</code> の場合は Live Reload はされてないようです。</p>

<pre class="code shell" data-lang="shell" data-unlink>% curl localhost:8080/hello
Hello World%</pre>


<h2 id="まとめ">まとめ</h2>

<ul>
<li>Go の Live Reload を実行してくれる <a class="keyword" href="https://d.hatena.ne.jp/keyword/air">air</a> を使ってみました</li>
<li><a class="keyword" href="https://d.hatena.ne.jp/keyword/air">air</a> コマンド実行時に、アプリケーションをビルドして実行し、ファイルの更新が発生した場合に自動で検知してアプリケーションが再起動されることが確認できました</li>
<li>設定ファイルは <code>.air.toml</code> で、デフォルトではこのファイルを読み込んでくれるようでした</li>
<li>リロード時には <code>go build</code> されるようなので、これができる必要があるようでした</li>
</ul>


<h2 id="参考">参考</h2>

<ul>
<li><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Fcosmtrek%2Fair%2F" title="GitHub - cosmtrek/air: ☁️ Live reload for Go apps" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><a href="https://github.com/cosmtrek/air/">GitHub - cosmtrek/air: &#x2601;&#xFE0F; Live reload for Go apps</a></li>
<li><a href="https://www.amazon.co.jp/%E8%A9%B3%E8%A7%A3Go%E8%A8%80%E8%AA%9EWeb%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E9%96%8B%E7%99%BA-%E6%B8%85%E6%B0%B4%E9%99%BD%E4%B8%80%E9%83%8E-ebook/dp/B0B62K55SL">https://www.amazon.co.jp/%E8%A9%B3%E8%A7%A3Go%E8%A8%80%E8%AA%9EWeb%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E9%96%8B%E7%99%BA-%E6%B8%85%E6%B0%B4%E9%99%BD%E4%B8%80%E9%83%8E-ebook/dp/B0B62K55SL</a></li>
</ul>


