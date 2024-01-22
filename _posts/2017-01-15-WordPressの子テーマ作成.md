---
layout: post
Categories:  ["tech"]
Description:  " WordPressで既存のテーマをカスタマイズしたいことは多いと思いますが、その方法として子テーマの作成が推奨されてます。       子テーマ - WordPress Codex 日本語版   例えばテーマに直接変更を加えていると、その"
Tags: ["tech", "WordPress"]
date: "2017-01-15T23:15:00+09:00"
title: "WordPressの子テーマ作成"
author: "dshimizu"
archive: ["2017"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a>で既存のテーマをカスタマイズしたいことは多いと思いますが、その方法として子テーマの作成が推奨されてます。</p>

<ul>
    <li><a href="https://wpdocs.osdn.jp/%E5%AD%90%E3%83%86%E3%83%BC%E3%83%9E" target="_blank" rel="noopener noreferrer">子テーマ - WordPress Codex 日本語版</a></li>
</ul>


<p>例えばテーマに直接変更を加えていると、そのテーマのアップデート時にその変更が上書きされて消えてしまうので、それに影響されずにテーマをカスタマイズできたりと便利です。</p>
</body>

<!-- more -->

<body>
<h3>子テーマの作成</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ構造は以下のような形になります。
テーマが配置されている<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ内に適当な名前(末尾に[code]-child[/code]をつけるのが推奨)の子テーマ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成して必要なファイルを配置していきます。</p>

<pre class="file"> wp-content
  - themes
    - hogehoge-child
      - functions.php
      - style.css
      - hogehoge.php
      :
      :
 </pre>


<p><code>style.css</code>と<code>function.php</code>は必須になります。
<code>style.css</code>は親テーマの<code>style.css</code>を上書きするので、親テーマのものをコピーして必要な箇所を書き換えます。幾つか書き方のルールがあるので、詳しくは公式ドキュメントの <a href="https://wpdocs.osdn.jp/%E5%AD%90%E3%83%86%E3%83%BC%E3%83%9E#.E5.AD.90.E3.83.86.E3.83.BC.E3.83.9E.E3.81.AE.E4.BD.9C.E3.82.8A.E6.96.B9" target="_blank" rel="noopener noreferrer">こちら</a> を参照してください。
<code>function.php</code>は親テーマのものに追加する形になるので、必要な関数を記述したものを設置しておきます。少なくとも子テーマ用の <code>style.css</code>を読み込む用の設定は必要です。
その他カスタマイズが必要な<code>.php</code>ファイルがあればそれをここに設置しておきます。</p>

<p>ここまで作成したら子テーマを有効化する準備ができてますので、<a class="keyword" href="http://d.hatena.ne.jp/keyword/WordPress">WordPress</a>管理画面にログインし、<code>管理画面 &gt; 外観 &gt; テーマ</code>に移動します。子テーマが表示され、有効化できる状態になっていますので有効化します。</p>

<h3>まとめ</h3>


<p>子テーマを作っておくと変更を子テーマのファイル内に限定できて管理も便利です。</p>
</body>
