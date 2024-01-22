---
layout: post
Categories:  ["tech"]
Description:  " はじめに  最近AWSを使う機会があり、よく触っています。 AWSは充実した管理画面があるので、WebブラウザからのGUIベースで簡単に環境を管理できます。ただ、Webブラウザ経由のGUIベースでAWS環境を管理するのもなかなか手間です。"
Tags: []
date: "2015-11-14T18:27:00+09:00"
title: "AWS CLIを使ってAmazon EC2インスタンスを起動する"
author: "dshimizu"
archive: ["2015"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p>最近<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>を使う機会があり、よく触っています。<br><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>は充実した管理画面があるので、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Web%A5%D6%A5%E9%A5%A6%A5%B6">Webブラウザ</a>からの<a class="keyword" href="http://d.hatena.ne.jp/keyword/GUI">GUI</a>ベースで簡単に環境を管理できます。ただ、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Web%A5%D6%A5%E9%A5%A6%A5%B6">Webブラウザ</a>経由の<a class="keyword" href="http://d.hatena.ne.jp/keyword/GUI">GUI</a>ベースで<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>環境を管理するのもなかなか手間です。Infrastructure as Codeなんてのも流行ってきてますし、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の環境もコードで管理できるようにしたいです。 </p> <p>ということで、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>にも<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>を<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%DE%A5%F3%A5%C9%A5%E9%A5%A4%A5%F3">コマンドライン</a>で操作するためのツールである<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>(Command Line Interface)がありますので、<a class="keyword" href="http://d.hatena.ne.jp/keyword/CentOS">CentOS</a> 7.0にこれをインストールしてみました。 </p>
<ul>  <li><a href="http://aws.amazon.com/jp/cli/">AWS Command Line Interface</a></li>
</ul> <p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>で<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>を<a class="keyword" href="http://d.hatena.ne.jp/keyword/CUI">CUI</a>で使いこなすために、まずはインストール手順を記載します。 </p> <a name="more"></a>  <h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>実行環境構築</h4>
<p>公式手順を元にインストールを行います。<a class="keyword" href="http://d.hatena.ne.jp/keyword/Linux">Linux</a>では<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>-<a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>のインストールのために<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a>(バージョン2.6.3以上)が必要ですので、事前にインストールしておく必要があります。 </p>
<ul>  <li><a href="http://docs.aws.amazon.com/cli/latest/userguide/installing.html">AWS CLIツール インストール手順</a></li>
</ul>   <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%C8%A1%BC%A5%E9">インストーラ</a>取得</h5>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a> Bundled Installerを取得します。取得したファイルはどこでも良いので適当な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リに設置します。 </p>
<pre class="terminal">  $ wget https://s3.amazonaws.com/aws-cli/awscli-bundle.zip  </pre> <p>取得したファイルを展開します。 </p>
<pre class="terminal">  $ unzip awscli-bundle.zip Archive:  awscli-bundle.zip   inflating: awscli-bundle/install   inflating: awscli-bundle/packages/argparse-1.2.1.tar.gz   inflating: awscli-bundle/packages/virtualenv-1.10.1.tar.gz   inflating: awscli-bundle/packages/pyasn1-0.1.7.tar.gz   inflating: awscli-bundle/packages/rsa-3.1.2.tar.gz   inflating: awscli-bundle/packages/docutils-0.12.tar.gz   inflating: awscli-bundle/packages/six-1.8.0.tar.gz   inflating: awscli-bundle/packages/colorama-0.2.5.tar.gz   inflating: awscli-bundle/packages/bcdoc-0.12.2.tar.gz   inflating: awscli-bundle/packages/jmespath-0.5.0.tar.gz   inflating: awscli-bundle/packages/python-dateutil-2.3.tar.gz   inflating: awscli-bundle/packages/awscli-1.6.10.tar.gz   inflating: awscli-bundle/packages/ordereddict-1.1.tar.gz   inflating: awscli-bundle/packages/simplejson-3.3.0.tar.gz   inflating: awscli-bundle/packages/botocore-0.80.0.tar.gz  </pre> <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>のインストール</h5>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>のインストール先<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成します。 </p>
<pre class="terminal">  # mkdir /usr/local/aws  </pre> <p>インストールを実行します。 </p>
<pre class="terminal">  # ./install -i /usr/local/aws -b /usr/local/bin/aws Running cmd: /bin/python virtualenv.py --python /bin/python /usr/local/aws Running cmd: /usr/local/aws/bin/pip install --no-index --find-links file:///home/dev/local/awscli-bundle/packages awscli-1.6.10.tar.gz You can now run: /usr/local/bin/aws --version  </pre> <p><code>/usr/local/bin</code>にコマンドがインストールされるのでPATHを通す必要があります。 </p>
<pre class="terminal">  export PATH=$PATH:/usr/local/bin  </pre> <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>の初期設定</h5>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>を実行するには、以下の4つの情報を<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>を実行する端末側で設定する必要があります。 </p>
<ul>  <li>アクセスキー</li>  <li>シークレットアクセスキー</li>  <li>接続先の<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>リージョン</li>  <li>出力フォーマット</li>
</ul> <p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>の<code>configure</code>コマンドを使うことで、対話的に設定できます。 </p>
<pre class="terminal">  $ aws configure AWS Access Key ID [None]: XXXXXXXXXXXXXXXXXXXXXXXXX AWS Secret Access Key [None]: XXXXXXXXXXXXXXXXXXXXXXXXX Default region name [None]: ap-northeast-1 Default output format [None]: json  </pre> <p><code>AWS Access Key ID</code>と<code>AWS Secret Access Key</code>にはアクセスキーとシークレットアクセスキーを設定します。アクセスキーとシークレットアクセスキーは<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>マネジメントコンソールから作成できます。IAMユーザを作って運用するのが一般的と思います。 </p>
<p><code>region name</code>には利用しているリージョンを指定します。 </p>
<code>output format</code>には、<code>json</code>、<code>table</code>、<code>text</code>の3種類を指定できます。  <p><code>aws configure</code>コマンドを実行が完了すると、<code>~/.aws</code><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リが作成され、その中に設定ファイル<code>config</code>と<code>credentials</code>が作成されます。<br><code>config</code>には<code>region name</code>と<code>output format</code>の情報が、<code>credentials</code>には<code>AWS Access Key ID</code>と<code>AWS Secret Access Key</code>の情報が記載されています。 </p> <h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>の動作確認</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>コマンドを実行してみて、動作することを確認します。<br>以下では EC2<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>の一覧を表示させています。 </p> <strong>EC2<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>の一覧を表示させる</strong><pre class="terminal">  $ aws ec2 describe-instances  </pre>
</body>

<!-- more -->


