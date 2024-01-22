---
layout: post
Categories:  ["tech"]
Description:  " DockerでPythonの実行環境を構築してみる。Pythonのアプリケーションを動かすというよりは、Dockerコンテナ内でPythonを使ってちょっとしたスクリプトとかを作ったり動かせるようにする、という程度のものを作る。  類似の"
Tags: ["Docker", "Python", "tech"]
date: "2020-07-23T01:04:00+09:00"
title: "DockerでPythonの実行環境を作る"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p>Dockerで<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a>の実行環境を構築してみる。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a>のアプリケーションを動かすというよりは、Dockerコンテナ内で<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a>を使ってちょっとした<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>とかを作ったり動かせるようにする、という程度のものを作る。</p>

<p>類似の記事は色々あると思うが自分用メモとして書いておく。</p>
</body>

<!-- more -->

<body>
<p><code>Dockefile</code>はとりあえず以下のような感じのものにして適当な作業<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リへ設置する。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%ED%A5%B1%A1%BC%A5%EB">ロケール</a>の設定とかちゃんとしないとダメだけどここでは書かない。</p>

<pre class="terminal"> FROM python:3.8

RUN apt-get update &amp;&amp; \
    apt-get -y install git \
                       vim

WORKDIR /usr/src/app

COPY requirements.txt ./
RUN pip install --upgrade pip
RUN pip install --no-cache-dir -r requirements.txt
 </pre>


<p>ビルドする。</p>

<pre class="terminal"> % docker build -t python-docker .
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/bash">bash</a> を起動する。</p>

<pre class="terminal"> % docker run --rm -it python-docker bash
root@08725c2d7b98:/usr/src/app#
root@08725c2d7b98:/usr/src/app# python -V
Python 3.8.3
 </pre>




<h3>参考</h3>




<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://hub.docker.com/_/python">Docker Hub</a></li>
</ul>

</body>
