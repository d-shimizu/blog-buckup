---
layout: post
Categories:  ["tech"]
Description:  " Terraform を実行する際に、踏み台となる AWS アカウントから AssumeRole で別の AWS アカウントに Terraform を実行するために必要な設定メモ。 "
Tags: []
date: "2021-12-11T19:29:00+09:00"
title: "AssumeRole で Terraform を実行するための設定メモ"
author: "dshimizu"
archive: ["2021"]
draft: "true"
---

<body>
<p>Terraform を実行する際に、踏み台となる <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウントから AssumeRole で別の <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウントに Terraform を実行するために必要な設定メモ。</p>
</body>

<!-- more -->

<body>
<h2>設定</h2>

<h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> 関連の設定</h3>

<p>踏み台となる <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウント(アカウントID: <code>987654321098</code>)に作成した IAM ユーザーを作成しておき、クレデンシャルの設定をする。
スイッチ先の <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウント(アカウントID: <code>123456789012</code>)用の profile の設定はなくて良い。</p>

<ul>
<li><code>~/.aws/config</code></li>
</ul>


<pre class="code" data-lang="" data-unlink> [default]
region = ap-northeast-1
output = json

[profile test]
region = ap-northeast-1
output = json
#role_arn=arn:aws:iam::123456789012:role/switch-role-admin
#source_profile=default </pre>


<ul>
<li><code>~/.aws/credentials</code></li>
</ul>


<pre class="code" data-lang="" data-unlink> [default]
aws_access_key_id = ************
aws_secret_access_key = ************************ </pre>


<p>また、Terraform を Apply とかしたいスイッチ先の <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウント(アカウントID: <code>123456789012</code>)に IAM Role を作成して利用可能にしておく。
ここではスイッチ先の <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウント(アカウントID: 123456789012)に <code>arn:aws:iam::123456789012:role/switch-role-admin</code> を作成しておき、踏み台となる <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウント(アカウントID: <code>987654321098</code>)に作成した IAM ユーザーから利用可能にしておく。</p>

<h3>Terraform の設定</h3>

<p>適当に以下のようなファイルを作成する。
backend と provider で Assume Role の設定が必要となる。</p>

<ul>
<li><code>provider.tf</code></li>
</ul>


<pre class="code lang-tf" data-lang="tf" data-unlink> provider "aws" {
  region     = var.aws_provider_configs["region"]
  shared_credentials_file     = var.aws_provider_configs["shared_credentials_file"]
  assume_role {
    role_arn = "arn:aws:iam::123456789012:role/switch-role-admin"
  }
 </pre>


<ul>
<li>variables.tf</li>
</ul>


<pre class="code lang-tf" data-lang="tf" data-unlink> variable "aws_configs" {
  type = map(string)
}
 </pre>


<ul>
<li><code>terraform.tfvars</code></li>
</ul>


<pre class="code tfvars" data-lang="tfvars" data-unlink> aws_configs = {
  region = "ap-northeast-1"
  shared_credentials_file = "$HOME/.aws/credentials"
} </pre>


<ul>
<li><code>backend.tf</code></li>
</ul>


<pre class="code lang-tf" data-lang="tf" data-unlink> terraform {
  backend "s3" {
    bucket  = "terraform-987654321098"
    key     = "terraform.tfstate"
    encrypt = true

    // vars から受け取れない
    region  = "ap-northeast-1"
    shared_credentials_file = "$HOME/.aws/credentials"
    role_arn = "arn:aws:iam::123456789012:role/switch-role-admin"
  }
}
 </pre>


<h2>実行</h2>

<pre class="code shell" data-lang="shell" data-unlink> % terraform plan -var-file=terraform.tfvars </pre>

</body>
