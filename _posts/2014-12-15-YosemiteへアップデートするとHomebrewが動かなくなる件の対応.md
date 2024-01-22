---
layout: post
Categories:  ["tech"]
Description:  " はじめに  MACのOSをyosemiteにアップデートした後、Homebrewが動かなくなりました。     原因と対応手段  事象  yosemiteにアップデート後にbrewコマンドを実行すると、以下のエラーが出るようになりました。"
Tags: ["Mac"]
date: "2014-12-15T02:16:00+09:00"
title: "YosemiteへアップデートするとHomebrewが動かなくなる件の対応"
author: "dshimizu"
archive: ["2014"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MAC">MAC</a>のOSをyosemiteにアップデートした後、Homebrewが動かなくなりました。 </p> <a name="more"></a> <h4>原因と対応手段</h4>
<h5>事象</h5>
<p>yosemiteにアップデート後に<code>brew</code>コマンドを実行すると、以下のエラーが出るようになりました。 </p>
<pre class="terminal">  $ brew /usr/local/bin/brew: /usr/local/Library/brew.rb: /System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby: bad interpreter: No such file or directory /usr/local/bin/brew: line 21: /usr/local/Library/brew.rb: Undefined error: 0  </pre> <h5>原因</h5>
<p>yosemiteへのアップデート後に<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ruby">Ruby</a>のバージョンが1.8から2.0に変わり<code>PATH</code>も変更されましたが、Homebrewのプログラムに記載されている<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ruby">Ruby</a>のパスが1.8のままのため、エラーとなってます。 </p> <h5>解決策</h5>
<p>最新のHomebrewでは既にこの修正が取り込まれているようなので、Homebrewのプログラムを最新にすることで解決できます。 <a class="keyword" href="http://d.hatena.ne.jp/keyword/brew">brew</a> repository(デフォルト <code>/usr/local</code>)に移動して、その後<code>git</code>コマンドでローカル<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>の更新を実行します。 </p>
<pre class="terminal">  $ cd /usr/local $ git pull origin master  
</pre>
<p>これで<code>brew</code>コマンドを実行できます。 </p>
<pre class="terminal">  % brew Example usage:   brew [info | home | options ] [FORMULA...]   brew install FORMULA...   brew uninstall FORMULA...   brew search [foo]   brew list [FORMULA...]   brew update   brew upgrade [FORMULA...]   brew pin/unpin [FORMULA...]  Troubleshooting:   brew doctor   brew install -vd FORMULA   brew [--env | config]  Brewing:   brew create [URL [--no-fetch]]   brew edit [FORMULA...]   open https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/Formula-Cookbook.md  Further help:   man brew   brew home  </pre> <h4>おわりに</h4>
<p>これでHomebrewが動くようになりました。定期的なアップデートは重要ですね。 </p> <h4>参考</h4>
<ul>  <li><a href="http://rrt.hateblo.jp/entry/2014/10/19/031312">homebrewユーザーがYosemiteにアプグレしたらbrewコマンドが使えなくなった問題解決法 - 現代版徒然草 (生まれてきたら負け) </a></li>  <li><a href="http://chroma.hatenablog.com/entry/2014/10/29/191638">Git のローカルリポジトリを更新して Homebrew を最新に - CHROMA</a></li>  <li><a href="http://loumo.jp/wp/archive/20141018001008/">Yosemite にしたら homebrew が動かなくなった | Lonely Mobiler</a></li>
</ul>
</body>

<!-- more -->


