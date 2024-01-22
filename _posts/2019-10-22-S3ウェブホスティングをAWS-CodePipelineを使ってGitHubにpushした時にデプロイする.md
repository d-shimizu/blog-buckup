---
layout: post
Categories:  ["tech"]
Description:  " S3ウェブホスティングのデプロイを手動でやっていたけど、AWS CodePipelineを利用してGitHub上にコードをPushした時にデプロイされるようにした。公式ドキュメントのチュートリアルをベースにやればだいたい良いけど自分用にメ"
Tags: ["AWS", "CodePipeline", "tech"]
date: "2019-10-22T18:28:00+09:00"
title: "S3ウェブホスティングをAWS CodePipelineを使ってGitHubにpushした時にデプロイする"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p>S3ウェブ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DB%A5%B9%A5%C6%A5%A3%A5%F3%A5%B0">ホスティング</a>のデプロイを手動でやっていたけど、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> CodePipelineを利用して<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>上にコードをPushした時にデプロイされるようにした。
公式ドキュメントの<a targe="_brank" rel="noopener" href="https://docs.aws.amazon.com/ja_jp/codepipeline/latest/userguide/tutorials-s3deploy.html">チュートリアル</a>をベースにやればだいたい良いけど自分用にメモとして記載しておく。</p>
</body>

<!-- more -->

<body>
<p><img src="http://blog.dshimizu.jp/wp-content/uploads/aws_codepipeline-s3.png" alt="" width="700" height="99" class="aligncenter size-full wp-image-1747"></p>

<h3>事前準備</h3>


<p>下記を作成しておく必要がある。</p>

<ul>
  <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Github">Github</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>とその中へ静的ウェブサイトのソースファイル</li>
  <li>ウェブサイト<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DB%A5%B9%A5%C6%A5%A3%A5%F3%A5%B0">ホスティング</a>用に設定された <a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon%20S3">Amazon S3</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%B1%A5%C3%A5%C8">バケット</a>
</li>
</ul>




<h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> CodePipelineを用いたパイプラインの作成</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>マネジメントコンソールのCodePipelineの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C0%A5%C3%A5%B7%A5%E5">ダッシュ</a>ボードへ遷移する。</p>

<p>下記の画面から「パイプラインの作成」を選択する。</p>

<p><img src="/wp-content/uploads/aws_codepipeline_201910_01.png" alt="" width="2872" height="1357" class="aligncenter size-full wp-image-1733"></p>

<h4>Step1: パイプラインの設定の選択</h4>


<p>パイプライン名を適当に入力する。</p>

<p>[Service role (サービスロール)]の設定で新しいサービスロールを作成するか、既存のサービスロールがある場合はそれを利用する。
新しいサービスロールを作成する場合、 <code>AWSCodePipelineServiceRole-${region}-${pipeline_name}</code>の形式でのサービスロールが作成される。${region}はパイプラインを作成するリージョン名、${pipeline_name}は上記で入力したパイプラインの名前になる。
このロールは、CodePipelineのパイプライン実行プロセスの中で使用される各<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>リソースへのアクセス許可に使用される。</p>

<p>それ以外はデフォルトでOK。</p>

<p><img src="/wp-content/uploads/aws_codepipeline_201910_02.png" alt="" width="2872" height="2043" class="aligncenter size-full wp-image-1734"></p>

<h4>Step2: ソースステージの追加</h4>


<p>デプロイする元となる<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BD%A1%BC%A5%B9%A5%B3%A1%BC%A5%C9">ソースコード</a>が保存されている<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>を指定する。ここでは<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>を選択する。</p>

<p><img src="/wp-content/uploads/aws_codepipeline_201910_03.png" alt="" width="2872" height="1360" class="aligncenter size-full wp-image-1735"></p>

<p>「<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>に接続する」を押下すると、初回の場合はOAuthでの認証が行われる。</p>

<p><img src="/wp-content/uploads/aws_codepipeline_201910_04.png" alt="" width="1200" height="1308" class="aligncenter size-full wp-image-1736"></p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>へ接続できたら、デプロイしたい<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>名とそのブランチを選択する。
変更検出オプションでは<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>ウェブフックを選択する。
これでその<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>のブランチになんらかの変更が加わったらデプロイが実行される。</p>

<p><img src="/wp-content/uploads/aws_codepipeline_201910_05.png" alt="" width="2872" height="1650" class="aligncenter size-full wp-image-1737"></p>

<h4>Step3: ビルドステージの追加</h4>


<p>ここではビルドは行わないので[ビルドステージのスキップ]を押下してスキップする。</p>

<p><img src="/wp-content/uploads/aws_codepipeline_201910_06.png" alt="" width="2872" height="1359" class="aligncenter size-full wp-image-1738"></p>

<h4>Step4: デプロイステージの追加</h4>


<p>デプロイプロバイダにS3を選択し、デプロイしたいS3<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%B1%A5%C3%A5%C8">バケット</a>を選択する。
[デプロイ前にファイルを展開] を選択する。デフォルトだとzip圧縮されるがこれによりファイルが展開される。
[デプロイパス]はファイルが展開されるS3のパスになる。空欄だとルートパスに配置される。</p>

<p><img src="/wp-content/uploads/aws_codepipeline_201910_07.png" alt="" width="2872" height="1681" class="aligncenter size-full wp-image-1739"></p>

<h4>レビュー</h4>


<p>内容を確認し、問題なければ[パイプラインの作成]を押下する。</p>

<p><img src="http://blog.dshimizu.jp/wp-content/uploads/aws_codepipeline_201910_08.png" alt="" width="2872" height="3009" class="aligncenter size-full wp-image-1740"></p>

<h4>実行</h4>


<p>これで<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>の対象のブランチ(ここではmaster)へコードをpushするだけでデプロイが開始されて、S3へ配置されるようになる。
うまく動けば完了。</p>

<h4>まとめ</h4>




<h3>参考</h3>




<ul>
  <li><a targe="_brank" rel="noopener" href="https://docs.aws.amazon.com/ja_jp/codepipeline/latest/userguide/tutorials-s3deploy.html">チュートリアル: Amazon S3 をデプロイプロバイダとして使用するパイプラインを作成する - CodePipeline</a></li>
  <li><a targe="_brank" rel="noopener" href="https://docs.aws.amazon.com/ja_jp/codepipeline/latest/userguide/how-to-custom-role.html">CodePipeline サービスロールの管理 - CodePipeline</a></li>
  <li><a targe="_brank" rel="noopener" href="https://docs.aws.amazon.com/ja_jp/codepipeline/latest/userguide/how-to-custom-role.html">CodePipeline アクションの種類との統合 - CodePipeline</a></li>
  <li><a targe="_brank" rel="noopener" href="https://www.kabegiwablog.com/entry/2019/02/19/100000">CodePipelineでGitHub上のコードをS3にデプロイする - かべぎわブログ</a></li>
</ul>

</body>
