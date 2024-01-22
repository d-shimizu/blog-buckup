---
layout: post
Categories:  ["tech"]
Description:  " GitHub Actions を手動で実行したい、と思ったらできるようになっていた。   ワークフローの手動実行 - GitHub Docs  "
Tags: []
date: "2021-09-19T19:19:00+09:00"
title: "GitHub Actions を手動実行できるようにする"
author: "dshimizu"
archive: ["2021"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a> Actions を手動で実行したい、と思ったらできるようになっていた。</p>

<ul>
<li><a href="https://docs.github.com/ja/actions/managing-workflow-runs/manually-running-a-workflow">ワークフローの手動実行 - GitHub Docs</a></li>
</ul>

</body>

<!-- more -->

<body>
<p><code>.github/workflows/</code> 配下のワークフローファイルで <code>on</code> に <code>workflow_dispatch</code> イベントを定義するだけで良い。</p>

<pre class="code workflow_dispatch_sample.yml" data-lang="workflow_dispatch_sample.yml" data-unlink> name: workflow_dispatch sample actions

on:
  workflow_dispatch:

jobs:
  sample-job:
    runs-on: ubuntu-latest

    steps:
    - name: sample
      run: echo "hello world!!" </pre>


<p>これで、このワークフローを利用可能なブランチで、Actions のメニューの該当のワークフローの定義で、手動実行用のボタンが表示される。</p>

<p>入力値などを細か定義したい場合は、 <code>on.workflow_dispatch.input</code> に必要なイベントを定義すれば良い。</p>

<h2>参考</h2>

<ul>
<li><a href="https://docs.github.com/ja/actions/using-workflows/events-that-trigger-workflows#workflow_dispatch">ワークフローをトリガーするイベント - GitHub Docs</a></li>
<li><a href="https://docs.github.com/ja/actions/using-workflows/workflow-syntax-for-github-actions#onworkflow_dispatchinputs">GitHub Actionsのワークフロー構文 - GitHub Docs</a></li>
<li><a href="https://docs.github.com/ja/actions/managing-workflow-runs/manually-running-a-workflow">ワークフローの手動実行 - GitHub Docs</a></li>
</ul>

</body>
