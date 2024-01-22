---
layout: post
Categories:  ["tech"]
Description:  " Golangを勉強しているので、AWSの請求情報をSlackに通知するLambda関数をGolangで書いてみた。 "
Tags: ["Golang", "Lambda", "tech"]
date: "2020-08-10T23:26:00+09:00"
title: "AWSの請求情報をSlackに通知するLambda関数をGolangで書いてみた"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Golang">Golang</a>を勉強しているので、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の請求情報をSlackに通知するLambda関数を<a class="keyword" href="http://d.hatena.ne.jp/keyword/Golang">Golang</a>で書いてみた。</p>
</body>

<!-- more -->

<body>
<h3>事前準備</h3>


<ul>
    <li>SlackのIncoming WebHookのURL生成</li>
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> SAM <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>のインストール</li>
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Golang">Golang</a>のインストール</li>
</ul>


<h3>コード</h3>


<p><a target="_brank" rel="noopener noreferrer" href="https://github.com/d-shimizu/NotifyAwsBillingToSlack">d-shimizu/NotifyAwsBillingToSlack</a> に置いた。
コメントやインデントが整ってなかったりと些末なところでもなかなか汚いので追って直す。</p>

<p>SlackのIncoming WebHookのURLはパラメータストアのものを取得して呼び出すようにしているので、Incoming WebHookのURLを事前にパラメータストアへの登録しておく必要がある。
template.<a class="keyword" href="http://d.hatena.ne.jp/keyword/yaml">yaml</a>には既存の<a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/Gateway">Gateway</a>の設定を削除して、IAMポリシーでssm:GetParameterの権限付与、Cloudwatch Eventsで所定の時間(ここでは9時)に実行されるような設定を追加する。</p>

<p>月額の料金に加えて、サービスごとの料金も通知するようにしている。</p>

<p>あとはデプロイすれば動くはず。</p>

<h3>まとめ</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Golang">Golang</a>を勉強の一貫で、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の請求情報をSlackに通知するLambda関数を<a class="keyword" href="http://d.hatena.ne.jp/keyword/Golang">Golang</a>で書いてみた。
構造体とポインタはまだ難しく感じる。</p>
</body>
