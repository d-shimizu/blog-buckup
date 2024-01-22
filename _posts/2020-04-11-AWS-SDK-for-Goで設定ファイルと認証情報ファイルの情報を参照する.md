---
layout: post
Categories:  ["tech"]
Description:  " AWS SDK for Goを使ってAWSの設定ファイルと認証情報ファイルのçクレデンシャル情報を利用する方法のメモ。 "
Tags: ["Golang", "tech"]
date: "2020-04-11T20:46:00+09:00"
title: "AWS SDK for Goで設定ファイルと認証情報ファイルの情報を参照する"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/SDK">SDK</a> for Goを使って<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の設定ファイルと認証情報ファイルのçクレデンシャル情報を利用する方法のメモ。</p>
</body>

<!-- more -->

<body>
<h3>~/.<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a>/credentialsの情報を参照する</h3>


<p>最初にsessionライブラリのMust関数（session.Must())を使い、引数にsession.NewSession()を定義してセッションを確立する。
session.NewSession()で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>や共有認証ファイル（〜/.<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a>/credentials）、共有設定ファイル（〜/.<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a>/config）を元にしてセッションが作成される。
Must関数を使えばエラーハンドリングもここでやってくれる。</p>

<p>また、そのために<code>github.com/aws/aws-sdk-go/aws/session</code>をインポートする。</p>

<p>デフォルトのプロファイルを利用する場合は以下のようになる。</p>

<p>続いて各サービスのNew関数を利用してクライアント用<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を作成する。ひとつ目の引数にsessionを、ふたつ目の引数にConfigを指定する。
以下ではcostexplorerを利用するためのものなので使うサービスによって読み替える。</p>

<pre class="terminal"> import (
    "github.com/aws/aws-sdk-go/aws"
    "github.com/aws/aws-sdk-go/aws/session"
    "github.com/aws/aws-sdk-go/service/costexplorer"  // 参照するAWSサービスのAPIに依存する
)

  :
  :

        sess := session.Must(session.NewSession())
        svc := costexplorer.New(
                sess,
                aws.NewConfig().WithRegion("ap-northeast-1"),
        )
 </pre>


<p>特定のプロファイルを利用する場合は以下のようにNewSessionWithOptions関数を利用し、その中でProfile名を指定する。
以下ではhogehogeプロファイルを利用している。</p>

<pre class="terminal"> import (
    "github.com/aws/aws-sdk-go/aws"
    "github.com/aws/aws-sdk-go/aws/session"
    "github.com/aws/aws-sdk-go/service/costexplorer"  // 参照するAWSサービスのAPIに依存する
)

  :
  :

        sess := session.Must(session.NewSessionWithOptions(session.Options{Profile:"hogehoge"})
        svc := costexplorer.New(
                sess,
                aws.NewConfig().WithRegion("ap-northeast-1"),
        )
 </pre>




<h3>まとめ</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/SDK">SDK</a> for Goでの<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>クレデンシャルを参照する方法について書いた。
他にも<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>を使う方法もある。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のEC2とかで実行するならIAM Roleを使うのが良い。</p>

<h3>参考</h3>




<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/sdk-for-go/api/aws/session/">session - Amazon Web Services - Go SDK</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/sdk-for-go/v1/api/aws.session.NewSession.html">NewSession - AWS SDK for Go (PILOT)</a></li>
</ul>

</body>
