---
title: "MFA 有効化時に AWS CLI で AssumeRole を実行する設定"
date: 2023-02-28
slug: "MFA 有効化時に AWS CLI で AssumeRole を実行する設定"
category: blog
tags: [Tech,AWS,AWS CLI]
---
<h2 id="はじめに">はじめに</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> を使う時に、MFA が設定されている IAM ユーザーのクレデンシャル情報を利用して、他の<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a>アカウントにある IAM Role を AssumeRole して使う(Switch Roleを実行する)設定について調べてやったことのメモ書きです。</p>

<p>MFA 用のプロファイルを定義して、コマンド実行に MFA のコードを入力するパターンと、MFA の認証処理をした一時認証情報を取得し、それを使って <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> を実行するパターンがあるようでした。
それぞれのやり方について試したことを書いてます。</p>

<h2 id="各-IAM-RolePolicy-の設定">各 IAM Role/Policy の設定</h2>

<h3 id="踏み台側の-IAM-Policy-の設定">踏み台側の IAM Policy の設定</h3>

<p>踏み台となる <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウントでは以下のようなポリシーを作成し、該当 IAM ユーザーにアタッチされているものとします。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>
    &quot;<span class="synStatement">Version</span>&quot;: &quot;<span class="synConstant">2012-10-17</span>&quot;,
    &quot;<span class="synStatement">Statement</span>&quot;: <span class="synSpecial">[</span>
        <span class="synSpecial">{</span>
            &quot;<span class="synStatement">Sid</span>&quot;: &quot;<span class="synConstant">VisualEditor0</span>&quot;,
            &quot;<span class="synStatement">Effect</span>&quot;: &quot;<span class="synConstant">Allow</span>&quot;,
            &quot;<span class="synStatement">Action</span>&quot;: &quot;<span class="synConstant">sts:AssumeRole</span>&quot;,
            &quot;<span class="synStatement">Resource</span>&quot;: &quot;<span class="synConstant">arn:aws:iam::123456789123:role/AdminRole</span>&quot;,
            &quot;<span class="synStatement">Condition</span>&quot;: <span class="synSpecial">{</span>
                &quot;<span class="synStatement">BoolIfExists</span>&quot;: <span class="synSpecial">{</span>
                    &quot;<span class="synStatement">aws:MultiFactorAuthPresent</span>&quot;: &quot;<span class="synConstant">true</span>&quot;
                <span class="synSpecial">}</span>
            <span class="synSpecial">}</span>
        <span class="synSpecial">}</span>
    <span class="synSpecial">]</span>
<span class="synSpecial">}</span>
</pre>


<p>Terraform だと以下のような感じになると思います。</p>

<pre class="code lang-tf" data-lang="tf" data-unlink><span class="synStatement">/</span>/ IAM ポリシー
resource &quot;<span class="synConstant">aws_iam_policy</span>&quot; &quot;<span class="synConstant">switch_role_policy</span>&quot; <span class="synSpecial">{</span>
  name   <span class="synStatement">=</span> &quot;<span class="synConstant">${aws_iam_group.switch_role_policy.name}</span>&quot;
  policy <span class="synStatement">=</span> &quot;<span class="synConstant">${data.aws_iam_policy_document.switch_role_policy.json}</span>&quot;
<span class="synSpecial">}</span>

<span class="synStatement">/</span>/ IAM ポリシードキュメント
data &quot;<span class="synConstant">aws_iam_policy_document</span>&quot; &quot;<span class="synConstant">switch_role_policy</span>&quot; <span class="synSpecial">{</span>
  statement <span class="synSpecial">{</span>
    <span class="synStatement">/</span>/ <span class="synIdentifier">123456789123</span> の AWS アカウントの AdminRole という IAM ロールを利用可能にする
    effect  <span class="synStatement">=</span> &quot;<span class="synConstant">Allow</span>&quot;
    actions <span class="synStatement">=</span> <span class="synSpecial">[</span>&quot;<span class="synConstant">sts:AssumeRole</span>&quot;<span class="synSpecial">]</span>
    resources <span class="synStatement">=</span> <span class="synSpecial">[</span>
      &quot;<span class="synConstant">arn:aws:iam::123456789123:role/AdminRole</span>&quot;,
    <span class="synSpecial">]</span>
    <span class="synStatement">/</span>/ MFA 認証されている場合に AssumeRole を許可する
    condition <span class="synSpecial">{</span>
      <span class="synStatement">test</span>     <span class="synStatement">=</span> &quot;<span class="synConstant">Bool</span>&quot;
      variable <span class="synStatement">=</span> &quot;<span class="synConstant">aws:MultiFactorAuthPresent</span>&quot;
      values   <span class="synStatement">=</span> <span class="synSpecial">[</span>&quot;<span class="synConstant">true</span>&quot;<span class="synSpecial">]</span>
    <span class="synSpecial">}</span>
  <span class="synSpecial">}</span>
<span class="synSpecial">}</span>
</pre>


