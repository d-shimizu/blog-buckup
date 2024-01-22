---
title: "Debian 11 へ Neovim をインストールして、 dein.vim の plugin manager を使って Go の language server(gopls) を利用できるようにするためのメモ"
date: 2023-04-01
slug: "Debian 11 へ Neovim をインストールして、 dein.vim の plugin manager を使って Go の language server(gopls) を利用できるようにするためのメモ"
category: blog
tags: [Tech,Neovim,Debian]
---
<h2 id="はじめに">はじめに</h2>

<p>Neovim をインストールして、 dein.<a class="keyword" href="https://d.hatena.ne.jp/keyword/vim">vim</a> の plugin manager を使って Go の language server(gopls) を利用できるようにするのに結構手間取ったので、設定方法をメモしておきます。</p>

<h2 id="目次">目次</h2>

<ul class="table-of-contents">
    <li><a href="#はじめに">はじめに</a></li>
    <li><a href="#目次">目次</a></li>
    <li><a href="#設定手順">設定手順</a><ul>
            <li><a href="#環境">環境</a></li>
            <li><a href="#gopls-のインストール">gopls のインストール</a></li>
            <li><a href="#Neovim-のインストール">Neovim のインストール</a></li>
            <li><a href="#deinvim-のインストール">dein.vim のインストール</a></li>
            <li><a href="#deinvim-の設定">dein.vim の設定</a></li>
            <li><a href="#発生したエラーとその対処">発生したエラーとその対処</a><ul>
                    <li><a href="#1つ目">1つ目</a></li>
                    <li><a href="#2-つ目">2 つ目</a></li>
                </ul>
            </li>
            <li><a href="#確認">確認</a></li>
        </ul>
    </li>
    <li><a href="#まとめ">まとめ</a></li>
</ul>

<h2 id="設定手順">設定手順</h2>

<h3 id="環境">環境</h3>

<pre class="code shell" data-lang="shell" data-unlink>% uname -svo
Linux #1 SMP Debian 5.10.162-1 (2023-01-21) GNU/Linux</pre>




<pre class="code shell" data-lang="shell" data-unlink>$ go version
go version go1.20 linux/amd64</pre>


<h3 id="gopls-のインストール">gopls のインストール</h3>

<p><code>gopls</code> をインストールします。</p>

<pre class="code shell" data-lang="shell" data-unlink>$ go install golang.org/x/tools/gopls@latest</pre>


<p><code>$GOPATH/bin</code> 配下にインストールされます。
ここでは、 <code>$HOME/$GOPATH/bin</code> となっています。</p>

<pre class="code shell" data-lang="shell" data-unlink>$ ls  ~/go/bin/gopls
/home/dshimizu/go/bin/gopls</pre>


<h3 id="Neovim-のインストール">Neovim のインストール</h3>

<p>Neovim をインストールします。
やり方はいくつかあると思いますが、ここでは <a class="keyword" href="https://d.hatena.ne.jp/keyword/GitHub">GitHub</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>から取得して、 <code>$HOME/.local/neovim</code> にインストールします。</p>

<pre class="code shell" data-lang="shell" data-unlink>$ git clone https://github.com/neovim/neovim

$ cd neovim

$ make CMAKE_EXTRA_FLAGS=&#34;-DCMAKE_INSTALL_PREFIX=$HOME/.local/neovim&#34; CMAKE_BUILD_TYPE=Release

$ make install</pre>


<p>以下のような感じでファイルが配置されます。</p>

<pre class="code shell" data-lang="shell" data-unlink>$ tree -L 1 .local/neovim/
.local/neovim/
├── BACKERS.md
├── BSDmakefile
├── CMakeLists.txt
├── CMakePresets.json
├── CONTRIBUTING.md
├── LICENSE.txt
├── MAINTAIN.md
├── Makefile
├── README.md
├── bin
├── build
├── cmake
├── cmake.config
├── cmake.deps
├── cmake.packaging
├── contrib
├── lib
├── runtime
├── scripts
├── share
├── snap
├── src
└── test

14 directories, 9 files</pre>


<p>パスを通します。</p>

<pre class="code shell" data-lang="shell" data-unlink>export PATH=$PATH:$HOME/.local/neovim/bin</pre>


<p>パスが通っていることを確認します。</p>

<pre class="code shell" data-lang="shell" data-unlink>$ nvim -v
NVIM v0.10.0-dev-102+g85bc9e897
Build type: Release
LuaJIT 2.1.0-beta3

      システム vimrc: &#34;$VIM/sysinit.vim&#34;
       省略時の $VIM: &#34;/home/dshimizu/.local/neovim/share/nvim&#34;

Run :checkhealth for more info</pre>


<h3 id="deinvim-のインストール">dein.<a class="keyword" href="https://d.hatena.ne.jp/keyword/vim">vim</a> のインストール</h3>

<p>これもやり方はいくつかあると思うけど、ここではインストール<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>を使って <code>~/.config/nvim/dein</code> 配下に インストールします。</p>

