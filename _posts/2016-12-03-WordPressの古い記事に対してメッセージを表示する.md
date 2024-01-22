---
layout: post
Categories:  ["tech"]
Description:  " はじめに  少し前からブログなどで公開日が古い記事に対してその旨を示すメッセージが表示されているのを見る機会が多くなってきたような気がします。  IT技術系の記事などは変化の早い分野なのでそれに伴って情報の劣化も早く、その時点では正しくな"
Tags: ["tech", "WordPress"]
date: "2016-12-03T00:45:00+09:00"
title: "WordPressの古い記事に対してメッセージを表示する"
author: "dshimizu"
archive: ["2016"]
draft: "true"
---

<body>
<h3>はじめに</h3>


<p>少し前からブログなどで公開日が古い記事に対してその旨を示すメッセージが表示されているのを見る機会が多くなってきたような気がします。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/IT%B5%BB%BD%D1">IT技術</a>系の記事などは変化の早い分野なのでそれに伴って情報の劣化も早く、その時点では正しくない情報の記事になってしまっていることもあります。そのような場合に閲覧者に注意を促したいと思い、<a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a>での古い記事に対してもメッセージを表示する方法を確認しましたので記載します。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>や<a class="keyword" href="http://d.hatena.ne.jp/keyword/Javascript">Javascript</a>などで行う方法もあるようですが、ここではコードを追記する方法でやっています。</p>
</body>

<!-- more -->

<body>
<h3>環境</h3>


<p>以下の環境で動作を確認しました。</p>

<table>
<tbody>
<tr>
<th>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a>バージョン</th>
<td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a> 4.6</td>
</tr>
</tbody>
</table>


<h3>古い記事に対してメッセージ表示するコード</h3>


<p><code>${WORDPRESS_DIR}/wp-content/themes/${THEMA_NAME}/content.php</code> に以下を追記します。
追記する場所は、メッセージを表示させたい場所にもよりますが [code]</p>

<p><header class="entry-header">[/code] 内あたりで良いかと思います。</header></p>

<pre class="code"> [code] 99 ) { ?&gt;


 
            &lt;p&gt;この記事はに公開されたものです。内容が古い可能性がありますのでご注意ください。&lt;/p&gt;
 



[/code] </pre>


<p>上記のコードでやっていることは、現在の日付(YYYYMM)と記事公開した日付(YYYYMM)をそれぞれ数値とみなし、その差が 99　以上(1年以上経過しているもの)ならメッセージを表示させる、というものです。</p>

<h3>おわりに</h3>


<p>情報の古い記事は決して恥ではないけど役に立たないこともあります。</p>

<h3>参考</h3>


<ul>
    <li><a href="http://www.nxworld.net/wordpress/wp-display-message-on-old-posts.html" target="_blank" rel="noopener noreferrer">WordPress：古い記事にメッセージを表示させる際の備忘録 | NxWorld</a></li>
</ul>


<p></p>
</body>
