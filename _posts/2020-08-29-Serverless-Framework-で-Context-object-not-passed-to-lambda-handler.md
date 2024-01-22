---
layout: post
Categories:  ["tech"]
Description:  " Serverless Framewokr で Python の Lambda 関数を作成していたら以下のエラーが発生した。    \"errorMessage\": \"Bad handler 'lambda_handler': not eno"
Tags: ["Serverless Framework", "tech"]
date: "2020-08-29T01:11:00+09:00"
title: "Serverless Framework で Context object not passed to lambda handler"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p>Serverless Framewokr で <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> の Lambda 関数を作成していたら以下のエラーが発生した。</p>

<pre class="terminal">   "errorMessage": "Bad handler 'lambda_handler': not enough values to unpack (expected 2, got 1)"
  "errorType": "Runtime.MalformedHandlerName"
 </pre>

</body>

<!-- more -->

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ構造は以下の様な形にしていた。</p>

<pre class="terminal"> ./my-prj
┣ serverless.yml
┗ test.py
 </pre>


<p><code>serverless.yml</code>をざっくり以下のようにしていた。</p>

<pre class="terminal"> functions:
  Test:
    handler: test
    description: test
 </pre>


<p><code>test.py</code>のハンドラーを以下のようにしていた。</p>

<pre class="terminal"> def lambda_handler(event, context):
    ...
 </pre>


<p>これがダメだった模様。</p>

<p>実際にはモジュール名とハンドラー名に合わせて、<code>serverless.yml</code>の<code>handler</code>を以下の様にする必要があった。
<code>test.py</code>がモジュール、<code>test.py</code>の中の<code>lambda_handler</code>がハンドラーになる。</p>

<pre class="terminal"> functions:
  Test:
    handler: test.lambda_handler
    description: test
 </pre>


<h3>参考</h3>


<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/lambci/docker-lambda/issues/200">Context object not passed to lambda handler · Issue #200 · lambci/docker-lambda</a></li>
</ul>

</body>
