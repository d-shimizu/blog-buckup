---
title: "Neovim で「E382: 'buftype' オプションが設定されているので書き込めません」のメッセージが出たので調べた"
date: 2023-09-20
slug: "Neovim で「E382: 'buftype' オプションが設定されているので書き込めません」のメッセージが出たので調べた"
category: blog
tags: [Neovim]
---
<h2 id="はじめに">はじめに</h2>

<p>Neovimで <code>:checkhealth</code> コマンドとかを実行した時に開かれたファイルを保存しようとしたら、以下のエラーメッセージが出て保存できない状態に遭遇しました。</p>

<pre class="code terminal" data-lang="terminal" data-unlink>E382: &#39;buftype&#39; オプションが設定されているので書き込めません</pre>


<h2 id="バッファと-buftype">バッファと buftype</h2>

<p>バッファはメモリに保持されているファイルの内容であるとのことです。</p>

<ul>
<li><a href="https://vim-jp.org/vimdoc-ja/windows.html#special-buffers">windows - Vim&#x65E5;&#x672C;&#x8A9E;&#x30C9;&#x30AD;&#x30E5;&#x30E1;&#x30F3;&#x30C8;</a></li>
<li><a href="https://vim-jp.org/vimdoc-ja/options.html#'buftype'">options - Vim&#x65E5;&#x672C;&#x8A9E;&#x30C9;&#x30AD;&#x30E5;&#x30E1;&#x30F3;&#x30C8;</a></li>
<li><a href="https://vim-jp.org/vimdoc-ja/windows.html#special-buffers">windows - Vim&#x65E5;&#x672C;&#x8A9E;&#x30C9;&#x30AD;&#x30E5;&#x30E1;&#x30F3;&#x30C8;</a></li>
</ul>


<p>いろんな種類のバッファがあるようで、それが <code>buftype</code> に格納されるようです。
buftypは以下のコマンドで確認できます。</p>

<pre class="code terminal" data-lang="terminal" data-unlink>:set buftype?</pre>


<p>普通に <a class="keyword" href="https://d.hatena.ne.jp/keyword/Vim">Vim</a>/Neovim で開いたファイルの  <code>buftype</code> は空ですが、<code>:checkhealth</code> コマンドとかを実行した時に開かれたファイルは特殊なバッファに保持されるようです。
今回の場合は <code>nofile</code> となっていました。</p>

<p>ドキュメントによると以下とのことでした。</p>

<blockquote><p>nofile        ファイルと関連がなく、書き込まれる予定のないバッファ</p></blockquote>

<h2 id="保存するには">保存するには</h2>

<p>以下のコマンドでbuftypeを空にすれば普通のファイルとして扱えるようになり、保存可能になりました。</p>

<pre class="code terminal" data-lang="terminal" data-unlink>:set buftype=</pre>




<pre class="code terminal" data-lang="terminal" data-unlink>:saveas /tmp/hogehoge.txt</pre>


<h2 id="参考">参考</h2>

<ul>
<li><a href="https://vim-jp.org/vimdoc-ja/windows.html#special-buffers">windows - Vim&#x65E5;&#x672C;&#x8A9E;&#x30C9;&#x30AD;&#x30E5;&#x30E1;&#x30F3;&#x30C8;</a></li>
<li><a href="https://vim-jp.org/vimdoc-ja/options.html#'buftype'">options - Vim&#x65E5;&#x672C;&#x8A9E;&#x30C9;&#x30AD;&#x30E5;&#x30E1;&#x30F3;&#x30C8;</a></li>
<li><a href="https://vim-jp.org/vimdoc-ja/windows.html#special-buffers">windows - Vim&#x65E5;&#x672C;&#x8A9E;&#x30C9;&#x30AD;&#x30E5;&#x30E1;&#x30F3;&#x30C8;</a></li>
</ul>


