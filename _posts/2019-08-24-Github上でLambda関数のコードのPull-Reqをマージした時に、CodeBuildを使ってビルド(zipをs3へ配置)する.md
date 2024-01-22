---
layout: post
Categories:  ["tech"]
Description:  " GithubでマージされたLambda関数を、CodeBuildを使ってビルド...と言うべきかzipをs3へ配置してみる。 "
Tags: ["AWS", "CodeBuild", "Lambda", "tech"]
date: "2019-08-24T21:21:00+09:00"
title: "Github上でLambda関数のコードのPull Reqをマージした時に、CodeBuildを使ってビルド(zipをs3へ配置)する"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Github">Github</a>でマージされたLambda関数を、CodeBuildを使ってビルド...と言うべきかzipをs3へ配置してみる。</p>
</body>

<!-- more -->

<body>
<h3>利用コード</h3>


<p>CloudWatchのアラームをSlackへ通知するLambda関数を使う。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/d-shimizu/aws-lambda-sample_cloudwatch-alarm-to-slack">d-shimizu/aws-lambda-sample_cloudwatch-alarm-to-slack - GitHub</a></li>
</ul>


<p>これの変更を<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>へPushしてマージされたらビルドが走り、S3へzipが配置されることになるようにする。</p>

<h4>Buildspec.yml</h4>


<p>buildspec.ymlの大まかな流れは下記のようになる。
commandsブロックに実行するコマンドを記述できる。</p>

<ul>
    <li>install: ビルドする環境を構築する。ここでは<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 3.7を指定する。</li>
    <li>pre_build: ビルド前の依存ライブラリのインストールなどを行う。ここでは定義しているだけで何もしていない。</li>
    <li>build: ビルド処理を行う。Lambdaへアップするファイルをzip圧縮してs3にアップロードしている。</li>
    <li>post_build: ビルド後のアップロードなどを実行する。ここでは定義しているだけで何もしていない。</li>
</ul>


<h3>CodeBuildのビルドプロジェクト作成</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>マネジメントコンソールのCodeBuildの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C0%A5%C3%A5%B7%A5%E5">ダッシュ</a>ボードへ遷移する。
「ビルドプロジェクトの作成」を選択する。すでに別なCodeBuildのプロジェクトがある場合は、画面左メニューの「ビルド」を展開し、「ビルドプロジェクト」を選択してから、「ビルドプロジェクトの作成」 を選択する。</p>

<h4>プロジェクトの設定</h4>


<p>ビルドプロジェクトを新規作成する。
「プロジェクト名」を適当に入力する。名前はあとで変更できない。
他の項目は入力しなくても良い。</p>

<p><img src="/wp-content/uploads/codebuild-project-setting.png" alt="" width="518" height="404" class="aligncenter size-full wp-image-1086"></p>

<h4>送信元</h4>


<p>「ソースプロバイダ」として「<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>」を指定する。初回の設定時は<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>との認証画面が立ち上がるので設定する。</p>

<p>「<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>」には「<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>アカウント」を指定する。
「<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>」には自分が利用したい<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>を指定する。</p>

<p>他は空欄で構わない。</p>

<p><img src="/wp-content/uploads/codebuild-source.png" alt="" width="518" height="404" class="aligncenter size-full wp-image-1086"></p>

<h4>プライマリソースのウェブフックイベント</h4>


<p>「コードの変更がこの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>にプッシュされるたびに再構築する」にチェックを入れる。
「イベントタイプ」には「PULL_REQUEST_MERGED」を指定する。
<img src="/wp-content/uploads/codebuild-webhook.png" alt="" width="518" height="404" class="aligncenter size-full wp-image-1086"></p>

<h4>環境</h4>


<p>「環境イメージ」には「マネージド型イメージ」を指定する。
「<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AA%A5%DA%A5%EC%A1%BC%A5%C6%A5%A3%A5%F3%A5%B0%A5%B7%A5%B9%A5%C6%A5%E0">オペレーティングシステム</a>」はどちらでも良いがここでは「AmazonLinux2」を指定している。
「ランタイム」は「Standard」、「イメージ」は「<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a>/codebuild/amazonlinux2-<a class="keyword" href="http://d.hatena.ne.jp/keyword/x86">x86</a>_64-standard:1.0」を、「イメージのバージョン」は「このランタイムには常に最新のイメージを使用してください」を選択する。</p>

<p>「サービスロール」はどちらでも良い。ここでは新規作成している。 <strong>S3にファイルをアップロードするので、ここで作成したロールにS３への読み書きができる権限を追加しておく必要がある。</strong></p>

<p><img src="/wp-content/uploads/codebuild-environment.png" alt="" width="518" height="404" class="aligncenter size-full wp-image-1086"></p>

<h4>Buildspec</h4>


<p>「ビルド仕様」には「buildspecファイルを使用する」を選択する。
「Buildspec名」には「buildspec.yml」を指定する。
<img src="/wp-content/uploads/codebuild-buildspec.png" alt="" width="518" height="404" class="aligncenter size-full wp-image-1086"></p>

<h4><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%C6%A5%A3%A5%D5%A5%A1%A5%AF%A5%C8">アーティファクト</a></h4>


<p>ビルドした成果物を配置する場所を指定する。
今回はbuildフェーズ内でzipファイルをアップロードするので指定しない。</p>

<h4>ログ</h4>


<p>必要に応じて設定すれば良い。</p>

<h3>まとめ</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Github">Github</a>でPull ReqがマージされたLambda関数をCodeBuildを使ってビルド(zipをs3へ配置)するやり方について書いた。</p>

<p>Lambda関数は現状codedeployではデプロイできないようなのでどうするか悩んでいる。
codebuildのcommandsブロックでLambdaへアップロードすることもできるが、ビルドフェーズでデプロイするというちょっとおかしいことになるのでうまいやり方を見つけたい。</p>

<h3>参考</h3>


<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/codebuild/latest/userguide/create-project.html">CodeBuild でビルドプロジェクトを作成する - AWS CodeBuild</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://ohke.hateblo.jp/entry/2019/07/06/230000">AWS CodeBuildでGitHubと連携してPythonアプリをビルドする - け日記</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://tech.actindi.net/2018/08/31/120906">AWS CodeBuild 入門 - アクトインディ開発者ブログ</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://dev.classmethod.jp/cloud/aws/codebuild-github-pullrequest-settings/">CodeBuild で GitHub のプルリクエストを自動ビルドして、結果を表示する ｜ DevelopersIO</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://qiita.com/maruware/items/c3a6f6741220ef3e61f7">CodeBuildでStandard2.0のイメージを使用したらエラー出たときの対処 - Qiita</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://qiita.com/ys_tydy/items/0323b84533e0edd468c3">CodeBuildを使ったlambda自動更新のメモ - Qiita</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/codedeploy/latest/userguide/welcome.html#compute-platform">CodeDeploy とは - AWS CodeDeploy</a></li>
</ul>

</body>
