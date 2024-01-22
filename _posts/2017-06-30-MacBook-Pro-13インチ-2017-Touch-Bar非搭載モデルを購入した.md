---
layout: post
Categories:  ["tech"]
Description:  " もうここ１年ほど、普段使っている MacBook Air Mid 2011 の動作が重く、ちょっとブラウザ使うだけでもファンがぶんまわっちゃうしストレージもSSDだけど128GBしかなくて容量もかなり厳しいし2011年モデルのAirはサポ"
Tags: ["Macbook Pro", "other", "tech"]
date: "2017-06-30T00:14:00+09:00"
title: "MacBook Pro 13インチ 2017 Touch Bar非搭載モデルを購入した"
author: "dshimizu"
archive: ["2017"]
draft: "true"
---

<body>
<p>もうここ１年ほど、普段使っている <a class="keyword" href="http://d.hatena.ne.jp/keyword/MacBook%20Air">MacBook Air</a> Mid 2011 の動作が重く、ちょっとブラウザ使うだけでもファンがぶんまわっちゃうしストレージも<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSD">SSD</a>だけど128GBしかなくて容量もかなり厳しいし2011年モデルの<a class="keyword" href="http://d.hatena.ne.jp/keyword/Air">Air</a>はサポート終了とか言われてるし、５年は頑張ってもらったのでいい加減買い換え時だなーと思っていたところで、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Apple">Apple</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/WWDC">WWDC</a> 2017 でのまさかの新型<a class="keyword" href="http://d.hatena.ne.jp/keyword/MacBook%20Pro">MacBook Pro</a>発表・・・
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Surface">Surface</a>もいいなー、これを機に<a class="keyword" href="http://d.hatena.ne.jp/keyword/Linux">Linux</a>に乗り換えるのも良いなーと思いながら悩んでいたところでしたが、このタイミングで全体的にスペックアップした新型発売と、今使っているのが<a class="keyword" href="http://d.hatena.ne.jp/keyword/Mac">Mac</a>なので色々使っているツールの移行の手間が省けるしなどなどと自分に言い聞かせて買い換えてしまいました。</p>
</body>

<!-- more -->

<body>
<blockquote class="twitter-tweet tw-align-center" data-conversation="none" data-lang="ja">来た。 <a href="https://t.co/ziUFPbffPr" target="_blank" rel="noopener noreferrer">pic.twitter.com/ziUFPbffPr</a>

— d_shimizu (@d_shimizu) <a href="https://twitter.com/d_shimizu/status/879697720864235521" target="_blank" rel="noopener noreferrer">2017年6月27日</a>
</blockquote>


<script async="" src="//platform.twitter.com/widgets.js" charset="utf-8"></script>


<h4>構成</h4>


<ul>
    <li>2.5GHz<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%E5%A5%A2%A5%EB%A5%B3%A5%A2">デュアルコア</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/Intel">Intel</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/Core%20i7">Core i7</a>プロセッサ (Turbo Boost使用時最大4.0GHz)</li>
    <li>16GB 2,133MHz LPDDR3メモリ</li>
    <li>512GB <a class="keyword" href="http://d.hatena.ne.jp/keyword/SSD">SSD</a>ストレージ</li>
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Intel">Intel</a> Iris Plus Graphics 640</li>
    <li>Thunderbolt 3ポート x 2</li>
    <li>感圧タッチ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%C3%A5%AF%A5%D1%A5%C3%A5%C9">トラックパッド</a>
</li>
</ul>


<p><img src="http://blog.dshimizu.jp/wp-content/uploads/2017/07/macbookpro.jpg" alt="" width="512" height="128" class="aligncenter size-full"></p>

<h3>最初にやったこと</h3>


<h4>システム環境設定</h4>


<h5>キーボード関連</h5>


<p>「システム環境設定」 -&gt; 「キーボード」を選択 -&gt; 「キーボード」タブと遷移し以下を設定。</p>

<ul>
    <li>「F1、F2 などのすべてのキーを標準のファンクションキーとして使用」にチェック</li>
</ul>


<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%C3%A5%AF%A5%D1%A5%C3%A5%C9">トラックパッド</a>関連</h5>


<p>「システム環境設定」 -&gt; 「<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%C3%A5%AF%A5%D1%A5%C3%A5%C9">トラックパッド</a>」 -&gt; 「ポイントとクリック」と遷移し以下を設定。</p>

<ul>
    <li>「調べる＆データ検出」を「1本指で強めのクリック」</li>
    <li>「副ボタンのクリック」を「２本指でクリックまたはタップ」</li>
    <li>「タップでクリック」を「1本指でタップ」</li>
</ul>


<h5>スクリーンキャプチャ保存先変更</h5>


<p>[command] + [Shift] + [3] or [4] キーでスクリーンキャプチャを取得した時のデフォルト保存先はデスクトップなので、これを任意の場所に変更します。
以下では <code>~/Pictures/screencapture</code> にしています。</p>

<pre class="terminal"> $ mkdir ~/Pictures/screencapture
$ defaults write com.apple.screencapture location ~/Pictures/screencapture
$ killall SystemUIServer
 </pre>


<h5>Dock</h5>


<p>「システム環境設定」 -&gt; 「Dock」と遷移し以下にチェックを入れる。</p>

<ul>
    <li>「ウィンドウタイトルバーのダブルクリックで拡大/縮小」</li>
    <li>「ウィンドウをアプリケーションアイコンにしまう」</li>
    <li>「ウィンドウをアプリケーションアイコンにしまう」</li>
    <li>「Dockを自動的に隠す/表示」</li>
    <li>「起動済みのアプリケーションにインジケータを表示」</li>
</ul>




<h5>ファイアーウォールの有効化</h5>


<p>システム環境設定 -&gt; セキュリティとプライバシー -&gt; ファイアーウォール でファイアーウォールを有効化します。</p>

<h4>アプリケーションインストール(AppStore以外)</h4>


<h5>homebrew</h5>


<p><a href="https://brew.sh/" target="_blank" rel="noopener noreferrer">公式手順</a> に沿ってインストールします。</p>

<pre class="terminal"> $ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
 </pre>


<h5>その他アプリケーション</h5>


<ul>
        <li><a target="_brank" rel="noopener noreferrer" href="https://apps.apple.com/jp/app/xcode/id497799835">Xcode</a></li>
    <li><a class="keyword" href="http://d.hatena.ne.jp/keyword/Chrome">Chrome</a></li>
    <li><a class="keyword" href="http://d.hatena.ne.jp/keyword/FireFox">FireFox</a></li>
    <li>iTerm2</li>
    <li>Git</li>
    <li>Docker</li>
    <li><a class="keyword" href="http://d.hatena.ne.jp/keyword/Vagrant">Vagrant</a></li>
    <li><a class="keyword" href="http://d.hatena.ne.jp/keyword/Dropbox">Dropbox</a></li>
</ul>


<h3>まとめ</h3>


<p>まだ十分環境整備もできてないので実のない記事になってしまいましたが、スペースグレーのボディ、キーボードの打鍵の感覚や<a class="keyword" href="http://d.hatena.ne.jp/keyword/Retina%A5%C7%A5%A3%A5%B9%A5%D7%A5%EC%A5%A4">Retinaディスプレイ</a>の綺麗さ、全体的なスペックアップによるサクサク感など今の所満足です。ただやはりType-Cしかないのがまだちょっとアレ・・・</p>
</body>
