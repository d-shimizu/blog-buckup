---
layout: post
Categories:  ["tech"]
Description:  " Terraform v0.l2がリリースされたのでAWS Providerをv0.11.14 からアップグレードした。  基本的に下記オフィシャルドキュメントの通りで、アップグレードには v0.12 に付属されているヘルパーコマンドを使う"
Tags: ["tech", "Terraform"]
date: "2019-11-10T11:55:00+09:00"
title: "Terraform v0.12がリリースされたのでAWS Providerをv0.11.14 からアップグレードした時の備忘録"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p>Terraform v0.l2がリリースされたので<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Providerをv0.11.14 からアップグレードした。</p>

<p>基本的に下記オフィシャルドキュメントの通りで、アップグレードには v0.12 に付属されているヘルパーコマンドを使う。
その時の手順を記載する。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/upgrade-guides/0-12.html">Upgrading to Terraform 0.12 - Terraform by HashiCorp</a></li>
</ul>

</body>

<!-- more -->

<body>
<h3>Terraform v0.l2 から v0.11.14 へのアップグレード</h3>




<h4>事前準備</h4>




<h5>既存Terraformのバックアップ</h5>


<p>既存のTerraformのコード、およびtfstateファイルをバックアップする。</p>

<p>Terraformのコードは v0.11 と v0.12で書き方が結構変わっているようだった。
v0.12に付属されているアップグレード機能を使ってアップグレードを行う場合、既存のTerraformコードが書き換えられてしまうため、もし v0.11に戻したい、となった場合に対応できるように既存コードのバックアップを取っておく。
tfstateも念のためバックアップを取っておく。</p>

<h5>Terraform v0.11.14へアップグレード</h5>


<p>Terraform v0.11 系であれば v0.12系へアップグレードできるようだが、 0.11.14 では 0.12へアップグレードする際のチェックを行うサブコマンドが追加されているので、0.11.14へアップグレードする。
すでに v0.11 系を使っていれば、v0.11.14のterraformを取得して<code>terraform init</code>, <code>terraform plan</code>, <code>terraform apply</code>で念のため問題ないことを確認するので良いと思うが細かいところはドキュメントを確認されたし。自分の環境では問題なかった。</p>

<ul>
  <li>v0.11.14でのバージョン確認</li>
</ul>
<pre class="terminal"> $ terraform version
Terraform v0.11.14

Your version of Terraform is out of date! The latest version
is 0.12.19. You can update by downloading from www.terraform.io/downloads.html
 </pre>





<ul>
  <li>作業<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リの初期化</li>
</ul>
<pre class="terminal"> $ terraform init

Initializing the backend...

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.aws: version = "~&gt; 2.27"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
 </pre>





<ul>
   <li>実行計画の確認</li>
</ul>
<pre class="terminal"> $ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.

:
:
(略)
:
:

------------------------------------------------------------------------

No changes. Infrastructure is up-to-date.

This means that Terraform did not detect any differences between your
configuration and real physical resources that exist. As a result, no
actions need to be performed.
 </pre>





<ul>
  <li>適用</li>
</ul>
<pre class="terminal"> $ terraform apply
:
:
(略)
:
:

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.
 </pre>





<h4>アップグレードする</h4>




<h5>Terraform v0.12.10の取得</h5>


<p>Terraform v0.12.10を取得して展開し、Pathが通っている場所へ設置する。</p>

<pre class="terminal"> $ wget https://releases.hashicorp.com/terraform/0.12.10/terraform_0.12.10_linux_amd64.zip -P /tmp/
 </pre>


<p>適当なところに展開する。
ここでは <code>/usr/local/bin/</code> とする。</p>

<pre class="terminal"> $ sudo unzip /tmp/terraform_0.12.10_linux_amd64.zip  -d /usr/local/bin/
Archive:  /tmp/terraform_0.12.10_linux_amd64.zip
  inflating: /usr/local/bin/terraform
 </pre>


<p><code>terraform 0.12checklist</code>を実行する。
これにより以下がチェックされる。</p>

<ul>
  <li>Terraform 0.12と互換性のないプロバイダーバージョンを使っているかどうか。</li>
  <li>数字で始まるリソース名やプロバイ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C0%A5%A8%A5%A4">ダエイ</a>リアス名の有無。Terraform 0.12では数字で始まるリソース名は無効になったため。</li>
  <li>外部モジュールが上記の項目に該当しないかどうか。</li>
</ul>


<p>問題なければ以下のメッセージが出力される。
問題があった場合にどのようなメッセージが返されるのかは不明。</p>

<pre class="terminal"> $ terraform 0.12checklist
Looks good! We did not detect any problems that ought to be
addressed before upgrading to Terraform v0.12.

This tool is not perfect though, so please check the v0.12 upgrade
guide for additional guidance, and for next steps:
    https://www.terraform.io/upgrade-guides/0-12.html
 </pre>




<h4>Terraform v0.12へのアップグレード</h4>


<p>Terraform v0.12で作業<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リの初期化を行う。</p>

<pre class="terminal"> $ terraform init

Initializing the backend...

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.aws: version = "~&gt; 2.27"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
 </pre>


<p><code>Terraform 0.12upgrade</code>でTerraformコードをアップグレードする。
<code>Enter a value:</code>でモジュールをアップデートするか？といったことを尋ねられるので問題なければ<code>yes</code>を実行する。</p>

<pre class="terminal"> $ terraform 0.12upgrade

This command will rewrite the configuration files in the given directory so
that they use the new syntax features from Terraform v0.12, and will identify
any constructs that may need to be adjusted for correct operation with
Terraform v0.12.

We recommend using this command in a clean version control work tree, so that
you can easily see the proposed changes as a diff against the latest commit.
If you have uncommited changes already present, we recommend aborting this
command and dealing with them before running this command again.

Would you like to upgrade the module in the current directory?
  Only 'yes' will be accepted to confirm.

  Enter a value: yes
 </pre>


<p>以下のメッセージが出力されればアップデート自体は完了となる。</p>

<pre class="terminal"> -----------------------------------------------------------------------------

Upgrade complete!

The configuration files were upgraded successfully. Use your version control
system to review the proposed changes, make any necessary adjustments, and
then commit.
 </pre>


<p>このままplan -&gt; applyを実行して、正常に実行できれば問題ない。
ただ、このやり方でもまだエラーが出るケースもあるようで、その場合は手動で修正していくしかない。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/upgrade-guides/0-12.html#integer-vs-float-number-types">Upgrading to Terraform 0.12 - Terraform by HashiCorp</a></li>
</ul>




<h3>まとめ</h3>


<p>Terraform v0.11(v0.11.14)-&gt;v0.12へのアップグレード手順を自分で実行した時の内容を備忘録的に書いた。</p>

<h3>参考</h3>




<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/upgrade-guides/0-12.html">Upgrading to Terraform 0.12 - Terraform by HashiCorp</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://dev.classmethod.jp/tool/terraform-upgrade-from-0-11-to-0-12/">Terraform 0.12がリリースされたのでアップグレードしてみた ｜ Developers.IO</a></li>
</ul>

</body>
