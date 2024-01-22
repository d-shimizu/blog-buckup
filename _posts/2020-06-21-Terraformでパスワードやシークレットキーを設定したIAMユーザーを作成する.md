---
layout: post
Categories:  ["tech"]
Description:  " Terraformでパスワードを設定したIAMユーザーを作成するのにPGP公開鍵が必要だったのでやり方のメモ。  流れは以下のような形だと思う。       PGPの公開鍵と秘密鍵のペアを作成する      Terraformのaws_i"
Tags: ["tech", "Terraform"]
date: "2020-06-21T18:26:00+09:00"
title: "Terraformでパスワードやシークレットキーを設定したIAMユーザーを作成する"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p>Terraformでパスワードを設定したIAMユーザーを作成するのに<a class="keyword" href="http://d.hatena.ne.jp/keyword/PGP">PGP</a>公開鍵が必要だったのでやり方のメモ。</p>

<p>流れは以下のような形だと思う。</p>

<ol>
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PGP">PGP</a>の公開鍵と<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C8%EB%CC%A9%B8%B0">秘密鍵</a>のペアを作成する</li>
    <li>Terraformの<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a>_iam_user_login_profileや<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a>_iam_<a class="keyword" href="http://d.hatena.ne.jp/keyword/access">access</a>_keyのResourceの<a class="keyword" href="http://d.hatena.ne.jp/keyword/pgp">pgp</a>_keyへ<a class="keyword" href="http://d.hatena.ne.jp/keyword/PGP">PGP</a>公開鍵をセットする</li>
    <li>セットした公開鍵を元に<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>側で暗号化したパスワードを返されるので確認する</li>
    <li>暗号化データを<a class="keyword" href="http://d.hatena.ne.jp/keyword/PGP">PGP</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%C8%EB%CC%A9%B8%B0">秘密鍵</a>で復号する</li>
</ol>

</body>

<!-- more -->

<body>
<h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PGP">PGP</a>の公開鍵と<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C8%EB%CC%A9%B8%B0">秘密鍵</a>のペアを作成する</h3>


<p>まず、<a class="keyword" href="http://d.hatena.ne.jp/keyword/PGP">PGP</a>の公開鍵と<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C8%EB%CC%A9%B8%B0">秘密鍵</a>を作成する。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PGP">PGP</a>(<a class="keyword" href="http://d.hatena.ne.jp/keyword/GnuPG">GnuPG</a>)がなければインストールする。</p>

<pre class="terminal"> % brew install gpg
 </pre>


<p>鍵ペアを作成する。<code>gpg</code>に<code>--gen-key</code>オプションを付与して実行する。
<code>--full-generate-key</code>が利用可能な旨のメッセージが出るがお好みで使えば良いと思う。</p>

<pre class="terminal"> % gpg --gen-key
gpg (GnuPG) 2.2.21; Copyright (C) 2020 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

注意: 全機能の鍵生成には "gpg --full-generate-key" を使います。

GnuPGはあなたの鍵を識別するためにユーザIDを構成する必要があります。

本名: 
 </pre>


<p>本名、メールアドレスの入力を促されるので適当に入力する。
最後に<code>名前(N)、電子メール(E)の変更、またはOK(O)か終了(Q)?</code>と問われるので、問題なければOを入力する。</p>

<pre class="terminal"> % gpg --gen-key
gpg (GnuPG) 2.2.21; Copyright (C) 2020 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

注意: 全機能の鍵生成には "gpg --full-generate-key" を使います。

GnuPGはあなたの鍵を識別するためにユーザIDを構成する必要があります。

本名: hogehoge
電子メール・アドレス: hogehoge@example.jp
次のユーザIDを選択しました:
    "hogeohge "

名前(N)、電子メール(E)の変更、またはOK(O)か終了(Q)?O
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D1%A5%B9%A5%D5%A5%EC%A1%BC%A5%BA">パスフレーズ</a>入力画面が出るので適当に入力する。</p>

