---
layout: post
Categories:  ["tech"]
Description:  " AWS CLI v2 のDockerイメージがAWSから公開された。     AWS CLI v2 Docker image | AWS Developer Blog  "
Tags: ["AWS", "tech"]
date: "2020-05-07T17:44:00+09:00"
title: "AWS CLI v2 のDockerイメージを使ってみる"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a> v2 のDockerイメージが<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>から公開された。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://aws.amazon.com/jp/blogs/developer/aws-cli-v2-docker-image/">AWS CLI v2 Docker image | AWS Developer Blog</a></li>
</ul>

</body>

<!-- more -->

<body>
<h3>使ってみる</h3>


<p>docker runコマンドを使用して実行してみる。
初回はイメージの取得が行われる。</p>

<pre class="terminal"> $ docker run --rm -it amazon/aws-cli --version
Unable to find image 'amazon/aws-cli:latest' locally
latest: Pulling from amazon/aws-cli
:
:
Status: Downloaded newer image for amazon/aws-cli:latest
aws-cli/2.0.30 Python/3.7.3 Linux/4.19.76-linuxkit botocore/2.0.0dev34
 </pre>


<p>S3の一覧を取得してみる。</p>

<pre class="terminal"> % docker run --rm -it -v ~/.aws:/root/.aws amazon/aws-cli s3 ls
2019-09-18 10:14:10 aaaaaa
2019-10-07 09:11:26 bbbbbb
:
 </pre>


<p><code>-v</code>オプションでボリュームマウントを行う。ここではローカルの<code>~/.aws</code>をコンテナの<code>/root/.aws</code>へマウントし、クレデンシャル情報をコンテナ側で読み込めるようにする。
<code>--rm</code>オプションでコンテナ終了時にコンテナ自動的に削除させる。
<code>--it</code>は上記のケースではなくても良い。</p>

<p>ドキュメントにある通り、ALIASに設定しておけば楽に使えるようになる。</p>

<pre class="terminal"> % alias aws-cli='docker run --rm -ti -v ~/.aws:/root/.aws -v $(pwd):/aws amazon/aws-cli'
 </pre>




<pre class="terminal"> % aws-cli s3 ls --profile=hogehoge
2019-09-18 10:14:10 aaaaaa
2019-10-07 09:11:26 bbbbbb
:
 </pre>


<p>まとめ</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>から公式に<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> CLIv2 のDockerイメージが提供されていた。
Dockerを使えば楽に利用できるようになる。</p>

<p>参考</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://aws.amazon.com/jp/blogs/developer/aws-cli-v2-docker-image/">AWS CLI v2 Docker image | AWS Developer Blog</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://qiita.com/kai_kou/items/cfb7c1d6a449e0da68d1">AWS公式さんがDocker Hubで aws-cli のイメージを公開してくれた！ - Qiita</a></li>
</ul>

</body>
