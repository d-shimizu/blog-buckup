---
layout: post
Categories:  ["tech"]
Description:  " AWSのVPSサービスであるAmazon Lightsailの新プラン/料金改定(値下げ)が発表された。       Amazon Lightsail Update – More Instance Sizes and Price Redu"
Tags: ["AWS", "Lightsail", "tech"]
date: "2018-09-01T21:03:00+09:00"
title: "Amazon Lightsailの新プランで料金改定(値下げ)が発表されたので既存インスタンスを移行した"
author: "dshimizu"
archive: ["2018"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPS">VPS</a>サービスである<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Lightsailの新プラン/料金改定(値下げ)が発表された。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://aws.amazon.com/jp/blogs/aws/amazon-lightsail-update-more-instance-sizes-and-price-reductions/">Amazon Lightsail Update – More Instance Sizes and Price Reductions | AWS News Blog </a></li>
</ul>


<p>今はLightsailの1Gメモリプランのものを使っていたが、それも$10-&gt;$5へ値下げされたにも関わらず<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSD">SSD</a>のサイズが30G-&gt;40Gへと上がっていた。
これは移行したほうが良いのではないかと思い、1Gプラン→新1Gプランへと移行してみた。</p>
</body>

<!-- more -->

<body>
<h3 id="移行手順">移行手順</h3>


<p>LightsailはEC2のように簡単にスペック変更できず、スナップショットから新しい<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を作成する必要があるとのこと。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://aws.amazon.com/jp/lightsail/faq/">よくある質問</a></li>
</ul>
<pre class="doc"> プランをアップグレードできますか?
はい。インスタンスのスナップショットを作成し、API を使用して新しい大きなサイズのインスタンスを作成します。Lightsail コンソールまたは CLI を使用して、スナップショットから新しいインスタンスを作成できます。CLI の使用方法については、こちらを参照してください。
 </pre>



<p>ということで、以下の手順にて新<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>への移行を行う。</p>

<ol>
    <li>今の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>のスナップショット取得</li>
    <li>取得したスナップショットから新<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を作成</li>
</ol>


<h4 id="事前">事前</h4>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>を利用できるようにしておく。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/Linux">Linux</a> AMI ならデフォルトで利用可能かと思われる。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" herf="https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/awscli-install-linux-al2017.html">Amazon Linux 2017 で AWS Command Line Interface をインストールする - AWS Command Line Interface</a></li>
</ul>


<p>それ以外のOSの場合はドキュメントを参照されたし。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" herf="https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/awscli-install-linux.html">Linux に AWS Command Line Interface をインストールします - AWS Command Line Interface</a></li>
</ul>


<h4 id="今のインスタンスのスナップショット取得">今の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>のスナップショット取得</h4>


<p>今のLightsailの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>情報を取得する。
以下のような結果が得られる。</p>

<pre class="terminal"> $ aws lightsail get-instances
{
    "instances": [
        {
            "username": "ec2-user",
            "isStaticIp": true,
            "networking": {
                "monthlyTransfer": {
                    "gbPerMonthAllocated": 2048
                },
                "ports": [
                    {
                        "protocol": "tcp",
                        "accessType": "public",
                        "commonName": "",
                        "accessFrom": "Anywhere (0.0.0.0/0)",
                        "fromPort": 80,
                        "accessDirection": "inbound",
                        "toPort": 80
                    },
                     :
                     :
                    }
                ]
            },
            "name": "Amazon_Linux-1GB-Tokyo-1",
            "resourceType": "Instance",
            "supportCode": "************/i-*****************",
            "blueprintName": "Amazon Linux",
            "hardware": {
                "cpuCount": 1,
                "disks": [
                    {
                        "attachmentState": "attached",
                        "attachedTo": "Amazon_Linux-1GB-Tokyo-1",
                        "iops": 100,
                        "sizeInGb": 30,
                        "path": "/dev/xvda",
                        "isSystemDisk": true,
                        "createdAt": **********.***
                    }
                ],
                "ramSizeInGb": 1.0
            },
            "privateIpAddress": "***.***.***.***",
            "state": {
                "code": 16,
                "name": "running"
            },
            "arn": "arn:aws:lightsail:ap-northeast-1:************:Instance/********-****-****-****-************",
            "publicIpAddress": "***.***.***.***",
            "blueprintId": "amazon_linux_2017_03_0",
            "bundleId": "micro_1_0",
            "sshKeyName": "keypair01",
            "createdAt": **********.***,
            "location": {
                "availabilityZone": "ap-northeast-1a",
                "regionName": "ap-northeast-1"
            }
        }
    ]
}
 </pre>


