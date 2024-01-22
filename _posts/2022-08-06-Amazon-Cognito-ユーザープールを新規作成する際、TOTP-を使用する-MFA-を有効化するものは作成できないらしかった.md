---
title: "Amazon Cognito ユーザープールを新規作成する際、TOTP を使用する MFA を有効化するものは作成できないらしかった"
date: 2022-08-06
slug: "Amazon Cognito ユーザープールを新規作成する際、TOTP を使用する MFA を有効化するものは作成できないらしかった"
category: blog
tags: [Tech,AWS,Amazon Cognito]
---
<h2 id="はじめに">はじめに</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> で TOTP を使用する MFA を有効化する場合の Cognito ユーザープールを作成しようとするとエラーになった。
どうも現時点では、ユーザープール新規作成時に、TOTP を使用する MFA 必須の状態のものは作成できないらしいかった。その際の対応方法を書いておく。</p>

<h2 id="環境">環境</h2>

<pre class="code" data-lang="" data-unlink>% aws --version
aws-cli/2.1.13 Python/3.7.4 Darwin/19.6.0 exe/x86_64 prompt/of</pre>


<h2 id="ユーザープールの作成時の---mfa-configuration-ON-でエラー">ユーザープールの作成時の <code>--mfa-configuration ON</code> でエラー</h2>

<p>適当に以下のようなユーザープールを作成する。</p>

<pre class="code" data-lang="" data-unlink>% aws cognito-idp create-user-pool --pool-name sample-user-pool --schema Name=user_id,AttributeDataType=String,Mutable=true --mfa-configuration ON</pre>


<p>その際に <code>--mfa-configuration ON</code> を指定すると以下のようなエラーが発生する。</p>

<pre class="code" data-lang="" data-unlink>An error occurred (InvalidParameterException) when calling the CreateUserPool operation: SMS configuration and Auto verification for phone_number are required when MFA is required/optional</pre>


<p>TOTP を利用したいので電話番号とかいらないのだが...と思いつつ help を見ても解消方法がわからなかった。</p>

<h2 id="解消方法">解消方法</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> の Issue に以下があった。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Faws%2Faws-cli%2Fissues%2F3876%23issuecomment-456998093" title="Cognito CreateUserPool doesn’t seem to respect AutoVerifiedAttributes value · Issue #3876 · aws/aws-cli" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" loading="lazy"></iframe><cite class="hatena-citation"><a href="https://github.com/aws/aws-cli/issues/3876#issuecomment-456998093">github.com</a></cite></p>

<p>どうも、ユーザープール作成時には SMS を使用すると見なされるようなので、TOTP を利用する場合はユーザープール作成後に <code>SetUserPoolMfaConfig</code> <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> を使って設定変更しよう、ということらしい。
ということでやってみた。</p>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> だと以下のようなコマンドになる。<a href="#f-698adac2" id="fn-698adac2" name="fn-698adac2" title="[https://awscli.amazonaws.com/v2/documentation/api/latest/reference/cognito-idp/set-user-pool-mfa-config.html:title]">*1</a></p>

<pre class="code" data-lang="" data-unlink>% aws  cognito-idp set-user-pool-mfa-config --user-pool-id ap-northeast-1_*********** --mfa-configuration ON --software-token-mfa-configuration Enabled=true</pre>


<p>以下のような出力になる。</p>

<pre class="code" data-lang="" data-unlink>{
    &#34;SoftwareTokenMfaConfiguration&#34;: {
        &#34;Enabled&#34;: true
    },
    &#34;MfaConfiguration&#34;: &#34;ON&#34;
}</pre>


<h2 id="まとめ">まとめ</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Cognito ユーザープールを新規作成する際、TOTP を使用する場合の MFA 必須のものは作成できないらしかったので、回避策(という表現が正しいのかわからないが)として、新規作成後に設定変更する、というやり方について書いた。
今回は<a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a>の場合について書いたが、CloudFormationとかの場合でも多分ダメそうと思うので、TOTPの場合はまずユーザープール作成して、その後に変更するという運用をするしかなさそう。</p>
<div class="footnote">
<p class="footnote"><a href="#fn-698adac2" id="f-698adac2" name="f-698adac2" class="footnote-number">*1</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://awscli.amazonaws.com/v2/documentation/api/latest/reference/cognito-idp/set-user-pool-mfa-config.html">set-user-pool-mfa-config &mdash; AWS CLI 2.15.10 Command Reference</a></span></p>
</div>
