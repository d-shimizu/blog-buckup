---
layout: post
Categories:  ["tech"]
Description:  " 環境変数をセットするのが面倒なので以下で1発で実行できるけど長いのが微妙。profile や terraform コマンドのオプションなどは実行環境に応じて使い分ける。  % docker run -i --rm -v $PWD:/wor"
Tags: []
date: "2021-08-07T02:06:00+09:00"
title: "Terraform の Docker イメージを使ってワンライナーで実行する"
author: "dshimizu"
archive: ["2021"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>をセットするのが面倒なので以下で1発で実行できるけど長いのが微妙。
profile や terraform コマンドのオプションなどは実行環境に応じて使い分ける。</p>

<pre class="code shell" data-lang="shell" data-unlink> % docker run -i --rm -v $PWD:/work -w /work -e AWS_ACCESS_KEY_ID=$(aws --profile terraform configure get aws_access_key_id) -e AWS_SECRET_ACCESS_KEY=$(aws --profile terraform configure get aws_secret_access_key) hashicorp/terraform plan -var-file=terraform.tfvars </pre>

</body>

<!-- more -->


