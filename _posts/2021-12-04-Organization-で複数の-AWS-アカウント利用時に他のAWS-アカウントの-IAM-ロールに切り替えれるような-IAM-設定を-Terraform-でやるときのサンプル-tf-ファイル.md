---
layout: post
Categories:  ["tech"]
Description:  " 他の AWS アカウントの IAM ロールを使って操作できるよう IAM 設定を Terraform で行いたい。 "
Tags: []
date: "2021-12-04T20:43:00+09:00"
title: "Organization で複数の AWS アカウント利用時に他のAWS アカウントの IAM ロールに切り替えれるような IAM 設定を Terraform でやるときのサンプル tf ファイル"
author: "dshimizu"
archive: ["2021"]
draft: "true"
---

<body>
<p>他の <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウントの IAM ロールを使って操作できるよう IAM 設定を Terraform で行いたい。</p>
</body>

<!-- more -->

<body>
<h2>スイッチロールのイメージ</h2>

<p>以下のような感じにする。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220406/20220406204128.png" alt="f:id:dshimizu:20220406204128p:plain" width="1200" height="669" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<h2>踏み台 <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウントの IAM 設定用 tf ファイル</h2>

<pre class="code lang-tf" data-lang="tf" data-unlink> // アカウントID を取得する
data "aws_caller_identity" "custodian" {}

// 管理者向けの IAM ポリシードキュメントを設定する
data "aws_iam_policy_document" "admin" {
  statement {
    effect = "Allow"
    actions = [
      "sts:DecodeAuthorizationMessage",
      "sts:GetCallerIdentity",
      "sts:GetSessionToken",
    ]
    resources = ["*"]
  }

  // アクセスする先の AWS アカウントにある IAM ロールへの AssumeRole を許可する
  statement {
    effect  = "Allow"
    actions = ["sts:AssumeRole"]
    resources = [
      "arn:aws:iam::987654321098:role/switch-role-admin",
    ]
  }

  // IAM のリソースの参照権限を許可する
  statement {
    effect = "Allow"
    actions = [
      "iam:Get*",
      "iam:List*",
    ]
    resources = ["*"]
  }

  // 自分自身へのパスワード変更やログイン情報、アクセスキーに関する操作を許可する
  statement {
    effect = "Allow"
    actions = [
      "iam:ChangePassword",
      "iam:*LoginProfile",
      "iam:*AccessKey*",
    ]
    resources = [
      "arn:aws:iam::${data.aws_caller_identity.custodian.account_id}:user/&amp;{aws:username}",
    ]
  }
}

// IAM ポリシーを作成して管理者用のポリシーを設定する
resource "aws_iam_policy" "admin" {
  name   = "${aws_iam_group.admin.name}"
  policy = "${data.aws_iam_policy_document.admin.json}"
}

// IAM グループを作成する
resource "aws_iam_group" "admin" {
  name = "passione"
}

// IAM グループにポリシーをアタッチする
resource "aws_iam_group_policy_attachment" "admin" {
  group      = "${aws_iam_group.admin.name}"
  policy_arn = "${aws_iam_policy.admin.arn}"
}

// IAM ユーザを作成する
resource "aws_iam_user" "hogehoge" {
  name = "hogehoge"
}

// IAM ユーザーを IAM グループに追加する
resource "aws_iam_group_membership" "admin" {
  name  = "${aws_iam_group.hogehoge.name}"
  group = "${aws_iam_group.admin.name}"
  users = [
    "${aws_iam_user.hogehoge.name}",
  ]
}
 </pre>


<h2>切り替え先 <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウントの IAM 設定用 tf ファイル</h2>

<pre class="code lang-tf" data-lang="tf" data-unlink> // IAM ポリシードキュメント作成
data "aws_iam_policy_document" "assume_role_policy" {
  statement {
    effect  = "Allow"
    actions = ["sts:AssumeRole"]
    principals {
      type        = "AWS"
      identifiers = ["arn:aws:iam::123456789012:root"]
    }
  }
}

// IAM ロール作成
resource "aws_iam_role" "admin" {
  name               = "switch-role-admin"
  assume_role_policy = "${data.aws_iam_policy_document.assume_role_policy.json}"
}

// IAM ポリシードキュメント作成
data "aws_iam_policy_document" "admin" {
  statement {
    effect    = "Allow"
    actions   = ["*"]
    resources = ["*"]
  }
}

// IAM ポリシー作成
resource "aws_iam_policy" "admin" {
  name   = "admin_policy"
  policy = "${data.aws_iam_policy_document.admin.json}"
}

// IAM ポリシーを IAM ポリシーへアタッチ
resource "aws_iam_role_policy_attachment" "admin" {
  role       = "${aws_iam_role.admin.name}"
  policy_arn = "${aws_iam_policy.admin.arn}"
}
 </pre>


<h2>参考</h2>

<p><a href="https://engineer.crowdworks.jp/entry/2018/07/17/103453#%E3%83%9E%E3%83%AB%E3%83%81%E3%82%A2%E3%82%AB%E3%82%A6%E3%83%B3%E3%83%88%E6%88%A6%E7%95%A5">AWS Organizationsによるマルチアカウント戦略とその実装 - クラウドワークス エンジニアブログ</a>
<a href="https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/id_roles_use_switch-role-console.html">ロールの切り替え (コンソール) - AWS Identity and Access Management</a></p>
</body>
