---
layout: post
Categories:  ["tech"]
Description:  " はじめに  VMDKファイルをAmazon EC2にインポートして起動する方法のメモです。    Amazon EC2 API Toolsを利用したVMDKファイルのインポート   環境  環境は以下です。CentOS 7からAmazon"
Tags: ["AWS"]
date: "2014-12-14T11:52:00+09:00"
title: "Amazon EC2 API Toolsを使ってVMDKファイルをAWS環境へインポートする"
author: "dshimizu"
archive: ["2014"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p>VMDKファイルを<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon%20EC2">Amazon EC2</a>にインポートして起動する方法のメモです。 </p> <a name="more"></a><h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon%20EC2">Amazon EC2</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a> Toolsを利用したVMDKファイルのインポート</h4> <h5>環境</h5>
<p>環境は以下です。<a class="keyword" href="http://d.hatena.ne.jp/keyword/CentOS">CentOS</a> 7から<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon%20EC2">Amazon EC2</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a> Toolsを使って<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>環境へVMDKファイルをインポートします。<br><a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon%20EC2">Amazon EC2</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a> Toolsを実行するために<a class="keyword" href="http://d.hatena.ne.jp/keyword/Java">Java</a>の実行環境が必要になりますので、別途インストールする必要があります。また、<a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a>キー、アクセスキーを準備します。<br>VMDKファイルのインポート先にS3を利用する必要があるため、S3<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%B1%A5%C3%A5%C8">バケット</a>を作成しておく必要があります。 </p>
<table>  <tr>    <td bgcolor="#cccccc">プラットフォーム</td>    <td>さくらの<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPS">VPS</a> (<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSD">SSD</a> 1G)</td>  </tr>  <tr>    <td bgcolor="#cccccc">OS</td>    <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/CentOS">CentOS</a> 7.0</td>  </tr>  <tr>    <td bgcolor="#cccccc">
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Java">Java</a>バージョン</td>    <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/JDK">JDK</a> 1.8.0_25</td>  </tr>
<tr>  </tr>
<tr>    <td bgcolor="#cccccc">
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Java">Java</a>設置先<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ($<a class="keyword" href="http://d.hatena.ne.jp/keyword/JAVA">JAVA</a>_HOME)td&gt;    </td>
<td>~/local/jdk1.8.0_25</td>  </tr>
<tr>    <td bgcolor="#cccccc">
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon%20EC2">Amazon EC2</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a> Toolsバージョン</td>    <td>1.7.2.3</td>  </tr>
<tr>    </tr>
<tr>    <td bgcolor="#cccccc">
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon%20EC2">Amazon EC2</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a> Tools設置先<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ($EC2_HOME)</td>    <td>~/local/ec2-<a class="keyword" href="http://d.hatena.ne.jp/keyword/api">api</a>-tools-1.7.2.3</td>  </tr>
<tr>      <td bgcolor="#cccccc">インポート用<a class="keyword" href="http://d.hatena.ne.jp/keyword/VM">VM</a>設置先S3<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%B1%A5%C3%A5%C8">バケット</a>
</td>    <td>※S3<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%B1%A5%C3%A5%C8">バケット</a>を準備</td>  </tr>  <tr>    <td bgcolor="#cccccc">VMDKファイル</td>    <td>※boxファイルから取り出したVMDKファイル</td>
</tr>
</table> <h5>インポート実行</h5>
<p>必要な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>を設定します。 </p> <h6>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Java">Java</a>(<a class="keyword" href="http://d.hatena.ne.jp/keyword/JDK">JDK</a>)の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>設定</h6>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/JDK">JDK</a>設置先(<a class="keyword" href="http://d.hatena.ne.jp/keyword/JAVA">JAVA</a>_HOME)を<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>として設定します。 </p>
<pre class="terminal">  % export JAVA_HOME=~/local/jdk1.8.0_25  </pre> <h6>EC2 <a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a> Toolsの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>設定</h6>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon%20EC2">Amazon EC2</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a> Tools設置先を<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>として設定します。 </p>
<pre class="terminal">  $ export EC2_HOME=~/local/ec2-api-tools-1.7.2.3  </pre> <h6>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のアクセスキー/シークレットキーの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>設定</h6>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のアクセスキー/シークレットキーの値を<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>として設定します。 </p>
<pre class="terminal">  $ export AWS_ACCESS_KEY=xxxxxxxxxxxx $ export AWS_SECRET_KEY=xxxxxxxxxxxx  </pre> <h5>インポート実行</h5>
<p><code>ec2-import-instance</code>でインポートを実行します。<code>hogehoge.vmdk </code>の部分にはインポートしたいVMDKファイルをフルパスで指定します。 </p>
<pre class="terminal">  $ ec2-import-instance hogehoge.vmdk -t t2.micro -f VMDK -a x86_64 --bucket s3-hogehoge -o $AWS_ACCESS_KEY -w $AWS_SECRET_KEY  </pre> <ul>  <li>書式</li>
</ul>
<pre class="file">  -t, --instance-type: 起動インスタンスタイプ -f, --format: インポート対象ファイルの形式 -a, --architecture: VMイメージのアーキテクチャを指定(デォルトは i386) -p, --platform: 仮想マシンのプラットフォームを指定 -b, --bucket: インポート先S3バケット名を指定 -o, --owner-akid: アクセスキーIDを指定 -w, --owner-sak: シークレットキーを指定  </pre> <p>アップロードが完了するとS3<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%B1%A5%C3%A5%C8">バケット</a>にファイルが作成され、US West(Origon)に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>が作成されています。 </p> <h4>おわりに</h4>
<p>以上がVMDKファイルをインポートする手順となります。最後に、インポート後のS3<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%B1%A5%C3%A5%C8">バケット</a>に作成されるファイルは不要なので削除しておきます。 </p>
</body>

<!-- more -->


