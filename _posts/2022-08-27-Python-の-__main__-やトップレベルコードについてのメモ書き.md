---
title: "Python の  __main__ やトップレベルコードについてのメモ書き"
date: 2022-08-27
slug: "Python の  __main__ やトップレベルコードについてのメモ書き"
category: blog
tags: [Tech,Python]
---
<h2 id="はじめに">はじめに</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> で出てくる <code>__main__</code> についてドキュメントを見ていて、「トッ<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%D7%A5%EC%A5%D9">プレベ</a>ルコード環境」というものが出てきて、あんまり詳しくわかっていなかったので気になったので調べたことのメモ書きです。</p>

<h2 id="__main__-について"><code>__main__</code> について</h2>

<p>ドキュメントを読むと、<code>__main__</code> は以下の2つのために使われる、と記載されていました。<a href="#f-442f27b9" id="fn-442f27b9" name="fn-442f27b9" title="https://docs.python.org/ja/3/library/main.html#module-main:title">*1</a></p>

<blockquote><ol>
<li>the name of the top-level environment of the program, which can be checked using the <strong>name</strong> == '<strong>main</strong>' expression; and</li>
<li>the <strong>main</strong>.py file in <a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> packages.</li>
</ol>
</blockquote>

<p><code>__name__ == '__main__'</code> を使用してトッ<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%D7%A5%EC%A5%D9">プレベ</a>ルの環境かどうか確認できるようにすることと、<a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> パッケージの <code>__main__.py</code> で利用するもの、とのことでした。</p>

<p>ちなみに先頭と末尾をアンダースコア <code>__</code> になっているものは、<a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> の中で予約されているもののようで、メインプログラムを表すものが <code>__main__</code> が利用されるようです。</p>

<h3 id="トップレベルと-__main__-変数">トッ<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%D7%A5%EC%A5%D9">プレベ</a>ルと <code>__main__</code> 変数</h3>

<p>例えば以下のような<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ構成だった場合には、 <code>main.py</code> に相当するものを起点に、同一階層のモジュールや、下位の階層の <code>mypackage</code> のモジュール類を読み込むような形で構成される形が多いと思います。</p>

<pre class="code" data-lang="" data-unlink>myproject/
├─ main.py
├─ hogemodule.py
├─ mypackage/
:   ├─ __init__.py
    ├─ __main__.py
    ├─ mymodule.py
    :</pre>


<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>を実行する場合、他の <a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>から import される場合と、何らかで直接 <code>.py</code> のプログラムを直接実行しる場合があるかと思いますが、直接実行する場合に指定されるモジュール(<code>python3 hogemodule.py</code>の場合は <code>hogemodule</code>)がトッ<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%D7%A5%EC%A5%D9">プレベ</a>ルコード<a href="#f-0a72068a" id="fn-0a72068a" name="fn-0a72068a" title="https://docs.python.org/ja/3/reference/toplevel_components.html:title">*2</a>ということになるようです。</p>

<p>ドキュメントによると、主に<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%D7%A5%EA%A5%BF">インタプリタ</a>で実行されるケースはトッ<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%D7%A5%EC%A5%D9">プレベ</a>ルコードでの実行ということになるらしいです<a href="#f-ca22455a" id="fn-ca22455a" name="fn-ca22455a" title="https://docs.python.org/ja/3/library/main.html#what-is-the-top-level-code-environment:title">*3</a>。</p>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> モジュールまたはパッケージがインポートされると、<code>__name__</code> という変数に <a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> モジュールの名前に設定されます。 通常、これは <code>.py</code> の拡張子を除いた <a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> ファイル自体の名前です。例えば <code>hello.py</code> の場合は <code>hello</code> という名前がモジュールとなり、<code>__name__</code> に設定されます。</p>

<p>ただし、モジュールがトッ<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%D7%A5%EC%A5%D9">プレベ</a>ル コード環境で実行される場合、 <code>__name__</code> 変数には、文字列 <code>__main__</code> が設定されます。</p>