<h3 id="アクセス先AWSアカウントの-IAM-Role-の設定">アクセス先<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a>アカウントの IAM Role の設定</h3>

<p>AdminRole という IAM ポリシーに、以下のような信頼ポリシードキュメントを設定します。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>
    &quot;<span class="synStatement">Version</span>&quot;: &quot;<span class="synConstant">2012-10-17</span>&quot;,
    &quot;<span class="synStatement">Statement</span>&quot;: <span class="synSpecial">[</span>
        <span class="synSpecial">{</span>
            &quot;<span class="synStatement">Effect</span>&quot;: &quot;<span class="synConstant">Allow</span>&quot;,
            &quot;<span class="synStatement">Principal</span>&quot;: <span class="synSpecial">{</span>
                &quot;<span class="synStatement">AWS</span>&quot;: &quot;<span class="synConstant">arn:aws:iam::987654321987:root</span>&quot;
            <span class="synSpecial">}</span>,
            &quot;<span class="synStatement">Action</span>&quot;: <span class="synSpecial">[</span>
                &quot;<span class="synConstant">sts:AssumeRole</span>&quot;,
                &quot;<span class="synConstant">sts:TagSession</span>&quot;
            <span class="synSpecial">]</span>
        <span class="synSpecial">}</span>
    <span class="synSpecial">]</span>
<span class="synSpecial">}</span>
</pre>


<p>Terraform だと以下のような感じになると思います。</p>

<pre class="code lang-tf" data-lang="tf" data-unlink><span class="synStatement">/</span>/ IAM ロール
resource &quot;<span class="synConstant">aws_iam_role</span>&quot; &quot;<span class="synConstant">admin</span>&quot; <span class="synSpecial">{</span>
  name               <span class="synStatement">=</span> &quot;<span class="synConstant">AdminRole</span>&quot;
  assume_role_policy <span class="synStatement">=</span> &quot;<span class="synConstant">${data.aws_iam_policy_document.assume_role_policy.json}</span>&quot;
<span class="synSpecial">}</span>

<span class="synStatement">/</span>/ IAM ポリシードキュメント 
data &quot;<span class="synConstant">aws_iam_policy_document</span>&quot; &quot;<span class="synConstant">assume_role_policy</span>&quot; <span class="synSpecial">{</span>
  statement <span class="synSpecial">{</span>
    effect  <span class="synStatement">=</span> &quot;<span class="synConstant">Allow</span>&quot;
    actions <span class="synStatement">=</span> <span class="synSpecial">[</span>&quot;<span class="synConstant">sts:AssumeRole</span>&quot;<span class="synSpecial">]</span>

    principals <span class="synSpecial">{</span>
      type        <span class="synStatement">=</span> &quot;<span class="synConstant">AWS</span>&quot;
      identifiers <span class="synStatement">=</span> <span class="synSpecial">[</span>&quot;<span class="synConstant">arn:aws:iam::987654321987:root</span>&quot;<span class="synSpecial">]</span>
    <span class="synSpecial">}</span>

    condition <span class="synSpecial">{</span>
      <span class="synStatement">test</span>     <span class="synStatement">=</span> &quot;<span class="synConstant">Bool</span>&quot;
      variable <span class="synStatement">=</span> &quot;<span class="synConstant">aws:MultiFactorAuthPresent</span>&quot;
      values   <span class="synStatement">=</span> <span class="synSpecial">[</span>&quot;<span class="synConstant">true</span>&quot;<span class="synSpecial">]</span>
    <span class="synSpecial">}</span>
  <span class="synSpecial">}</span>
<span class="synSpecial">}</span>
</pre>


