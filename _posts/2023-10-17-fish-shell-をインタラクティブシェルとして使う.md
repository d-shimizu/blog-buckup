---
title: "fish shell をインタラクティブシェルとして使う"
date: 2023-10-17
slug: "fish shell をインタラクティブシェルとして使う"
category: blog
tags: [macOS,fish shell]
---
<h2 id="はじめに">はじめに</h2>

<p>数年前に fish というシェルがあるのを知ってそのままでしたが、また最近耳にしたので少し触ってみながら、<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%E9%A5%AF%A5%C6%A5%A3%A5%D6">インタラクティブ</a>シェルとして使うよう設定してみました。</p>

<h2 id="fish-とは">fish とは</h2>

<p>fish は friendly interactive shell の略らしく、シェルの一つです。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Ffishshell.com%2F" title="fish shell" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://fishshell.com/">fishshell.com</a></cite></p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Ffish-shell%2Ffish-shell" title="GitHub - fish-shell/fish-shell: The user-friendly command line shell." class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://github.com/fish-shell/fish-shell">github.com</a></cite></p>

<h2 id="試す">試す</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/macOS">macOS</a> の環境にインストールしてみます。</p>

<h3 id="環境">環境</h3>

<pre class="code shell" data-lang="shell" data-unlink>% sw_vers
ProductName:        macOS
ProductVersion:     13.6
BuildVersion:       22G120</pre>


<h3 id="インストール">インストール</h3>

<p>ドキュメントにある通り、Homebrew でインストールできます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% brew install fish</pre>


<h3 id="設定ファイルを見る">設定ファイルを見る</h3>

<p>設定ファイルは下記にあるようです。<code>.bottle</code> は Homebrew のものなので無視して他のファイルを見てみます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% find / -type f -name config.fish -not -path &#39;/System/Volumes/Data/*&#39;  2&gt; /dev/null
/usr/local/etc/fish/config.fish
/usr/local/Cellar/fish/3.6.1/.bottle/etc/fish/config.fish
/usr/local/Cellar/fish/3.6.1/share/fish/config.fish
/usr/local/share/fish/config.fish
/Users/dshimizu/.config/fish/config.fish</pre>


<p><code>/usr/local/etc/fish/config.fish</code>の中身は初期時点では全て<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B3%A5%E1%A5%F3%A5%C8%A5%A2%A5%A6%A5%C8">コメントアウト</a>状態のようでした。
グローバルな設定をこのファイルか、 <code>/usr/local/etc/fish/conf.d</code> 配下に配置するようです。</p>

<pre class="code shell" data-lang="shell" data-unlink>% cat /usr/local/etc/fish/config.fish
# Put system-wide fish configuration entries here
# or in .fish files in conf.d/
# Files in conf.d can be overridden by the user
# by files with the same name in $XDG_CONFIG_HOME/fish/conf.d

# This file is run by all fish instances.
# To include configuration only for login shells, use
# if status is-login
#    ...
# end
# To include configuration only for interactive shells, use
# if status is-interactive
#   ...
# end</pre>


<p><code>/usr/local/share/fish/config.fish</code> は <code>/usr/local/Cellar/fish/3.6.1/share/fish/config.fish</code> の<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B7%A5%F3%A5%DC%A5%EA%A5%C3%A5%AF%A5%EA%A5%F3%A5%AF">シンボリックリンク</a>でした。</p>

<pre class="code shell" data-lang="shell" data-unlink>% ls -l /usr/local/share/fish/config.fish
lrwxr-xr-x  1 dshimizu  admin  46 10 17 01:02 /usr/local/share/fish/config.fish -&gt; ../../Cellar/fish/3.6.1/share/fish/config.fish</pre>


<p><code>/usr/local/Cellar/fish/3.6.1/share/fish/config.fish</code> を見ると、色々処理が書かれてました。ただ、このファイルの冒頭に、このファイルの削除や編集は推奨しない、とありました。</p>

<pre class="code shell" data-lang="shell" data-unlink>% cat /usr/local/Cellar/fish/3.6.1/share/fish/config.fish
# This file does some internal fish setup.
# It is not recommended to remove or edit it.
#
# Set default field separators
#
 :
 :</pre>


<p><code>~/.config/fish/config.fish</code> は、以下のような状態でした。</p>

<p><code>~/.config/fish/</code> 以下に、ユーザー個別の設定を配置していくようです。</p>

<pre class="code shell" data-lang="shell" data-unlink>% cat ~/.config/fish/config.fish
if status is-interactive
    # Commands to run in interactive sessions can go here
