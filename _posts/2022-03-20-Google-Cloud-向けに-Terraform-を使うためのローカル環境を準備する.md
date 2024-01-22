---
title: "Google Cloud 向けに Terraform を使うためのローカル環境を準備する"
date: 2022-03-20
slug: "Google Cloud 向けに Terraform を使うためのローカル環境を準備する"
category: blog
tags: [Tech]
---
<h2 id="はじめに">はじめに</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud の環境構築を Terraform で行いたいので、Terraform を実行するにあたってどういう風に設定すれば良いのか調べた。</p>

<h2 id="事前準備">事前準備</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud は利用可能になっていることとする。</p>

<h2 id="Google-Cloud-用の-Provider-設定"><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud 用の Provider 設定</h2>

<p>Terraform の <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Provider という<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>を定義する必要がある。
tf ファイルに以下のような感じのことを記述する。<code>provider.tf</code>  とかファイル名は何でも良い。私は <code>main.tf</code> にまとめている。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Provider に、Terraform を利用する <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud プロジェクトとデフォルトリージョン、ゾーンを設定する。</p>

<p>Provider バージョンは運用方針によると思うけど、互換性のある最小バージョンにするのが良さそう。ここでは <code>4.0.0</code> 以上としている。</p>

<pre class="code lang-tf" data-lang="tf" data-unlink>provider &quot;<span class="synConstant">google</span>&quot; <span class="synSpecial">{</span>
  project <span class="synStatement">=</span> &quot;<span class="synConstant">hogehoge-prj</span>&quot;
  region  <span class="synStatement">=</span> &quot;<span class="synConstant">asia-northeast1</span>&quot;
  zone    <span class="synStatement">=</span> &quot;<span class="synConstant">asia-northeast1-a</span>&quot;
<span class="synSpecial">}</span>

terraform <span class="synSpecial">{</span>
  required_providers <span class="synSpecial">{</span>
    google <span class="synStatement">=</span> <span class="synSpecial">{</span>
      <span class="synStatement">version</span> <span class="synStatement">=</span> &quot;<span class="synConstant">~&gt; 4.0.0</span>&quot;
    <span class="synSpecial">}</span>
  <span class="synSpecial">}</span>
<span class="synSpecial">}</span>
</pre>


<h3 id="Terraform-を実行する-Google-Cloud-プロジェクトの指定">Terraform を実行する <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud プロジェクトの指定</h3>

<p>ローカル実行するには <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud の <a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a> を利用できるようにしておく必要がある。</p>

<p>以下のいずれかを利用する必要がある。</p>

<ul>
<li>サービスアカウントを使う</li>
<li>個人ユーザーアカウント(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> アカウント)を使う(Cloud <a class="keyword" href="http://d.hatena.ne.jp/keyword/SDK">SDK</a>)</li>
</ul>


<p>CI を行う場合はサービスアカウントを利用する感じになると思うが、サービスアカウントだと、複数人で Terraform を利用する場合、各々がローカルで実行する時にアカウント情報を使い回すことになるので、ローカル実行時は自身のアカウントを使いたい、といったケースもありそうだなと思い、自分自身の個人ユーザーアカウントを使うこととする。</p>

<p>この場合、個人ユーザーアカウントで認証することで、<code>~/.config/gcloud/application_default_credentials.json</code> (Application Default Credentials (ADC)) が作成され、Terraform の <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Provider (多分 Cloud <a class="keyword" href="http://d.hatena.ne.jp/keyword/SDK">SDK</a>)がこれを使うようになる。</p>

<h3 id="プロジェクト設定">プロジェクト設定</h3>

<p>まず、Terraform を実行する対象となる <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud プロジェクトの設定をする。
プロジェクトが Terraform を実行したいプロジェクトではない場合、指定する。</p>

<pre class="code bash" data-lang="bash" data-unlink>% gcloud config set project hogehoge-prj</pre>


<h3 id="Google-Cloud-認証用の設定"><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud 認証用の設定</h3>

<p><code>init</code> を実行する。</p>

<pre class="code bash" data-lang="bash" data-unlink>% terraform init

Initializing the backend...

Initializing provider plugins...
- Finding latest version of hashicorp/google...
- Installing hashicorp/google v4.14.0...
- Installed hashicorp/google v4.14.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run &#34;terraform init&#34; in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running &#34;terraform plan&#34; to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.</pre>


<p>そのまま何もせず実行しようとすると <code>plan</code> 実行時に以下のようなエラーになる。</p>

<pre class="code bash" data-lang="bash" data-unlink>% terraform plan
╷
│ Error: Attempted to load application default credentials since neither `credentials` nor `access_token` was set in the provider block.  No credentials loaded. To use your gcloud credentials, run &#39;gcloud auth application-default login&#39;.  Original error: google: could not find default credentials. See https://developers.google.com/accounts/docs/application-default-credentials for more information.
│ 
│   with provider[&#34;registry.terraform.io/hashicorp/google&#34;],
│   on main.tf line 2, in provider &#34;google&#34;:
│    2: provider &#34;google&#34; {
│ </pre>


<p>Terraform を介して認証を突破できる状態にないため、メッセージにある通り、<code>gcloud auth application-default login</code> を実行する。</p>

<pre class="code bash" data-lang="bash" data-unlink>% gcloud auth application-default login</pre>


<p>ブラウザが起動し、認証許可画面が出るので、そのまま許可する。
以下のようなクレデンシャル情報を保持したファイルが生成される。これで認証できるようになる。</p>

<pre class="code bash" data-lang="bash" data-unlink>% cat ~/.config/gcloud/application_default_credentials.json
{
  &#34;client_id&#34;: &#34;7**********0-********************************.apps.googleusercontent.com&#34;,
  &#34;client_secret&#34;: &#34;d-******************Ty&#34;,
  &#34;quota_project_id&#34;: &#34;hogehoge-prj&#34;,
  &#34;refresh_token&#34;: &#34;1//**********************************************************2qo&#34;,
  &#34;type&#34;: &#34;authorized_user&#34;
}</pre>


<h2 id="実行">実行</h2>

<p>これで実行できるようになる。</p>

<pre class="code bash" data-lang="bash" data-unlink>% terraform plan</pre>


<h2 id="まとめ">まとめ</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud で Terraform を実行するためのローカル環境構築について雑にまとめた。
あとは tfstate を GCS に置くようにしたほうが良い。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://cloud.google.com/sdk/gcloud/reference/auth/application-default/login">gcloud auth application-default login &nbsp;|&nbsp; Google Cloud CLI Documentation</a></li>
<li><a href="https://registry.terraform.io/providers/hashicorp/google/latest/docs/guides/getting_started">Terraform Registry</a></li>
<li><a href="https://qiita.com/fetaro/items/f44d47191fff9f7b10e4">[GCP&#x8A8D;&#x8A3C;] &#x30D7;&#x30ED;&#x30B0;&#x30E9;&#x30DF;&#x30F3;&#x30B0;&#x8A00;&#x8A9E;&#x306E;&#x30AF;&#x30E9;&#x30A4;&#x30A2;&#x30F3;&#x30C8;&#x30E9;&#x30A4;&#x30D6;&#x30E9;&#x30EA;&#x304B;&#x3089;&#x30E6;&#x30FC;&#x30B6;&#x30A2;&#x30AB;&#x30A6;&#x30F3;&#x30C8;&#x3092;&#x7528;&#x3044;&#x3066;&#x8A8D;&#x8A3C;&#x3059;&#x308B;&#x65B9;&#x6CD5; - Qiita</a></li>
</ul>