<pre class="code shell" data-lang="shell" data-unlink>$ curl https://raw.githubusercontent.com/Shougo/dein-installer.vim/main/installer.sh &gt; installer.sh

$ ./installer.sh ~/.config/nvim/dein</pre>


<p>以下のような出力がされますので、 <code>2 neovim path (~/.config/nvim/init.vim)</code> を選択するため、 <code>2</code> を入力します。</p>

<pre class="code shell" data-lang="shell" data-unlink>[ CONFIG LOCATION ]

1 vim path (~/.vimrc)
2 neovim path (~/.config/nvim/init.vim)

Select your editor config location (eg. 1 or 2)
2</pre>


<p>以下のように <a class="keyword" href="https://d.hatena.ne.jp/keyword/GitHub">GitHub</a> からソースを取得されて配置されるようなログが出ます。
<code>~/.config/nvim/init.vim</code> がある場合は、バックアップされて、<code>dein</code> 用のものが配置されるようです。</p>

<pre class="code log" data-lang="log" data-unlink>Cloning Dein.vim into &#39;/home/dshimizu/.config/nvim/dein/repos/github.com/Shougo/dein.vim&#39;...
remote: Enumerating objects: 66, done.
remote: Counting objects: 100% (66/66), done.
remote: Compressing objects: 100% (55/55), done.
remote: Total 66 (delta 2), reused 30 (delta 0), pack-reused 0
Unpacking objects: 100% (66/66), 80.78 KiB | 3.85 MiB/s, done.
From https://github.com/Shougo/dein.vim
 * branch            master     -&gt; FETCH_HEAD
 * [new branch]      master     -&gt; origin/master
Looking for an existing &#39;init.vim&#39; config...
Found init.vim. Creating backup in &#39;/home/dshimizu/.config/nvim/init.vim.pre-dein-vim&#39;...
Using the Dein.vim config example and adding it to &#39;/home/dshimizu/.config/nvim/init.vim&#39;...
_______________________________________________________________

 #######  ######## ### ###  ###       ###  ### ### ###########
 ##!  ### ##!      ##! ##!#!###       ##!  ### ##! ##! ##! ##!
 #!#  !#! #!!!:!   !!# #!##!!#!       #!#  !#! !!# #!! !#! #!#
 !!:  !!! !!:      !!: !!:  !!!        !:..:!  !!: !!:     !!:
 ::::::   :::::::: ::: :::   ::   ::     ::    ::: :::     :::

              by Shougo • MIT License • v3.0
_______________________________________________________________

All done. Look at your init.vim file to set plugins, themes, and more.

● Documentation: /home/dshimizu/.config/nvim/dein/repos/github.com/Shougo/dein.vim/doc/dein.txt
● Chat with the community: https://gitter.im/Shougo/dein.vim
● Report issues: https://github.com/Shougo/dein.vim/issues
</pre>


<p>これで、 <code>~/.config/nvim/dein</code> が作成されます。
また <code>init.vim</code> も配置されます。</p>

<pre class="code text" data-lang="text" data-unlink>/home/dshimizu/.config/nvim/
├── dein
├── init.vim
├── pack
└── plugins</pre>


<h3 id="deinvim-の設定">dein.<a class="keyword" href="https://d.hatena.ne.jp/keyword/vim">vim</a> の設定</h3>

<p><code>~/.config/nvim/dein/dein.toml</code> に以下の設定を追記します。</p>

<pre class="code text" data-lang="text" data-unlink>[[plugins]]
repo = &#39;neovim/nvim-lspconfig&#39;
hook_add = &#39;&#39;&#39;
lua &lt;&lt;EOF
  local lspconfig = require&#39;lspconfig&#39;
  lspconfig.gopls.setup{}
EOF
&#39;&#39;&#39;

[[plugins]]
repo = &#39;nvim-lua/completion-nvim&#39;</pre>


<p><code>~/.config/nvim/dein/dein_lazy.toml</code>を新規作成し、以下の設定を記述します。</p>

<pre class="code text" data-lang="text" data-unlink>[[plugins]]
repo = &#39;Shougo/neco-vim&#39;

[[plugins]]
repo = &#39;Shougo/denite.nvim&#39;

[[plugins]]
repo = &#39;Shougo/deoplete.nvim&#39;

[[plugins]]
repo = &#39;Shougo/neomru.vim&#39;</pre>


<p><code>~/.config/nvim/init.vim</code> に <code>call dein#load_toml('~/.config/nvim/dein/dein.toml', {'lazy': 0})</code>,  <code>call dein#load_toml('~/.config/nvim/dein/dein_lazy.toml', {'lazy': 1})</code> を追記します。
また、最後の <code>if~ call dein#install() ~ endif</code> の部分を<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B3%A5%E1%A5%F3%A5%C8%A5%A2%A5%A6%A5%C8">コメントアウト</a>をアンコメントして、plugin を自動でインストールできるようにしておきます。 それか Neovim 起動時に <code>:call dein#install()</code> を実行します。</p>