<p>この情報をもとにスナップショットを取得する。</p>

<pre class="terminal"> $ aws lightsail create-instance-snapshot \
  --region ap-northeast-1 \
  --instance-snapshot-name "Amazon_Linux-1GB-Tokyo-1-snapshot-2018090101" \
  --instance-name "Amazon_Linux-1GB-Tokyo-1"
 </pre>


<p>スナップショットの結果を確認する。</p>

<pre class="terminal"> $ aws lightsail get-instance-snapshots
{
    "instanceSnapshots": [
        {
            "fromInstanceName": "Amazon_Linux-1GB-Tokyo-1",
            "fromAttachedDisks": [],
            "name": "Amazon_Linux-1GB-Tokyo-1-snapshot-2018090101",
            "sizeInGb": 30,
            "resourceType": "InstanceSnapshot",
            "supportCode": "************/ami-*****************",
            "fromInstanceArn": "arn:aws:lightsail:ap-northeast-1:************:Instance/********-****-****-****-************",
            "state": "pending",
            "arn": "arn:aws:lightsail:ap-northeast-1:************:InstanceSnapshot/********-****-****-****-************",
            "fromBundleId": "micro_1_0",
            "fromBlueprintId": "amazon_linux_2017_03_0",
            "createdAt": **********.***,
            "location": {
                "availabilityZone": "all",
                "regionName": "ap-northeast-1"
            }
        }
    ]
}
 </pre>


<p><code>"state": "pending"</code> となっているところを <code>"state": "available"</code> となるまで待つ。</p>

<pre class="terminal"> $ aws lightsail get-instance-snapshots
{
    "instanceSnapshots": [
        {
            "fromInstanceName": "Amazon_Linux-1GB-Tokyo-1",
            "fromAttachedDisks": [],
            "name": "Amazon_Linux-1GB-Tokyo-1-snapshot-2018090101",
            "sizeInGb": 30,
            "resourceType": "InstanceSnapshot",
            "supportCode": "************/ami-*****************",
            "fromInstanceArn": "arn:aws:lightsail:ap-northeast-1:************:Instance/********-****-****-****-************",
            "state": "available",
            "arn": "arn:aws:lightsail:ap-northeast-1:************:InstanceSnapshot/********-****-****-****-************",
            "fromBundleId": "micro_1_0",
            "fromBlueprintId": "amazon_linux_2017_03_0",
            "createdAt": **********.***,
            "location": {
                "availabilityZone": "all",
                "regionName": "ap-northeast-1"
            }
        }
    ]
}
 </pre>


<p>これでスナップショットの取得は完了。</p>

<h4 id="取得したスナップショットから新インスタンスを作成">取得したスナップショットから新<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を作成</h4>


<p>スケールアップさせたいスペックを確認するため、以下を実行する。
利用できる<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>の情報が出力されるので、利用したいスペックの<code>bundleId</code>をメモしておく。
ここでは現行と同様(ただし <code>"diskSizeInGb": 40,</code> となっている)メモリ1Gのものを使いたいので<code>"bundleId": "micro_2_0"</code>となる。</p>

