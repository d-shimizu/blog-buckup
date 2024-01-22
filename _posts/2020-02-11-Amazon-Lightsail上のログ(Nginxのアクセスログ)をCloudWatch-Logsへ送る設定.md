---
layout: post
Categories:  ["tech"]
Description:  " Amazon Lightsailで動かしているAmazon Linuxの中にあるログをCloudWatch Logsへ送りたい、と思って調べたらやれたのでメモしておく。 "
Tags: ["AWS", "Lightsail", "tech"]
date: "2020-02-11T18:13:00+09:00"
title: "Amazon Lightsail上のログ(Nginxのアクセスログ)をCloudWatch Logsへ送る設定"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Lightsailで動かしている<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/Linux">Linux</a>の中にあるログをCloudWatch Logsへ送りたい、と思って調べたらやれたのでメモしておく。</p>
</body>

<!-- more -->

<body>
<h3>TL;DR</h3>


<p>箇条書きでまとめると以下を行う必要がある。</p>

<ul>
  <li>Ligtsailで起動したOSへのCloudWatch Agentのインストール</li>
  <li>CloudWatch Agentで使うIAMユーザの作成</li>
  <li>CloudWatch Agentの設定</li>
</ul>




<h3>CloudWatch Agentのインストール</h3>


<p>インストールの方法はいくつかあるが、公式ドキュメントの通り<a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>でパッケージをインストールする。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/installing-cloudwatch-agent-commandline.html">コマンドラインを使用して CloudWatch エージェントをインストールする</a></li>
</ul>




<pre class="terminal"> $ sudo rpm -ihv https://s3.amazonaws.com/amazoncloudwatch-agent/amazon_linux/amd64/latest/amazon-cloudwatch-agent.rpm
 </pre>




<h3>CloudWatch Agentで使うIAMユーザの作成</h3>


<p>Lightsail上のCloudWatch Agentで使うIAMユーザを作成して、IAMの<a class="keyword" href="http://d.hatena.ne.jp/keyword/Access">Access</a> KeyとSecret Keyを作成する。
普通のEC2ならIAMロールを割り当てれば良いが、Lightsailでは使えないので、IAMの<a class="keyword" href="http://d.hatena.ne.jp/keyword/Access">Access</a> KeyとSecret Keyも作成する必要がある</p>

<p>作成の仕方は以下にあるドキュメントの「CloudWatch エージェントをオンプレミスサーバーで実行するために必要な IAM ユーザーを作成するには」の通り。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/create-iam-roles-for-cloudwatch-agent-commandline.html">CloudWatch エージェントで使用する IAM ロールおよびユーザーを作成する</a></li>
</ul>




<h3>CloudWatch Agentの設定</h3>


<p>CloudWatch Agentの設定を行う。</p>

<h4>common-config.tomlの設定</h4>


<p>ドキュメントは下記にあるのでこれを参考にする。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/create-cloudwatch-agent-configuration-file.html">CloudWatch エージェント設定ファイルを作成する</a></li>
</ul>


<p>まずは<code>/opt/aws/amazon-cloudwatch-agent/etc/common-config.toml</code>の<code>credentials</code>の項目を編集し、上記で作成したIAMユーザの<a class="keyword" href="http://d.hatena.ne.jp/keyword/Access">Access</a> KeyとSecret KeyをCloudWatch Agentから使用できるようにしておく。</p>

<p>例えば以下のようにする。
credentialsファイルの場所やprofileは環境に合わせて変更すれば良い。
<code>shared_credential_file</code>には上記で作成したIAMユーザの<a class="keyword" href="http://d.hatena.ne.jp/keyword/Access">Access</a> KeyとSecret Keyを記載したファイルを設置しておく。</p>

<pre class="terminal"> [credentials]
    shared_credential_profile = "default"
    shared_credential_file = "/opt/aws/.aws/credentials"
 </pre>


<p><code>/opt/aws/amazon-cloudwatch-agent/etc/common-config.toml</code>はプロキシやアクセス権の設定をするためのもののようだった。</p>

<h4>config.<a class="keyword" href="http://d.hatena.ne.jp/keyword/json">json</a>の設定</h4>


<p><code>/opt/aws/amazon-cloudwatch-agent/bin/config.json</code>の設定ファイルを作成する。</p>

<p><code>/opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard</code>というコマンドで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%E9%A5%AF%A5%C6%A5%A3%A5%D6">インタラクティブ</a>に設定ファイルを作成することもできるが、ここではNginxのログを送るだけなので手動で設定する。</p>

<p>設定ファイルは以下のようになる。</p>

<pre class="terminal"> {
    "agent": {
        "metrics_collection_interval": 60,
        "run_as_user": "cwagent"
    },
    "logs": {
        "logs_collected": {
            "files": {
                "collect_list": [
                    {
                        "file_path": "/opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log",
                        "log_group_name": "/aws/lightsail/amazon-cloudwatch-agent.log",
                        "log_stream_name": "{hostname}"
                    },
                    {
                        "file_path": "/var/log/nginx/ssl_access.log.ltsv",
                        "log_group_name": "/aws/lightsail/accesslog",
                        "log_stream_name": "{hostname}"
                    }
                ]
            }
        }
    }
}
 </pre>


<p>設定ファイルを反映するために以下のコマンドを実行する。</p>

<pre class="terminal"> $ sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m onPremise -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json -s
 </pre>


<p>これでしばらく待ってCloudWatch Logsへログが送られてくることを確認する。
ログが送られない場合は<code>/var/log/amazon/amazon-cloudwatch-agent/amazon-cloudwatch-agent.log</code>を確認すれば何か手がかりがあると思う。</p>

<h3>まとめ</h3>


<p>LightsailのNginxの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A5%AF%A5%BB%A5%B9%A5%ED%A5%B0">アクセスログ</a>をCloudWatch Logsへ送るやり方を書いた。</p>

<p>オンプレミス(というか<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>以外の環境)で動いているOS等のログも同様のやり方で送ることができると思う。</p>

<h3>参考</h3>




<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/create-cloudwatch-agent-configuration-file.html">CloudWatch エージェント設定ファイルを作成する</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/installing-cloudwatch-agent-commandline.html">コマンドラインを使用して CloudWatch エージェントをインストールする</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://blog.asterism.xyz/posts/2019-07-25/">Pleromaを動かしているLightsailからCloudWatch Logsにログ転送 - blog.asterism.xyz</a></li>
</ul>

</body>