<pre class="terminal">                                                             ┌──────────────────────────────────────────────────────┐
                                                            │ Please enter the passphrase to                       │
                                                            │ protect your new key                                 │
                                                            │                                                      │
                                                            │ Passphrase: ________________________________________ │
                                                            │                                                      │
                                                            │                                          │
                                                            └──────────────────────────────────────────────────────┘
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D1%A5%B9%A5%D5%A5%EC%A1%BC%A5%BA">パスフレーズ</a>の再入力をする。</p>

<pre class="terminal">                                                            ┌──────────────────────────────────────────────────────┐
                                                           │ Please re-enter this passphrase                      │
                                                           │                                                      │
                                                           │ Passphrase: ________________________________________ │
                                                           │                                                      │
                                                           │                                          │
                                                           └──────────────────────────────────────────────────────┘
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D1%A5%B9%A5%D5%A5%EC%A1%BC%A5%BA">パスフレーズ</a>を空にした場合以下のようなメッセージが出るので<code>Yes, protection is not needed</code>を選択する。</p>

<pre class="terminal">                                                             ┌───────────────────────────────────────────────────────────────────────────────────────────────┐
                                                            │ You have not entered a passphrase - this is in general a bad idea!                            │
                                                            │ Please confirm that you do not want to have any protection on your key.                       │
                                                            │                                                                                               │
                                                            │                                          │
                                                            └───────────────────────────────────────────────────────────────────────────────────────────────┘
 </pre>


<p>入力が終わったら以下のようなメッセージとともに鍵が作成される。</p>

<pre class="terminal"> たくさんのランダム・バイトの生成が必要です。キーボードを打つ、マウスを動か
す、ディスクにアクセスするなどの他の操作を素数生成の間に行うことで、乱数生
成器に十分なエントロピーを供給する機会を与えることができます。
たくさんのランダム・バイトの生成が必要です。キーボードを打つ、マウスを動か
す、ディスクにアクセスするなどの他の操作を素数生成の間に行うことで、乱数生
成器に十分なエントロピーを供給する機会を与えることができます。
gpg: 鍵66A1D81A25C544B6を究極的に信用するよう記録しました
gpg: 失効証明書を '/Users/hogehoge/.gnupg/openpgp-revocs.d/8A558AEBED1F1005E593552266A1D81A25C544B6.rev' に保管しました。
公開鍵と秘密鍵を作成し、署名しました。

pub   rsa2048 2020-07-24 [SC] [有効期限: 2022-06-24]
      8A558AEBED1F1005E593552266A1D81A25C544B6
uid                      hogeohge 
sub   rsa2048 2020-07-24 [E] [有効期限: 2022-06-24]
 </pre>


<p>鍵の一覧を確認する。</p>

<pre class="terminal"> % gpg --list-keys
/Users/hogehoge/.gnupg/pubring.kbx
----------------------------------------
pub   rsa2048 2020-07-24 [SC] [有効期限: 2022-06-24]
      8A558AEBED1F1005E593552266A1D81A25C544B6
uid                      hogeohge 
sub   rsa2048 2020-07-24 [E] [有効期限: 2022-06-24]

% gpg --list-secret-keys
/Users/hogehoge/.gnupg/pubring.kbx
----------------------------------------
pub   rsa2048 2020-07-24 [SC] [有効期限: 2022-06-24]
      8A558AEBED1F1005E593552266A1D81A25C544B6
uid                      hogeohge 
sub   rsa2048 2020-07-24 [E] [有効期限: 2022-06-24]
 </pre>


<p>作成したユーザの公開鍵、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C8%EB%CC%A9%B8%B0">秘密鍵</a>をエクスポートする。</p>

<pre class="terminal"> $ gpg -o ./hogehoge.public.gpg  --export hogehoge
 </pre>


<pre class="terminal"> $ gpg -o ./hogehoge.private.gpg --export-secret-key hogehoge
 </pre>


<p>公開鍵を<a class="keyword" href="http://d.hatena.ne.jp/keyword/Base64">Base64</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%F3%A5%B3%A1%BC%A5%C9">エンコード</a>する。</p>