<pre class="terminal"> $ aws lightsail get-bundles
{
    "bundles": [
        {
            "name": "Nano",
            "power": 300,
            "price": 3.5,
            "ramSizeInGb": 0.5,
            "diskSizeInGb": 20,
            "transferPerMonthInGb": 1024,
            "cpuCount": 1,
            "instanceType": "nano",
            "isActive": true,
            "bundleId": "nano_2_0"
        },
        {
            "name": "Micro",
            "power": 500,
            "price": 5.0,
            "ramSizeInGb": 1.0,
            "diskSizeInGb": 40,
            "transferPerMonthInGb": 2048,
            "cpuCount": 1,
            "instanceType": "micro",
            "isActive": true,
            "bundleId": "micro_2_0"
        },
         :
         :
         :
        {
            "name": "Xlarge",
            "power": 4000,
            "price": 120.0,
            "ramSizeInGb": 16.0,
            "diskSizeInGb": 320,
            "transferPerMonthInGb": 6144,
            "cpuCount": 4,
            "instanceType": "xlarge",
            "isActive": true,
            "bundleId": "xlarge_win_2_0"
        },
        {
            "name": "2Xlarge",
            "power": 5000,
            "price": 240.0,
            "ramSizeInGb": 32.0,
            "diskSizeInGb": 640,
            "transferPerMonthInGb": 7168,
            "cpuCount": 8,
            "instanceType": "2xlarge",
            "isActive": true,
            "bundleId": "2xlarge_win_2_0"
        }
    ]
}
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/Cli">Cli</a>ツールで<code>aws lightsail create-instances-from-snapshot</code>を実行する。
必要な引数は環境に合わせて調整する。</p>

<pre class="terminal"> $ aws lightsail create-instances-from-snapshot \
  --instance-names "Amazon_Linux-1GB-Tokyo-1-2018090101" \
  --availability-zone "ap-northeast-1a" \
  --instance-snapshot-name "Amazon_Linux-1GB-Tokyo-1-snapshot-2018090101" \
  --key-pair-name "lightsail-key-pair" \
  --bundle-id "micro_2_0"
{
    "operations": [
        {
            "status": "Started",
            "resourceType": "Instance",
            "isTerminal": false,
            "statusChangedAt": **********.***,
            "location": {
                "availabilityZone": "ap-northeast-1a",
                "regionName": "ap-northeast-1"
            },
            "operationType": "CreateInstancesFromSnapshot",
            "resourceName": "Amazon_Linux-1GB-Tokyo-1-2018090101",
            "id": "********-****-****-****-************",
            "createdAt": **********.***
        }
    ]
}
 </pre>


<p>これでしばらく待てば、新しい<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>が起動して利用可能になる。
ネットワーク回りの設定は引き継がれないので、Lightsailの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%A1%A5%A4%A5%A2%A5%A6%A5%A9%A1%BC%A5%EB">ファイアウォール</a>とか静的IPの設定は別途行う必要がある。</p>

<h3 id="まとめ">まとめ</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Lightsailのプラン変更(スペックアップ)試した。
現時点では、現行<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>のスナップショットを取得して、それを用いて新しい<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を作成するしかない。</p>

<h3 id="参考">参考</h3>


<ul>
    <li><a target="_brank" rel="noopener noreferrer" herf="https://lightsail.aws.amazon.com/ls/docs/en/articles/lightsail-how-to-create-larger-instance-from-snapshot-using-aws-cli">Create a larger Lightsail instance from a snapshot using the AWS CLI | Lightsail Documentation</a></li>
    <li><a target="_brank" rel="noopener noreferrer" herf="https://dev.classmethod.jp/cloud/aws/amazon-lightsail-scaleup/">Amazon Lightsail インスタンスのスケールアップ手順を試してみた ｜ DevelopersIO</a></li>
    <li><a target="_brank" rel="noopener noreferrer" herf="https://www.slideshare.net/AmazonWebServicesJapan/aws-black-belt-online-seminar-amazon-lightsail">AWS Black Belt Online Seminar - Amazon Lightsail</a></li>
</ul>

</body>