<pre class="code text" data-lang="text" data-unlink>&#34; Ward off unexpected things that your distro might have made, as
&#34; well as sanely reset options when re-sourcing .vimrc
set nocompatible

&#34; Set Dein base path (required)
let s:dein_base = &#39;/home/dshimizu/.config/nvim/dein&#39;

&#34; Set Dein source path (required)
let s:dein_src = &#39;/home/dshimizu/.config/nvim/dein/repos/github.com/Shougo/dein.vim&#39;

&#34; Set Dein runtime path (required)
execute &#39;set runtimepath+=&#39; . s:dein_src

&#34; Call Dein initialization (required)
call dein#begin(s:dein_base)

call dein#add(s:dein_src)

&#34; Your plugins go here:
&#34;call dein#add(&#39;Shougo/neosnippet.vim&#39;)
&#34;call dein#add(&#39;Shougo/neosnippet-snippets&#39;)
&#34;call dein#add(&#39;/home/dshimizu/.config/nvim/dein/dein.toml&#39;)
call dein#load_toml(&#39;~/.config/nvim/dein/dein.toml&#39;, {&#39;lazy&#39;: 0})
call dein#load_toml(&#39;~/.config/nvim/dein/dein_lazy.toml&#39;, {&#39;lazy&#39;: 1})

&#34; Finish Dein initialization (required)
call dein#end()

&#34; Attempt to determine the type of a file based on its name and possibly its
&#34; contents. Use this to allow intelligent auto-indenting for each filetype,
&#34; and for plugins that are filetype specific.
if has(&#39;filetype&#39;)
  filetype indent plugin on
endif

&#34; Enable syntax highlighting
if has(&#39;syntax&#39;)
  syntax on
endif

&#34; Uncomment if you want to install not-installed plugins on startup.
if dein#check_install()
  call dein#install()
endif</pre>


<h3 id="発生したエラーとその対処">発生したエラーとその対処</h3>

<h4 id="1つ目">1つ目</h4>

<p>以下のようなエラーが出ました。</p>

<pre class="code" data-lang="" data-unlink>Spawning language server with cmd: `gopls` failed. The language server is either not installed, missing from PATH, or not executable.</pre>


<p>gopls にパスが読めない場合に発生するので、パスが通っているか確認して、通っていない場合はパスを設定します。</p>

<h4 id="2-つ目">2 つ目</h4>

<p>Neovim を起動したときに以下のようなエラーが出ました。</p>

<pre class="code log" data-lang="log" data-unlink>[deoplete] deoplete requires Python3 support(&#34;+python3&#34;).
VimEnter Autocommands for &#34;*&#34;..function deoplete#enable[9]..deoplete#initialize[1]..deoplete#init#_initialize[15]..deoplete#init#_channel[59]..VimEnter Autocommands for &#34;*&#34;..function deoplete#enable[9]..deoplete#initialize[1]..deoplet
e#init#_initialize[15]..deoplete#init#_channel[42]..deoplete#init#_python_version_check の処理中にエラーが検出されました:
行    8:
E319: No &#34;python3&#34; provider found. Run &#34;:checkhealth provider&#34;</pre>


<p>Neovim の pip ライブラリが必要なようですのでインストールします。</p>

<pre class="code shell" data-lang="shell" data-unlink>$ pip3 install neovim</pre>


<p>neovim を起動し、以下を実行します。</p>

<pre class="code text" data-lang="text" data-unlink>:checkhealth provider</pre>


<p>以下を実行するようなメッセージが出力されたら、以下も実行します。</p>

<pre class="code text" data-lang="text" data-unlink>:UpdateRemotePlugins</pre>


<h3 id="確認">確認</h3>

<p>これで、Neovim で language server(gopls) を起動できるようになりました。
適当にコードをエラーになるコードを書いてみると、以下のように表示されます。</p>

<pre class="code lang-go" data-lang="go" data-unlink><span class="synStatement">package</span> main

<span class="synStatement">import</span> <span class="synConstant">&quot;fmt&quot;</span>

<span class="synStatement">func</span> main() {
        fmt.Printn(<span class="synConstant">&quot;Hello World&quot;</span>)     ■ undefined: fmt.Printn
}
</pre>


<h2 id="まとめ">まとめ</h2>

<p>Neovim をインストールして、 dein.<a class="keyword" href="https://d.hatena.ne.jp/keyword/vim">vim</a> の plugin manager を使って Go の language server(gopls) を利用できるようにするための設定がうまくできなかったのでその手順を記載しました。</p>

<ul>
<li>gopls のインストール</li>
<li>Neovim のインストール</li>
<li>dein.<a class="keyword" href="https://d.hatena.ne.jp/keyword/vim">vim</a> のインストール</li>
<li>dein.<a class="keyword" href="https://d.hatena.ne.jp/keyword/vim">vim</a> の設定/<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>のインストール</li>
<li>エラーの対処</li>
</ul>


