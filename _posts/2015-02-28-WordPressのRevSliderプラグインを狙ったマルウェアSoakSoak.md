---
layout: post
Categories:  ["tech"]
Description:  " はじめに  昨年末より、WordPressのスライダープラグイン \"RevSlider\" に対して、SoakSoakというマルウェアでの攻撃が確認されているようです。      最も有名なスライダープラグイン　RevSlider プラグイ"
Tags: ["WordPress", "セキュリティ"]
date: "2015-02-28T22:13:00+09:00"
title: "WordPressのRevSliderプラグインを狙ったマルウェアSoakSoak"
author: "dshimizu"
archive: ["2015"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p>昨年末より、<a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a>のスライダー<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a> "RevSlider" に対して、SoakSoakという<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A5%EB%A5%A6%A5%A7%A5%A2">マルウェア</a>での攻撃が確認されているようです。 </p> <ul>  <a href="https://www.leon-tec.co.jp/wordpress-vulnerability/966/">最も有名なスライダープラグイン　RevSlider プラグインを狙ったマルウェアが、すでに10万サイトを汚染。 | 株式会社レオンテクノロジーはWebサイトを守るセキュリティ会社です</a>
</ul> <a name="more"></a> <h4>攻撃方法</h4> <p>攻撃者はまず、"revicons.eot" や "wp-config.<a class="keyword" href="http://d.hatena.ne.jp/keyword/php">php</a>" のファイルが存在するか、アクセス可能であるかを確認するために、以下のようなリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを送信します。 </p> <pre class="terminal">   GET /wp-content/plugins/revslider/rs-plugin/font/revicons.eot HTTP/1.1″ 200  </pre> <pre class="terminal">   GET /wp-admin/admin-ajax.php?action=revslider_show_image&amp;img=../wp-config.php HTTP/1.0″ 202  </pre>  <p>ファイルの存在が確認できた場合、攻撃者は対象サイトに<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C0%C8%BC%E5%C0%AD">脆弱性</a>のある Revslider が利用されていると判断し、Revslider の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C0%C8%BC%E5%C0%AD">脆弱性</a>を利用してファイルをアップロードしようとします。 </p> <pre class="terminal">   “POST /wp-admin/admin-ajax.php HTTP/1.1″ 200  </pre> <p>この攻撃が成功した場合、<code>/wp-content/plugins/revslider/temp/update_extract/revslider/update.php</code> へアクセスする為に、 Filesman と呼ばれる<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%C3%A5%AF%A5%C9%A5%A2">バックドア</a>(一度侵入に成功した攻撃者が、後から何度も侵入するために仕掛けておく秘密の入り口)が仕込まれます。 </p> <pre class="terminal">  “GET /wp-content/plugins/revslider/temp/update_extract/revslider/update.php HTTP/1.1″ 200  </pre> <p>そこから、攻撃者はswfobject.jsファイルを変更して2つ目の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%C3%A5%AF%A5%C9%A5%A2">バックドア</a>を仕込み、サイト訪問者をsoaksoak.ruへリダイレクトする<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A5%EB%A5%A6%A5%A7%A5%A2">マルウェア</a>を注入します。 </p>  <h4>対策</h4> <p>対策としてはやはり基本的なことですが、以下のような手法が考えられます。 </p> <ul>  <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a> のバージョンアップ</li>  <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>のバージョンアップ</li>  <li>/wp-admin 配下のアクセス制限</li>
</ul> <h4>参考</h4>
<ul>  <li><a href="https://blog.sucuri.net/2014/12/revslider-vulnerability-leads-to-massive-wordpress-soaksoak-compromise.html">revslider-vulnerability-leads-to-massive-wordpress-soaksoak-compromise.html</a></li>
</ul> </body>

<!-- more -->