end</pre>


<h3 id="使ってみる">使ってみる</h3>

<p>fish を起動してみます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% fish
Welcome to fish, the friendly interactive shell
Type help for instructions on how to use fish
dshimizu@MacBook-Pro ~&gt;</pre>


<p>上記では表現できてませんが、デフォルトで色がついています。
また、コマンド入力をしていると、補完やハイライトがかなり良い感じで出てきます。</p>

<h3 id="GUI-設定"><a class="keyword" href="https://d.hatena.ne.jp/keyword/GUI">GUI</a> 設定</h3>

<p><code>fish_config</code> コマンドを実行すると、<a class="keyword" href="https://d.hatena.ne.jp/keyword/GUI">GUI</a> の画面が起動します。</p>

<pre class="code shell" data-lang="shell" data-unlink>dshimizu@MacBook-Pro ~&gt; fish_config</pre>


<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20231028/20231028021902.png" width="1200" height="621" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<p>この時、ブラウザが起動して <code>http://localhost:8000/**********</code> といったURLでリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トが飛びますが、ブラウザが <a class="keyword" href="https://d.hatena.ne.jp/keyword/https">https</a> で処理しようとすると <code>ERR_SSL_PROTOCOL_ERROR</code> というエラーになるので、 その場合は http でアクセスし直すと画面が表示されます。</p>

<h2 id="fish-のインタラクティブシェル設定">fish の<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%E9%A5%AF%A5%C6%A5%A3%A5%D6">インタラクティブ</a>シェル設定</h2>

<p>fish は <a class="keyword" href="https://d.hatena.ne.jp/keyword/POSIX">POSIX</a> 非互換のシェルであるようで、デフォルトのシェルとして扱うのは少々手間が多いかもしれないので、普段使いの<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%E9%A5%AF%A5%C6%A5%A3%A5%D6">インタラクティブ</a>シェルでのみ実行することにします。</p>

<p>ターミナル起動時に fish を実行するようにするには、多分 <code>.zshrc</code> に以下のような記述をすることでいけると思います。</p>

<pre class="code lang-zsh" data-lang="zsh" data-unlink><span class="synStatement">if</span> [[ -z <span class="synPreProc">${BASH_EXECUTION_STRING}</span> <span class="synStatement">||</span> -z <span class="synPreProc">${ZSH_EXECUTION_STRING}</span> &amp;&amp; -t <span class="synConstant">1</span> ]]
<span class="synStatement">then</span>
    <span class="synStatement">exec</span> fish
<span class="synStatement">fi</span>
</pre>


<h2 id="まとめ">まとめ</h2>

<p>fish シェルの初期設定ファイルを眺めつつ、<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%E9%A5%AF%A5%C6%A5%A3%A5%D6">インタラクティブ</a>シェルとして使うようにしてみました。
まだ自分に合った設定は見えておらず、また、<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>等あるようなのでちょっとずつ使っていければと思います。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://fishshell.com/">fish shell</a></li>
<li><a href="https://github.com/fish-shell/fish-shell">GitHub - fish-shell/fish-shell: The user-friendly command line shell.</a></li>
<li><a href="https://wiki.archlinux.jp/index.php/Fish">fish - ArchWiki</a></li>
<li><a href="https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html">Shell Command Language</a></li>
<li><a href="https://news.mynavi.jp/techplus/article/techp2695/">&#x306B;&#x308F;&#x304B;&#x7BA1;&#x7406;&#x8005;&#x306E;&#x305F;&#x3081;&#x306E;Linux&#x904B;&#x7528;&#x5165;&#x9580;(73) &#x4FBF;&#x5229;&#x30B7;&#x30A7;&#x30EB;&#x300C;fish&#x300D;&#x3092;&#x4F7F;&#x3046;&#xFF08;&#x305D;&#x306E;4&#xFF09; | TECH+&#xFF08;&#x30C6;&#x30C3;&#x30AF;&#x30D7;&#x30E9;&#x30B9;&#xFF09;</a></li>
<li><a href="https://unix.stackexchange.com/questions/324951/load-in-environment-variables-when-using-sudo">bash - Load in environment variables when using sudo - Unix &amp; Linux Stack Exchange</a></li>
<li><a href="https://unix.stackexchange.com/questions/389495/what-does-t-1-check">bash - What does [ -t 1 ] check? - Unix &amp; Linux Stack Exchange</a></li>
</ul>