<h4 id="Python3-のインタプリタを呼び出した時">Python3 の<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%D7%A5%EA%A5%BF">インタプリタ</a>を呼び出した時</h4>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%D7%A5%EA%A5%BF">インタプリタ</a>を呼び出して <code>__name__</code> を出力すると、値は <code>__main__</code> になっています。</p>

<pre class="code shell" data-lang="shell" data-unlink>% python3
Python 3.7.9 (v3.7.9:13c94747c7, Aug 15 2020, 01:31:08)
[Clang 6.0 (clang-600.0.57)] on darwin
Type &#34;help&#34;, &#34;copyright&#34;, &#34;credits&#34; or &#34;license&#34; for more information.
&gt;&gt;&gt; __name__
&#39;__main__&#39;</pre>


<h4 id="直接実行する場合の-Python-モジュール">直接実行する場合の <a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> モジュール</h4>

<p>適当に以下のような<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>を作成してみます。</p>

<pre class="code lang-python" data-lang="python" data-unlink><span class="synComment">#!/usr/local/bin/python3</span>

<span class="synIdentifier">print</span>(__name__)
<span class="synIdentifier">print</span>(<span class="synConstant">&quot;Hello World!!&quot;</span>)
</pre>


<p><code>ファイル名.py</code> でモジュールと見なされ、 モジュール名を<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B0%A5%ED%A1%BC%A5%D0%A5%EB%CA%D1%BF%F4">グローバル変数</a> <code>__name__</code> で取得できることが確認できます<a href="#f-8562bcf0" id="fn-8562bcf0" name="fn-8562bcf0" title="https://docs.python.org/ja/3/tutorial/modules.html:title">*4</a>
実行すると <code>__name__</code> の値は <code>__main__</code> になっています。</p>

<pre class="code shell" data-lang="shell" data-unlink>% python3 helloworld.py
__main__
Hello World!!</pre>


<h4 id="-m-オプションで実行される-Python-モジュールまたはパッケージ">-m オプションで実行される <a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> モジュールまたはパッケージ</h4>

<p>実行してみると、こちらも <code>__name__</code> の値は <code>__main__</code> になっています。</p>

<pre class="code shell" data-lang="shell" data-unlink>% python3 -m helloworld
__main__
Hello World!!</pre>


<h4 id="標準入力から-Python-インタプリタへ渡されたコード">標準入力から <a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%D7%A5%EA%A5%BF">インタプリタ</a>へ渡されたコード</h4>

<p>読み込んだモジュールはそのままモジュールとして扱われており、標準入力で渡したコード自体は <code>__main__</code> と判定されているようです。</p>

<pre class="code shell" data-lang="shell" data-unlink>% echo &#34;import helloworld \nprint(__name__)&#34; | python3
helloworld
Hello World!!
__main__</pre>


<h4 id="-c-オプションでのPythonインタプリタに渡されたコード">-c オプションでの<a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a><a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%D7%A5%EA%A5%BF">インタプリタ</a>に渡されたコード</h4>

<p>-c オプションの場合も同様に、読み込んだモジュールはそのままモジュールとして扱われており、標準入力で渡したコード自体は <code>__main__</code> と判定されているようです。</p>

<pre class="code shell" data-lang="shell" data-unlink>% python3 -c &#34;import helloworld
print(__name__)&#34;
helloworld
Hello World!!
__main__</pre>


<h3 id="まとめ">まとめ</h3>

<p>ドキュメントに書いてあることですが、<a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> の <code>__main__</code> に関して、以下のことを確認してみました。</p>

