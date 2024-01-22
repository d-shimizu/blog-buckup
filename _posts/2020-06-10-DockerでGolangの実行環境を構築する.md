---
layout: post
Categories:  ["tech"]
Description:  " DockerでGolangの実行環境を構築してみる。Golangのアプリケーションを動かすというよりは、DockerでGolangのちょっとしたスクリプトを作って動かせる、という程度のものを作る。 "
Tags: ["Golang", "tech"]
date: "2020-06-10T21:46:00+09:00"
title: "DockerでGolangの実行環境を構築する"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p>Dockerで<a class="keyword" href="http://d.hatena.ne.jp/keyword/Golang">Golang</a>の実行環境を構築してみる。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Golang">Golang</a>のアプリケーションを動かすというよりは、Dockerで<a class="keyword" href="http://d.hatena.ne.jp/keyword/Golang">Golang</a>のちょっとした<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>を作って動かせる、という程度のものを作る。</p>
</body>

<!-- more -->

<body>
<p>Dockefile はとりあえず以下のもの。</p>

<pre class="terminal"> FROM golang:1.14

RUN apt update &amp;&amp;
    apt install -y git ¥
                   vim

WORKDIR /go/src/app
COPY . ./

### 必要なパッケージのインストール
#RUN go get -d -v ./...
#RUN go install -v ./...

#CMD ["app"]

 </pre>


<p>ビルドする。</p>

<pre class="terminal"> % docker build -t golang-docker .
 </pre>


<p>起動する。</p>

<pre class="terminal"> % docker run --rm -it golang-docker
root@08c05673ee81:/go/src/app#
 </pre>




<pre class="terminal"> root@e483f66775da:/go/src/app# go version
go version go1.14.7 linux/amd64
 </pre>




<h3>参考</h3>




<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://hub.docker.com/_/golang">https://hub.docker.com/_/golang</a></li>
</ul>

</body>