<pre class="terminal"> % cat ./hogehoge.private.gpg | base64 | tr -d '\n' &gt;./hogehoge.private.gpg.base64
 </pre>


<h3>Terraformの<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a>_iam_user_login_profileや<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a>_iam_<a class="keyword" href="http://d.hatena.ne.jp/keyword/access">access</a>_keyのResourceの<a class="keyword" href="http://d.hatena.ne.jp/keyword/pgp">pgp</a>_keyへ<a class="keyword" href="http://d.hatena.ne.jp/keyword/PGP">PGP</a>公開鍵をセットする</h3>


<pre class="terminal"> # GPG鍵
variable "pgp_key" {
    default = "***** ... ******"
}

# IAMユーザー
resource "aws_iam_user" "hogehoge" {
    name = "hogehoge"
}

# アクセスキー
resource "aws_iam_access_key" "hogehoge" {
    user    = "${aws_iam_user.hogehoge.name}"
    pgp_key = "${var.pgp_key}"
}

resource "aws_iam_user_login_profile" "hogehoge" {
    user = "${aws_iam_user.hogehoge.name}"
    pgp_key = var.pgp_key
    password_reset_required = false
}

resource "aws_iam_user_policy_attachment" "admin" {
    user = "${aws_iam_user.hogehoge.name}"
    policy_arn = "arn:aws:iam::aws:policy/AdministratorAccess"
}

# シークレットキーを出力する
output "hogehoge_secret" {
    value = "${aws_iam_access_key.hogehoge.encrypted_secret}"
}

# パスワードを出力する
output "hogehoge_password" {
    value = "${aws_iam_user_login_profile.hogehoge.encrypted_password}"
}
 </pre>


<h3>セットした公開鍵を元に<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>側で暗号化したパスワードを返されるので確認する</h3>


<p>applyするとOutputで値を取得できる。</p>

<pre class="terminal"> % terraform apply
:

Apply complete! Resources: 7 added, 0 changed, 0 destroyed.

Outputs:

hogehoge_secret = ***** ... ******
hogehoge_password = ***** ... ******
 </pre>


<h3>暗号化データを<a class="keyword" href="http://d.hatena.ne.jp/keyword/PGP">PGP</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%C8%EB%CC%A9%B8%B0">秘密鍵</a>で復号する</h3>


<p>Outputの値を元にgpgコマンドで復号してパスワードを取得できる。</p>

<pre class="terminal"> % terraform output hogehoge_password  | base64 -d | gpg -r  hogehoge
gpg: *警告*: コマンドが指定されていません。なにを意味しているのか当ててみます ...
gpg: 2048-ビットRSA鍵, ID 0F51A1988DE59DB5, 日付2020-07-24に暗号化されました
      "hogeohge "
***************

% terraform output hogehoge_secret  | base64 -d | gpg -r  hogehoge
gpg: *警告*: コマンドが指定されていません。なにを意味しているのか当ててみます ...
gpg: 2048-ビットRSA鍵, ID 0F51A1988DE59DB5, 日付2020-07-24に暗号化されました
      "hogeohge "
******************************
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/PGP">PGP</a>の鍵はどこかに安全なところに保存してローカルのものは削除しておく。</p>

<pre class="terminal"> % rm *.gpg
 </pre>


<h3>参考</h3>


<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/docs/providers/aws/r/iam_access_key.html">AWS: aws_iam_access_key - Terraform by HashiCorp</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/docs/providers/aws/r/iam_user_login_profile.html">AWS: aws_iam_user_login_profile - Terraform by HashiCorp</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://akihiro-ob.hatenadiary.org/entry/20120131/1328031230">gpgでのファイルの暗号化基礎 - akihiro_obの日記</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="http://blog.father.gedow.net/2016/12/16/terraform-iam-user-password/">Terraform＋GPG で IAM User にログインパスワードを設定 | 外道父の匠</a></li>
</ul>

</body>
