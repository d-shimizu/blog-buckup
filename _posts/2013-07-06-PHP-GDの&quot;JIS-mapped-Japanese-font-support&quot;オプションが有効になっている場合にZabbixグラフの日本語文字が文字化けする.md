---
layout: post
Categories:  ["tech"]
Description:  " はじめに  日本語フォントを利用しているのにZabbixのグラフで日本語文字が文字化けしてしまいました。 原因は、長いタイトルの通りPHP GD(グラフィックライブラリ)のJIS-mapped Japanese font support "
Tags: ["PHP", "Zabbix", "FreeBSD"]
date: "2013-07-06T02:01:00+09:00"
title: "PHP GDの&quot;JIS-mapped Japanese font support&quot;オプションが有効になっている場合にZabbixグラフの日本語文字が文字化けする"
author: "dshimizu"
archive: ["2013"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p>日本語フォントを利用しているのにZabbixのグラフで日本語文字が文字化けしてしまいました。<br>原因は、長いタイトルの通り<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a> GD(グラフィックライブラリ)の<code>JIS-mapped Japanese font support</code> (文字列を<a class="keyword" href="http://d.hatena.ne.jp/keyword/SJIS">SJIS</a>へ変換するオプション)が影響していたためであったようで、それについて調べたことをまとめておきます。 </p>
<a name="more"></a><h5>環境</h5>
<p>確認した環境は以下の通りです。 </p>
<table>
<tr>  <td bgcolor="#cccccc" align="center">OS</td>  <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/FreeBSD">FreeBSD</a> 9.1-RELEASE <a class="keyword" href="http://d.hatena.ne.jp/keyword/amd64">amd64</a> (64bit)</td>
</tr>
<tr>  <td bgcolor="#cccccc" align="center">Zabbix</td>  <td>Zabbix Server 2.0.6</td>
</tr>
<tr>  <td bgcolor="#cccccc" align="center">Webサーバ</td>  <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a> 2.4.4</td>
</tr>
<tr>  <td bgcolor="#cccccc" align="center"><a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a></td>  <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a> 5.4.16</td>
</tr>
<tr>  <td bgcolor="#cccccc" align="center">DB</td>  <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a> 9.2.4</td>
</tr> <tr>  <td bgcolor="#cccccc" align="center">Zabbixフロントエンド<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ</td>  <td>/usr/local/www/zabbix2 <br>(<a class="keyword" href="http://d.hatena.ne.jp/keyword/FreeBSD">FreeBSD</a>デフォルト ※環境に合わせて読み替えてください)</td>
</tr>
</table> <h4>Zabbixグラフの文字化けの原因と対策</h4>
<p>Zabbixはユーザごとに管理画面からWebインタフェースの表示言語を設定することができますが、特に何もしないまま"日本語"を選択すると、グラフの日本語文字部分が以下の通り文字化けしてしまいます。 </p> <div class="separator" style="clear: both; text-align: center;"><a href="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220322/20220322113809.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220322/20220322113809.png"></a></div>
<br><p>文字化けの原因は英語フォントを利用しているためですので、通常は日本語フォントを指定すれば日本語を表示することができます。<br><a class="keyword" href="http://d.hatena.ne.jp/keyword/FreeBSD">FreeBSD</a>では<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ports">Ports</a>に<a class="keyword" href="http://d.hatena.ne.jp/keyword/IPA%A5%D5%A5%A9%A5%F3%A5%C8">IPAフォント</a>が用意されていますので、これをインストールします。 </p>
<pre class="terminal">  # cd /usr/ports/japanese/font-ipa-uigothic  
</pre>
<pre class="terminal">  # make install clean  
</pre>
<p>インストール後、<code>/usr/local/share/font-mplus-ipa/fonts</code> 以下に <code>ipagui.ttf</code> が設置されますので、Zabbixフロントエンドのfonts<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ以下に設置します。ここでは<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%F3%A5%DC%A5%EA%A5%C3%A5%AF%A5%EA%A5%F3%A5%AF">シンボリックリンク</a>を作成しています。 </p>
<pre class="terminal">  # ln -s /usr/local/share/font-mplus-ipa/fonts/ipagui.ttf /usr/local/www/zabbix2/fonts/ipagui.ttf  
</pre>
<p>Zabbixフロントエンドの <code>/usr/local/www/zabbix2/include/defines.inc.php</code> ファイルの38行目を修正します。<br>フォント名を <code>DejaVuSans</code> から <code>ipagui</code> に変更します。 </p>
<pre class="terminal">  //define('ZBX_GRAPH_FONT_NAME', 'DejaVuSans'); ※コメントアウト define('ZBX_GRAPH_FONT_NAME', 'ipagui');  
</pre>
<p>設定を反映させるために<a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a>を再起動します。 </p>
<pre class="terminal">  # service apache24 restart  
</pre>
<p>通常、これで文字化けは解消されるはずですが、私の環境では以下のように文字化けしておりました。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://4.bp.blogspot.com/-Nh16RbkA10w/UdWA5IB98TI/AAAAAAAAAWk/iVsSxWrCbVc/s700/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88+2013-07-04+23.00.43.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://4.bp.blogspot.com/-Nh16RbkA10w/UdWA5IB98TI/AAAAAAAAAWk/iVsSxWrCbVc/s700/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88+2013-07-04+23.00.43.png"></a></div> <h5>文字化け原因は<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a> GD(画像生成ライブラリ)の日本語処理</h5>
<p>最初は原因が分からず、DB側の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%CA%B8%BB%FA%A5%B3%A1%BC%A5%C9">文字コード</a>を見直してみたりしましたが、解消されませんでした。そして、さらに調べると<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a> GD(グラフィックライブラリ)に日本語処理オプションがあるようで、これが影響していることが分かりました。 </p>
<p>Zabbixで日本語文字をグラフに埋め込む処理は <code>/usr/local/www/zabbix2/include/graphs.inc.php</code> に記載されています。Zabbix 2.0.6では663行目にあります。 </p>
<pre class="terminal">  if ($gdinfo['FreeType Support'] &amp;&amp; function_exists('imagettftext')) {  
</pre>
<p>詳しい説明は省略しますが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>でグラフを扱うには、GDというライブラリをインストールする必要があります。<br>また、ここで指定されている <code>imagettftext()</code> 関数は<a class="keyword" href="http://d.hatena.ne.jp/keyword/TrueType%A5%D5%A5%A9%A5%F3%A5%C8">TrueTypeフォント</a>(拡張子が".ttf",".<a class="keyword" href="http://d.hatena.ne.jp/keyword/ttc">ttc</a>"のもの)を扱うための関数で、この関数を利用するにはマニュアルによると<a class="keyword" href="http://d.hatena.ne.jp/keyword/FreeType">FreeType</a>というフォントライブラリをインストールする必要があります。<br>これらのライブラリは<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>時に<code>--with-gd[=DIR], --with-freetype-dir[=DIR]</code>のオプションが指定されていればでインストールされています。 </p>
<p>通常これらのライブラリがあれば、日本語フォントを指定することでZabbixのグラフを日本語化できます。 </p>
<p>しかし、それでも文字化けが発生するのは、<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>時に <code>JIS-mapped Japanese font support</code> (--enable-gd-jis-conv)が有効になっている場合です。<br><code>imagettftext()</code> 関数は本来、<a class="keyword" href="http://d.hatena.ne.jp/keyword/UTF-8">UTF-8</a>で文字列を扱いますが、<code>JIS-mapped Japanese font support</code> が有効な場合、内部で<a class="keyword" href="http://d.hatena.ne.jp/keyword/SJIS">SJIS</a>に変換される処理が行われてしまうため、<a class="keyword" href="http://d.hatena.ne.jp/keyword/UTF-8">UTF-8</a>の文字列も<a class="keyword" href="http://d.hatena.ne.jp/keyword/SJIS">SJIS</a>に変換され、上記のような文字化けが発生していたようです。 </p> <h5>※補足</h5>
<p>GDライブラリの設定情報は以下のコードで表示可能です。 <br><code>[JIS-mapped Japanese Font Support] ==&gt;&gt; OK</code> と表示されれば、<code>JIS-mapped Japanese font support</code> が有効になっています。 </p>
<pre class="terminal">      $buf_info = "";     $arrInfo = gd_info();     foreach ($arrInfo as $idx =&gt; $buf) {         if (is_bool($buf)) {             $buf_info .= " " .htmlspecialchars("[$idx]  ==&gt;&gt; " .($buf ? "OK" : "No Support"))." ";         } else {             $buf_info .= " " .htmlspecialchars("[$idx]  ==&gt;&gt; $buf") ." ";         }     }     echo $buf_info; ?&gt;  </pre>  <h5>対応策</h5>
<p>これを解決するためには、日本語フォントサポート(JIS-mapped Japanese font support)オプションを無効になるように<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>を再<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>するか、<code>/usr/local/www/zabbix2/include/graphs.inc.php</code> の664行目に<a class="keyword" href="http://d.hatena.ne.jp/keyword/SJIS">SJIS</a>から<a class="keyword" href="http://d.hatena.ne.jp/keyword/UTF-8">UTF-8</a>へ変換する以下3行の処理を追加する必要があります。 </p>
<pre class="terminal">  if ($gdinfo['FreeType Support'] &amp;&amp; function_exists('imagettftext')) {     // JIS-mapped Japanese Font Supportが有効になっている場合、UTF-8 から SJISへの変換する処理を追加     if ($gdinfo['JIS-mapped Japanese Font Support']) {         $string = mb_convert_encoding($string, "SJIS", "UTF-8");     }  
</pre>
<p>ここでは <code>/usr/local/www/zabbix2/include/graphs.inc.php</code> に上記の処理を追加しました。設定を反映させるため、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a>を再起動します。 </p>
<pre class="terminal">  # service apache24 restart  
</pre>
<p>再度グラフを確認してみますと、以下の通り日本語が正常に表示されていることを確認できました。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://4.bp.blogspot.com/-Zmg0g3nnbXY/UdboUFVGbsI/AAAAAAAAAW8/5cNkwPsh1kE/s700/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88+2013-07-06+0.33.40.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://4.bp.blogspot.com/-Zmg0g3nnbXY/UdboUFVGbsI/AAAAAAAAAW8/5cNkwPsh1kE/s700/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88+2013-07-06+0.33.40.png"></a></div> <h4>おわりに</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>の知識が無かったのでこの問題になかなか気付けませんでしたが、Zabbixのソースを見たり<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>のマニュアルを読んで少しですが理解が出来たのでよかったです。 </p>
<p>ただ、<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>がZabbixのみで使用されている環境であれば、<code>JIS-mapped Japanese font support</code> が無効になるよう<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>を再<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>してしまうほうが良いかも知れません。 </p> <h4>参考</h4>
<ul>  <li><a href="http://www.zabbix.jp/node/491">（Zabbix1.8.1）グラフの日本語の文字化けについて | ZABBIX-JP</a></li>  <li><a href="http://www.zabbix.jp/node/596">zabbix-1.8.3 グラフの日本語処理について | ZABBIX-JP</a></li>
</ul>
</body>

<!-- more -->


