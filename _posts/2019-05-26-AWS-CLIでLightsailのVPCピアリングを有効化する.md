---
layout: post
Categories:  ["tech"]
Description:  " LightsailはAWSアカウント上のVPCとは別のネットワーク環境で起動する。(Lightsail専用のVPC上で起動しているように見える)そのため、何もしてない状態でAWSアカウントの他のリソース(例えばRDS等)に接続しようとする"
Tags: ["AWS", "tech"]
date: "2019-05-26T18:46:00+09:00"
title: "AWS CLIでLightsailのVPCピアリングを有効化する"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p>Lightsailは<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>アカウント上の<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>とは別のネットワーク環境で起動する。(Lightsail専用の<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>上で起動しているように見える)
そのため、何もしてない状態で<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>アカウントの他のリソース(例えばRDS等)に接続しようとすると、インターネットを経由しなければならない。</p>

<p>現状ではLightsail側の<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>はデフォルト<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>に対してのみPeering接続がサポートされている。</p>
</body>

<!-- more -->

<body>
<h3>Lightsailの<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>ピアリング</h3>


<p>Lightsailのマネジメントコンソールから一発でできるが、今回は<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>を利用して接続してみる。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a> Peeringを有効にする場合のコマンドは下記。オプションは環境に合わせて使い分ける。</p>

<pre> $ aws lightsail peer-vpc --region &lt;リージョン名&gt; [--profile ]
 </pre>


<p>実行してみる。下記のような出力がなされれば成功しているはず。</p>

<pre> $ aws lightsail peer-vpc --region ap-northeast-1 --profile hogehoge
{
    "operation": {
        "status": "Succeeded",
        "resourceType": "PeeredVpc",
        "isTerminal": true,
        "operationDetails": "vpc-********",
        "statusChangedAt": **********.***,
        "location": {
            "availabilityZone": "all",
            "regionName": "ap-northeast-1"
        },
        "operationType": "PeeredVpc",
        "resourceName": "vpc-********",
        "id": "********-****-****-****-************",
        "createdAt": **********.***
    }
}
 </pre>


<p>確認コマンドは下記。オプションは環境に合わせて(ry</p>

<pre> $ aws lightsail peer-vpc --region &lt;リージョン名&gt; [--profile ]
 </pre>


<p>実行してみる。trueとなっているのでpeeringされている。</p>

<pre> $ aws lightsail is-vpc-peered --region ap-northeast-1 --profile hogehoge
{
    "isPeered": true
}
 </pre>


<p>これで完了。</p>

<p>解除するときは下記。オプションは(ry</p>

<pre> $ aws lightsail unpeer-vpc --region &lt;リージョン名&gt; [--profile ]
 </pre>


<h3>まとめ</h3>


<p>Lightsailの<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>と、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>アカウント上のデフォルト<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>とのPeeringするやり方について書いた。
少なくとも2019年5月時点ではデフォルト<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>としかつなげないのが少し残念だが、Lightsailを使って他の<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>リソースにアクセスさせたい場合は役に立ちそう。</p>

<h3>参考</h3>


<ul>
    <li><a target="_brank" rel="noopener noreferrer" herf="https://lightsail.aws.amazon.com/ls/docs/ja_jp/articles/lightsail-how-to-set-up-vpc-peering-with-aws-resources">Amazon Lightsail の外部の AWS リソースを使用するために Amazon VPC ピア接続をセットアップする | Lightsail ドキュメント</a></li>
    <li><a target="_brank" rel="noopener noreferrer" herf="https://aws.amazon.com/jp/lightsail/faq/">よくある質問 - Amazon Lightsail | AWS</a></li>
    <li><a target="_brank" rel="noopener noreferrer" herf="https://dev.classmethod.jp/cloud/aws/lightsail-vpc-peering-with-cli/">[Amazon Lightsail] VPCピアリングしたいリージョンがコンソールに出ない場合にはCLIを使おう ｜ DevelopersIO</a></li>
</ul>

</body>