<h3 id="1つ目のやり方-aws-profile-に-mfa_serial-を定義する">1つ目のやり方: <a class="keyword" href="https://d.hatena.ne.jp/keyword/aws">aws</a> profile に mfa_serial を定義する</h3>

<p><code>~/.aws/credentials</code> を以下のように設定します。</p>

<pre class="code" data-lang="" data-unlink>[default]
aws_access_key_id = ********************
aws_secret_access_key = ***************************************</pre>


<p><code>~/.aws/config</code> を以下のように設定します。</p>

<pre class="code txt" data-lang="txt" data-unlink>[default]
region = ap-northeast-1
output = json

[profile dev]
region = ap-northeast-1
output = json
role_arn = arn:aws:iam::123456789123:role/AdminRole  # アクセス先の AWS アカウントに作成しているロール
mfa_serial = arn:aws:iam::987654321987:mfa/dshimizu  # 踏み台 AWS アカウントに作成している IAM ユーザーの MFA の ARN
source_profile = default  # default プロファイルの認証情報を利用する設定</pre>


<p>この状態で、dev のプロファイルをつかって <code>123456789123</code> のアカウントのリソースにアクセスするために <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> を実行すると、MFA のコードを要求するプロンプトが表示されます。
これを入力すると、S3 の一覧の結果が得られます。</p>

<pre class="code shell" data-lang="shell" data-unlink> % aws --profile dev s3 ls
Enter MFA code for arn:aws:iam::987654321987:mfa/dshimizu:</pre>


<p>このとき、<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> のキャッシュ情報を見ると、以下のようなものが記録されていました。</p>

