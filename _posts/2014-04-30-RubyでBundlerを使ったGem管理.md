---
layout: post
Categories:  ["tech"]
Description:  " はじめに  Rubyでプログラムを書く時には必要なGemパッケージをインストールしていくことになりますが、環境に合わせてGemパッケージやそのバージョンを使い分けたり、システム環境を汚したくないとき等があります。 そんなときはディレクトリ"
Tags: ["Ruby", "Bundler", "Gem"]
date: "2014-04-30T23:59:00+09:00"
title: "RubyでBundlerを使ったGem管理"
author: "dshimizu"
archive: ["2014"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Ruby">Ruby</a>でプログラムを書く時には必要なGemパッケージをインストールしていくことになりますが、環境に合わせてGemパッケージやそのバージョンを使い分けたり、システム環境を汚したくないとき等があります。<br>そんなときは<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ毎にBundlerを使ってGemパッケージを管理するといろいろ便利かと思いやってみましたのでそのメモです。 </p> <a name="more"></a><h5>Bundlerとは</h5>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Ruby">Ruby</a>環境内で利用するGemを管理するためのソフトウェアです。<br><code>Gemfile</code>というファイルにパッケージ名、バージョンを記述してGemパッケージを管理出来ます。 Bundler自体もGemパッケージとして提供されています。 </p>
<ul>  <li><a href="http://bundler.io/">Bundler: The best way to manage a Ruby application's gems</a></li>
</ul> <h4>Bundlerを使ったGem管理のやり方</h4>
<h5>Bundlerのインストール</h5>
<p>Bundlerだけは通常通りにインストールしておく必要があります。 </p>
<pre class="terminal">  $ gem install bundler  </pre> <h5>Gemfileの準備</h5>
<p>まずはプログラム実行環境用の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成します。<br>その<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ配下で<code>bundle init</code>を実行して<code>Gemfile</code>を作成します。<br></p>
<pre class="terminal">  $ mkdir ~/rbdev  
</pre>
<pre class="terminal">  $ cd ~/rbdev  
</pre>
<pre class="terminal">  $ bundle init  
</pre>
<p><code>Gemfile</code>を編集し、インストールしたい必要なGemパッケージとそのバージョンを記述します。 </p>
<pre class="terminal">  $ vim Gemfile  </pre> <h5>Gemパッケージのインストール</h5>
<p><code>bundle install</code>を実行し、<code>Gemfile</code>に記述されたGemパッケージをインストールします。このとき、<code>--path</code>オプションを指定することでGemパッケージインストール先の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを指定できます。ここでは<code>vendor/bundler</code>と指定しているので、<code>~/rbdev/vendor/bundler</code>配下にGemパッケージがインストールされます。インストール先をこの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ環境内にすることでシステム環境を不用意に汚さずに済みます。 </p>
<pre class="terminal">  $ bundle install --path vendor/bundler  </pre> <h5>実行</h5>
<p>実行したいプログラム内でGemパッケージを<code>require</code>し、実行時に<code>bundle exec</code>を指定することで、Bundler経由でインストールしたGemパッケージを読み込んでプログラムを実行できます。 </p>
<pre class="terminal">  $ bundle exec ruby foo.rb  </pre> <h4>おわりに</h4>
<p>上記の内容よりもっとスマートなやり方があるかもしれませんが、Bundlerを使うことでGemパッケージを管理しやすくなると思います。<br>必要な環境毎に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを分けて<code>Gemfile</code>を作成し、<code>bundle install</code>実行時に<code>--path</code>でGemパッケージインストール先を制御することで、Gemパッケージを<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ毎に管理できるので、システム環境を汚したくない場合に便利かと思います。 </p> <h4>参考</h4>
<ul>  <li><a href="http://blog.dakatsuka.jp/2010/11/09/bundle-install.html">bundle installするときはpathを指定しよう | dakatsuka's blog 変更する</a></li>  <li><a href="http://shokai.org/blog/archives/7262">橋本商会 » Ruby書くならBundler使え 変更する</a></li>
</ul>
</body>

<!-- more -->


