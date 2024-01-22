---
title: "AWS SDK for Go v2 で Assume Role を実行するサンプル"
date: 2023-03-05
slug: "AWS SDK for Go v2 で Assume Role を実行するサンプル"
category: blog
tags: [AWS,AWS SDK for Go,Go]
---
<h2 id="はじめに">はじめに</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/SDK">SDK</a> for Go v2 で Assume Role を実行しようと思ってのサンプルコードです。</p>

<h2 id="準備">準備</h2>

<h3 id="AWS-SDK-for-Go-v2-インストール"><a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/SDK">SDK</a> for Go v2 インストール</h3>

<p>作業<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成します。</p>

<pre class="code" data-lang="" data-unlink>$ mkdir ~/hello-aws-sdk-go-v2

$ cd ~/hello-aws-sdk-go-v2

$ go mod init hello-aws-sdk-go-v2</pre>


<p>パッケージをインストールします。</p>

<pre class="code" data-lang="" data-unlink>$ go get github.com/aws/aws-sdk-go-v2/aws

$ go get github.com/aws/aws-sdk-go-v2/config

$ go get github.com/aws/aws-sdk-go-v2/credentials/stscreds

$ go get github.com/aws/aws-sdk-go-v2/service/sts</pre>


<p>利用したい <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> サービスに合わせて必要なものをインストールします。ここでは EC2 の一覧を取得しようとしているため、 ec2 のモジュールをインストールします。</p>

<pre class="code" data-lang="" data-unlink>$ go get github.com/aws/aws-sdk-go-v2/service/ec2</pre>


<h3 id="AWS-CLI-用の-Configuration-and-credentia-の設定"><a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> 用の Configuration and credentia の設定</h3>

<p>ローカルで下記のような設定がなされているものとします。</p>

<pre class="code lang-config" data-lang="config" data-unlink><span class="synComment"># ~/.aws/credentials</span>
<span class="synComment"># IAMユーザーのアクセスキー情報が foo という名前のprofileで定義されている</span>

<span class="synSpecial">[</span>foo<span class="synSpecial">]</span>
aws_access_key_id<span class="synStatement">=**********</span>
aws_secret_access_key<span class="synStatement">=******************************</span>
</pre>




<pre class="code lang-config" data-lang="config" data-unlink><span class="synComment"># ~/.aws/config</span>

<span class="synSpecial">[</span>profile foo<span class="synSpecial">]</span>
region<span class="synStatement">=</span>ap-northeast<span class="synConstant">-1</span>
</pre>


<h3 id="アクセス元-AWS-アカウントでの-IAM-Policy-の設定">アクセス元 <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウントでの IAM Policy の設定</h3>

<p>アクセス元の <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウント(仮に <code>987654321098</code> とする)の IAM ユーザーに、 <code>"arn:aws:iam:123456789012:role/TestRole"</code> のロールを利用できる IAM ポリシーが作成され、アタッチされているものとします。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>
    &quot;<span class="synStatement">Version</span>&quot;: &quot;<span class="synConstant">2012-10-17</span>&quot;,
    &quot;<span class="synStatement">Statement</span>&quot;: <span class="synSpecial">[</span>
        <span class="synSpecial">{</span>
            &quot;<span class="synStatement">Action</span>&quot;: <span class="synSpecial">[</span>
                &quot;<span class="synConstant">sts:AssumeRole</span>&quot;,
                &quot;<span class="synConstant">sts:TagSession</span>&quot;
            <span class="synSpecial">]</span>,
            &quot;<span class="synStatement">Resource</span>&quot;: <span class="synSpecial">[</span>
                &quot;<span class="synConstant">arn:aws:iam::123456789012:role/TestRole</span>&quot;<span class="synError">,</span>
<span class="synError">            ]</span>,
            &quot;<span class="synStatement">Effect</span>&quot;: &quot;<span class="synConstant">Allow</span>&quot;
        }
    ]
}
</pre>


<h3 id="アクセス先-AWS-アカウントでの-IAM-Role-の設定">アクセス先 <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウントでの IAM Role の設定</h3>

<p>アクセスしたい <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウント(仮に <code>123456789012</code> とする)に <code>"arn:aws:iam::123456789012:role/TestRole"</code> という IAM ロールが作成されているものとします。
この IAM ロールには、下記のように、アクセス元となる <code>987654321098</code> の <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> アカウントから <code>sts:AssumeRole</code>, <code>sts:TagSession</code> を許可する旨の「信頼ポリシー」が設定されているものとします。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>
    &quot;<span class="synStatement">Version</span>&quot;: &quot;<span class="synConstant">2012-10-17</span>&quot;,
    &quot;<span class="synStatement">Statement</span>&quot;: <span class="synSpecial">[</span>
        <span class="synSpecial">{</span>
            &quot;<span class="synStatement">Effect</span>&quot;: &quot;<span class="synConstant">Allow</span>&quot;,
            &quot;<span class="synStatement">Principal</span>&quot;: <span class="synSpecial">{</span>
                &quot;<span class="synStatement">AWS</span>&quot;: &quot;<span class="synConstant">arn:aws:iam::987654321098:root</span>&quot;
            <span class="synSpecial">}</span>,
            &quot;<span class="synStatement">Action</span>&quot;: <span class="synSpecial">[</span>
                &quot;<span class="synConstant">sts:AssumeRole</span>&quot;,
                &quot;<span class="synConstant">sts:TagSession</span>&quot;
            <span class="synSpecial">]</span>
        <span class="synSpecial">}</span>
    <span class="synSpecial">]</span>