<pre class="code" data-lang="" data-unlink>% cat ~/.aws/cli/cache/************.json | jq
{
  &#34;Credentials&#34;: {
    &#34;AccessKeyId&#34;: &#34;********************&#34;,
    &#34;SecretAccessKey&#34;: &#34;****************************************&#34;,
    &#34;SessionToken&#34;: &#34;************************************************************************************************************************&#34;,
    &#34;Expiration&#34;: &#34;2023-02-03T11:26:11+00:00&#34;
  },
  &#34;AssumedRoleUser&#34;: {
    &#34;AssumedRoleId&#34;: &#34;****************:botocore-session-123456789123&#34;,
    &#34;Arn&#34;: &#34;arn:aws:sts::123456789123:assumed-role/AdminRole/botocore-session-123456789123&#34;
  },
  &#34;ResponseMetadata&#34;: {
    &#34;RequestId&#34;: &#34;********-****-****-****-************&#34;,
    &#34;HTTPStatusCode&#34;: 200,
    &#34;HTTPHeaders&#34;: {
      &#34;x-amzn-requestid&#34;: &#34;********-****-****-****-************&#34;,
      &#34;content-type&#34;: &#34;text/xml&#34;,
      &#34;content-length&#34;: &#34;1520&#34;,
      &#34;date&#34;: &#34;Fri, 03 Feb 2023 10:26:10 GMT&#34;
    },
    &#34;RetryAttempts&#34;: 0
  }
}</pre>


<p>ドキュメントを見ると、下記のようにあり、指定されたロールの一時認証情報が取得されるとのことなので、キャッシュに記録されているのはその情報のようです。</p>

<blockquote><p>When you specify that an <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> command is to use the profile marketingadmin, the <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> automatically looks up the credentials for the linked user1 profile and uses them to request temporary credentials for the specified IAM role. The <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> uses the <a class="keyword" href="https://d.hatena.ne.jp/keyword/sts">sts</a>:AssumeRole operation in the background to accomplish this. Those temporary credentials are then used to run the requested <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> command. The specified role must have attached IAM permission policies that allow the requested <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> command to run.</p></blockquote>

<h3 id="2つ目のやり方-aws-profile-に一時認証情報を定義する">2つ目のやり方: <a class="keyword" href="https://d.hatena.ne.jp/keyword/aws">aws</a> profile に一時認証情報を定義する</h3>

<p>一時認証情報を利用するやり方をやってみます。</p>

<p><code>~/.aws/credentials</code> を以下のように設定します。</p>

<pre class="code" data-lang="" data-unlink>[default]
aws_access_key_id = ********************
aws_secret_access_key = ****************************************</pre>


<p><code>~/.aws/config</code> を以下のように設定します。</p>

<pre class="code txt" data-lang="txt" data-unlink>[default]
region = ap-northeast-1
output = json</pre>


<p>以下のコマンドで、IAM ユーザーの一時認証情報を取得します。<code>get-session-token</code> の <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> を実行するためのアクセス権限設定は不要なようなので、何もしなくても実行できると思います。</p>

<pre class="code shell" data-lang="shell" data-unlink>% aws --profile default sts get-session-token --serial-number arn:aws:iam::987654321987:mfa/dshimizu --token-code &lt;MFA の 6桁の番号を指定&gt;</pre>


<p>以下のような一時認証情報用のアクセスキー、シークレットキー、セッション<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C8%A1%BC%A5%AF">トーク</a>ンが返されます。
これで MFA 認証が通った状態の一時認証情報を取得できました。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>
  &quot;<span class="synStatement">Credentials</span>&quot;: <span class="synSpecial">{</span>
    &quot;<span class="synStatement">AccessKeyId</span>&quot;: &quot;<span class="synConstant">********************</span>&quot;,
    &quot;<span class="synStatement">SecretAccessKey</span>&quot;: &quot;<span class="synConstant">****************************************</span>&quot;,
    &quot;<span class="synStatement">SessionToken</span>&quot;: &quot;<span class="synConstant">************************************************************************************************************************</span>&quot;,
    &quot;<span class="synStatement">Expiration</span>&quot;: &quot;<span class="synConstant">2023-02-02T22:13:39+00:00</span>&quot;
  <span class="synSpecial">}</span>
<span class="synSpecial">}</span>
</pre>


<p>取得した一時認証情報を <code>~/.aws/credentials</code> へ追記します。</p>

<pre class="code" data-lang="" data-unlink>[default]
aws_access_key_id = ********************
aws_secret_access_key = ****************************************

[mfa]
aws_access_key_id = ********************
aws_secret_access_key = ****************************************
aws_session_token = ************************************************************************************************************************</pre>


<p><code>~/.aws/config</code> では、以下のように Assume する Role に対して一時認証情報を利用するように <code>source_profile</code> を定義します。</p>

<pre class="code txt" data-lang="txt" data-unlink>[default]
region = ap-northeast-1
output = json

[profile mfa]
region = ap-northeast-1
output = json
source_profile = mfa
role_arn = arn:aws:iam::123456789123:role/AdminRole</pre>


<p>これで <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> が実行できるようになりました。
MFAの認証は通っているので、6桁のコードを入力することなく実行できます。</p>

<pre class="code shell" data-lang="shell" data-unlink> % aws --profile dev s3 ls
:
:</pre>


<h2 id="まとめ">まとめ</h2>

<p>MFA が設定されている IAM ユーザーのクレデンシャル情報を利用して、他の<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a>アカウントにある IAM Role を AssumeRole して使う(Switch Roleを実行する)設定について書きました。</p>

<p>ドキュメントを見る限りですと、 <code>get-session-token</code> の <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> が、 MFA 認証する場合のために用意されているためのもののようですので、これを利用するのが良さそうです。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-role.html#cli-configure-role-mfa">Use an IAM role in the AWS CLI - AWS Command Line Interface</a></li>
<li><a href="https://aws.amazon.com/jp/premiumsupport/knowledge-center/authenticate-mfa-cli/">AWS CLI &#x7D4C;&#x7531;&#x3067; MFA &#x3092;&#x4F7F;&#x7528;&#x3057;&#x3066;&#x30A2;&#x30AF;&#x30BB;&#x30B9;&#x3092;&#x8A8D;&#x8A3C;&#x3059;&#x308B; | AWS re:Post</a></li>
<li><a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_control-access_getsessiontoken.html">Permissions for GetSessionToken - AWS Identity and Access Management</a></li>
</ul>


