---
layout: post
Categories:  ["tech"]
Description:  " はじめに  Railsの勉強のため、簡単なRSSリーダーを作ってみて、その中で利用しているFeed取得・解析用のRubyライブラリ\"Feedzirra\"というものについて、基本的な使い方等を簡単なメモとして記載しておきます。      p"
Tags: ["Ruby", "Feedzirra"]
date: "2013-11-17T14:22:00+09:00"
title: "RubyのFeedライブラリ&quot;Feedzirra&quot;でFeedの情報を取得してみる"
author: "dshimizu"
archive: ["2013"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Rails">Rails</a>の勉強のため、簡単な<a class="keyword" href="http://d.hatena.ne.jp/keyword/RSS%A5%EA%A1%BC%A5%C0%A1%BC">RSSリーダー</a>を作ってみて、その中で利用しているFeed取得・解析用の<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ruby">Ruby</a>ライブラリ"Feedzirra"というものについて、基本的な使い方等を簡単なメモとして記載しておきます。 </p>
<ul>  <li><a href="https://github.com/pauldix/feedzirra">pauldix/feedzirra - GitHub</a></li>
</ul>
<a name="more"></a><h4>feedzirra</h4>
<p>Feed取得・解析用の<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ruby">Ruby</a>ライブラリです。<br>詳しい説明はREADMEを読んでいただくのが早いと思います。使い方のサンプルなども載ってます。 </p>
<ul>  <li><a href="https://github.com/pauldix/feedzirra/blob/master/README.md">feedzirra/README.md at master · pauldix/feedzirra · GitHub</a></li>
</ul> <h5>環境</h5> <p>実行環境は以下です。 </p>
<table>
<tr>  <td bgcolor="#cccccc">OS</td>  <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/CentOS">CentOS</a> 6.4 <a class="keyword" href="http://d.hatena.ne.jp/keyword/x86">x86</a>_64 (<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AB%A1%BC%A5%CD%A5%EB">カーネル</a>バージョン 2.6.32-358.18.1)</td>
</tr>
<tr>  <td bgcolor="#cccccc"><a class="keyword" href="http://d.hatena.ne.jp/keyword/Ruby">Ruby</a></td>  <td>バージョン 2.0.0p195 (2013-05-14 revision 40734) [<a class="keyword" href="http://d.hatena.ne.jp/keyword/x86">x86</a>_64-<a class="keyword" href="http://d.hatena.ne.jp/keyword/linux">linux</a>]</td>
</tr>
<tr>  <td bgcolor="#cccccc">Feedzirra</td>  <td>バージョン 0.2.2</td>
</tr>
</table> <h5>インストール</h5>
<p>gemコマンドでインストールできます。 </p>
<pre class="terminal">  $ gem search feedzirra  </pre> <h5>サンプルプログラム</h5>
<p>試しに実行して、いくつかのデータを取得してみます。<br>FeedzirraはFeedのURLを解析するため、逐一対象WebサイトのFeedのURLを自分で調べるのは手間なので、Nokogiriを利用してWebサイトのURLからそのWebサイトのFeedのURLを取得して、それを解析するようにします。 </p>
<p>記事のデータ取得に関しては、Webサイト側で配信されている順に配列で取得されます。<br>ニュースサイト等は必ずしも公開時間順になっている訳ではないようで、収集データを時系列で管理する場合は取得後に並び変える必要がありました(以下では特に記載してません)。 </p>
<pre class="terminal">  #!/usr/bin/env ruby  require 'open-uri' require 'nokogiri' require 'rss' require 'feedzirra'   ### 引数で受けとったWEBサイトのURLをnokogiriで解析してFeedのURLを取得 filename = ARGV[0] doc = Nokogiri::HTML(open(filename),nil,'utf-8') doc.css('link').each do |link|    if link['type'] == 'application/rss+xml' &amp;&amp; link['rel'] == 'alternate'     href = link['href']     @feed_url = URI.join(filename, href)    elsif link['type'] == 'application/atom+xml' &amp;&amp; link['rel'] == 'alternate'     href = link['href']     @feed_url = URI.join(filename, href)    end end  ### FeedのURLをFeedzirraで取得・解析 feed = Feedzirra::Feed.fetch_and_parse "#{@feed_url}"  ### データ取得例 ### 対象サイトのURL取得 p "url: #{feed.url}" ### 対象サイトのFeed用URL取得 p "feed_url: #{feed.feed_url}" ### 対象サイトの最終更新日取得 p "last_modified: #{feed.last_modified}"  ### 全記事を取得(配列で取得される) p "ALL articles: #{feed.entries[0]}" ### 最初の記事の公開日を取得 p "The first article 'title': #{feed.entries[0].title}" ### 2番目の記事の公開日を取得 p "The second article 'published': #{feed.entries[1].published}"  </pre> <h5>実行</h5>
<p>本ブログを対象に実行してみました。 </p>
<pre class="terminal">  $ ruby feedzirra-test.rb http://kanjuku-tomato.blogspot.com  
</pre>
<p><b>実行結果</b></p>
<pre class="terminal">  "url: http://kanjuku-tomato.blogspot.com/" "feed_url: http://kanjuku-tomato.blogspot.com/feeds/posts/default?alt=rss" "last_modified: 2013-11-17 13:55:23 +0900" "ALL articles: [#＜Feedzirra::Parser::RSSEntry:0x007fb4d607c910 @entry_id=\"tag:blogger.com,1999:blog-522146242299075566.post-5854226855805152372\", @published=2013-10-05 10:51:00 UTC, @categories=[\"FreeBSD\", \"さくらのVPS\"], @title=\"FreeBSD 9.1-RELEASEをFreeBSD 9.2-RELEASEへアップグレード\", @summary=\"&lt; ...(略)... 致します。 \", @url=\"http://kanjuku-tomato.blogspot.com/2013/01/blog-post.html\", @author=\"noreply@blogger.com (dshim)\"&gt;]" "The first article 'title': FreeBSD 9.1-RELEASEをFreeBSD 9.2-RELEASEへアップグレード" "The second article 'published': 2013-09-15 08:52:00 UTC"  </pre> <h4>おわりに</h4>
<p>Feed取得用の<a class="keyword" href="http://d.hatena.ne.jp/keyword/Runy">Runy</a> Gemは他にも以下のようなものがあるようです。<br></p>
<ul>  <li><a href="https://github.com/aasmith/feed-normalizer">aasmith / feed-normalizer</a></li>  <li><a href="https://github.com/arttu/feed_parser">arttu / feed_parser</a></li>
</ul>
<p>どのような違いがあるのか使って比較してないのでわかりませんが、現状ではFeedzirraは<a class="keyword" href="http://d.hatena.ne.jp/keyword/Github">Github</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>上の更新も頻繁に行われているようなので、Feedzirraを使ってみてます。取得したデータをDBに格納するようにして、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Rails">Rails</a>でFeed Readerを作ってます。 </p> <h4>参考</h4>
<ul>  <li><a href="http://havelog.ayumusato.com/develop/ruby/e555-rails_on_heroku_app.html">Rails + Heroku で俺専用RSSリーダー作った ::ハブろぐ</a></li>  <li><a href="http://i78s.me/tech/rails3_tutorial06">Rails3.2でRSSリーダーを作る その2(完) | i78s.me</a></li>  <li><a href="http://bellonieta.net/2013/08/rails4%E3%81%A7feedzilla%E3%82%92%E8%A9%A6%E3%81%97%E3%81%A6%E3%81%BF%E3%81%9F/">Rails4でfeedzillaを試してみた</a></li>  <li><a href="http://www.nextsoft.jp/articles/2013/rails-application-feedzirra.html">Railsアプリケーションを作ってみる。feedzirra – RSS取得編 | ネクスト株式会社</a></li>
</ul>
</body>

<!-- more -->