<span class="synSpecial">}</span>
</pre>


<p>また、下記のサンプルでは EC2 の一覧を表示する処理を実行するようにしているため、<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> 管理ポリシーである <code>arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess</code>  の IAM ポリシーも付与しておきます。</p>

<h2 id="サンプルコード">サンプルコード</h2>

<p>下記のような <code>main.go</code> を作成します。</p>

<pre class="code lang-go" data-lang="go" data-unlink><span class="synStatement">package</span> main

<span class="synStatement">import</span> (
    <span class="synConstant">&quot;context&quot;</span>
    <span class="synConstant">&quot;log&quot;</span>

    <span class="synConstant">&quot;github.com/aws/aws-sdk-go-v2/aws&quot;</span>
    <span class="synConstant">&quot;github.com/aws/aws-sdk-go-v2/config&quot;</span>
    <span class="synConstant">&quot;github.com/aws/aws-sdk-go-v2/credentials/stscreds&quot;</span>
    <span class="synConstant">&quot;github.com/aws/aws-sdk-go-v2/service/sts&quot;</span>

    <span class="synConstant">&quot;github.com/aws/aws-sdk-go-v2/service/ec2&quot;</span>
)

<span class="synStatement">func</span> main() {

    ctx := context.TODO()
    cfg, err := config.LoadDefaultConfig(ctx,
        config.WithRegion(<span class="synConstant">&quot;ap-northeast-1&quot;</span>),
        config.WithSharedConfigProfile(<span class="synConstant">&quot;foo&quot;</span>),  <span class="synComment">// ~/.aws/config のプロファイル名に合わせる</span>
    )
    <span class="synStatement">if</span> err != <span class="synStatement">nil</span> {
        log.Fatal(err)
    }

        <span class="synComment">// 利用するのロール名を設定する</span>
    roleArn := <span class="synConstant">&quot;arn:aws:iam::*************:role/********&quot;</span>

    stsSvc := sts.NewFromConfig(cfg)
    creds := stscreds.NewAssumeRoleProvider(stsSvc, roleArn)
    cfg.Credentials = aws.NewCredentialsCache(creds)


        <span class="synComment">// ここから実行したい処理を記述する</span>
        <span class="synComment">// 下記では EC2 の一覧を取得している</span>
    cli := ec2.NewFromConfig(cfg)

    resp, err := cli.DescribeInstances(ctx, &amp;ec2.DescribeInstancesInput{})
    <span class="synStatement">if</span> err != <span class="synStatement">nil</span> {
        log.Fatal(err)
    }

    <span class="synStatement">for</span> _, r := <span class="synStatement">range</span> resp.Reservations {
        <span class="synStatement">for</span> _, ins := <span class="synStatement">range</span> r.Instances {
            log.Printf(<span class="synConstant">&quot;%s %v&quot;</span>, *ins.InstanceId, ins.InstanceType)
        }
    }

    <span class="synComment">// second time, not need to input MFA code because credential is cached.</span>
    resp, err = cli.DescribeInstances(ctx, &amp;ec2.DescribeInstancesInput{})
    <span class="synStatement">if</span> err != <span class="synStatement">nil</span> {
        log.Fatal(err)
    }

    <span class="synStatement">for</span> _, r := <span class="synStatement">range</span> resp.Reservations {
        <span class="synStatement">for</span> _, ins := <span class="synStatement">range</span> r.Instances {
            log.Printf(<span class="synConstant">&quot;%s %v&quot;</span>, *ins.InstanceId, ins.InstanceType)
        }
    }
}
</pre>


<h2 id="参考">参考</h2>

<ul>
<li><a href="https://docs.aws.amazon.com/code-library/latest/ug/go_2_iam_code_examples.html">IAM examples using SDK for Go V2 - AWS SDK Code Examples</a></li>
<li><a href="https://pkg.go.dev/github.com/aws/aws-sdk-go-v2/credentials/stscreds#hdr-Assume_Role">stscreds package - github.com/aws/aws-sdk-go-v2/credentials/stscreds - Go Packages</a></li>
<li><a href="https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/access_policies.html">IAM &#x3067;&#x306E;&#x30DD;&#x30EA;&#x30B7;&#x30FC;&#x3068;&#x30A2;&#x30AF;&#x30BB;&#x30B9;&#x8A31;&#x53EF; - AWS Identity and Access Management</a></li>
</ul>