<ul>
<li>主に <a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%D7%A5%EA%A5%BF">インタプリタ</a>を利用する場合に指定されるモジュール( ex) <code>hogehoge.py</code>)がトッ<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%D7%A5%EC%A5%D9">プレベ</a>ルコードとなる</li>
<li><a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> モジュールは、ファイルの引数として <a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%D7%A5%EA%A5%BF">インタプリタ</a>に渡される場合や <code>-m</code> オプションの引数として指定される場合には <code>__main__</code> となる</li>
<li><a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> パッケージは  <code>-m</code> オプションの引数として指定される場合には <code>__main__</code> となる</li>
<li><a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> コードは、<code>-c</code> オプションや標準入力から  <a class="keyword" href="https://d.hatena.ne.jp/keyword/Python">Python</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%D7%A5%EA%A5%BF">インタプリタ</a>に渡される場合には <code>__main__</code> となる</li>
</ul>


<p>モジュールは、自身の <code>__name__</code> を以下のようなコードでチェックすることで、何らかの形で直接実行されているのか他のモジュール等から呼び出されて実行されているかを判定できるため、テスト時等他のモジュールから Import されずに実行される場合には所定のメソッドを呼び出す、といった場合などに実行させたい処理を記述できます。</p>

<pre class="code lang-python" data-lang="python" data-unlink><span class="synStatement">if</span> __name__ == <span class="synConstant">'__main__'</span>:
    <span class="synComment"># exec </span>
</pre>


<h2 id="参考">参考</h2>

<ul>
<li><a href="https://docs.python.org/ja/3.11/library/__main__.html#what-is-the-top-level-code-environment">__main__ --- &#x30C8;&#x30C3;&#x30D7;&#x30EC;&#x30D9;&#x30EB;&#x306E;&#x30B9;&#x30AF;&#x30EA;&#x30D7;&#x30C8;&#x74B0;&#x5883; &mdash; Python 3.11.7 &#x30C9;&#x30AD;&#x30E5;&#x30E1;&#x30F3;&#x30C8;</a></li>
<li><a href="https://atmarkit.itmedia.co.jp/ait/articles/1907/09/news010.html">&#xFF3B;Python&#x5165;&#x9580;&#xFF3D;&#x30D1;&#x30C3;&#x30B1;&#x30FC;&#x30B8;&#xFF1A;Python&#x5165;&#x9580;&#xFF08;1/2 &#x30DA;&#x30FC;&#x30B8;&#xFF09; - &#xFF20;IT</a></li>
</ul>

<div class="footnote">
<p class="footnote"><a href="#fn-442f27b9" id="f-442f27b9" name="f-442f27b9" class="footnote-number">*1</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://docs.python.org/ja/3/library/">https://docs.python.org/ja/3/library/</a><strong>main</strong>.html#module-<strong>main</strong>:title</span></p>
<p class="footnote"><a href="#fn-0a72068a" id="f-0a72068a" name="f-0a72068a" class="footnote-number">*2</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://docs.python.org/ja/3/reference/toplevel_components.html">9. &#x30C8;&#x30C3;&#x30D7;&#x30EC;&#x30D9;&#x30EB;&#x8981;&#x7D20; &mdash; Python 3.12.1 &#x30C9;&#x30AD;&#x30E5;&#x30E1;&#x30F3;&#x30C8;</a></span></p>
<p class="footnote"><a href="#fn-ca22455a" id="f-ca22455a" name="f-ca22455a" class="footnote-number">*3</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://docs.python.org/ja/3/library/">https://docs.python.org/ja/3/library/</a><strong>main</strong>.html#what-is-the-top-level-code-environment:title</span></p>
<p class="footnote"><a href="#fn-8562bcf0" id="f-8562bcf0" name="f-8562bcf0" class="footnote-number">*4</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://docs.python.org/ja/3/tutorial/modules.html">6. &#x30E2;&#x30B8;&#x30E5;&#x30FC;&#x30EB; &mdash; Python 3.12.1 &#x30C9;&#x30AD;&#x30E5;&#x30E1;&#x30F3;&#x30C8;</a></span></p>
</div>
