---
layout: post
Categories:  ["tech"]
Description:  " 2017/5/31 AWS Summit Tokyo 2017で Amazon Lightsail が東京リージョンで利用可能になったと発表がありました。   Amazon Lightsail is now available in As"
Tags: ["AWS", "Lightsail", "tech"]
date: "2017-06-04T23:28:00+09:00"
title: "Amazon Lightsail が東京リージョンで利用可能になったので起動してみた"
author: "dshimizu"
archive: ["2017"]
draft: "true"
---

<body>
<p>2017/5/31 <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Summit Tokyo 2017で <a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Lightsail が東京リージョンで利用可能になったと発表がありました。</p>

<blockquote class="twitter-tweet tw-align-center" data-conversation="none" data-lang="ja">
<p lang="ja" dir="ltr"><a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Lightsail is now available in Asia Pacific <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Regions - Tokyo, Mumbai, Singapore, &amp; Sydney! <a href="https://t.co/4tZUDKPoh4" target="_blank" rel="noopener noreferrer">https://t.co/4tZUDKPoh4</a> <a href="https://t.co/jMKGzp1QcM" target="_blank" rel="noopener noreferrer">pic.twitter.com/jMKGzp1QcM</a></p>
— <a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon%20Web%20Services">Amazon Web Services</a> (@awscloud) <a href="https://twitter.com/awscloud/status/869988008879050752" target="_blank" rel="noopener noreferrer">2017年5月31日</a>
</blockquote>


<script async="" src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

</body>

<!-- more -->

<body>
<p>公式ブログにも記載があります。</p>

<ul>
    <li><a href="https://aws.amazon.com/jp/blogs/news/amazon-lightsail-tokyo-region-launch/" target="_blank" rel="noopener noreferrer">Amazon Lightsail、東京リージョンにて提供開始 | Amazon Web Services ブログ</a></li>
</ul>


<p>どんなものかサクッと起動してみました。</p>

<h3>試しに起動してみた</h3>


<p>Lightsailの管理画面から"Create instance"ボタンをクリックした後の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>作成画面で、"Tokyo"が選択可能になっていました。
※現時点(2017/6/3)では画面は日本語にはなりません。</p>

<p><img src="http://blog.dshimizu.jp/wp-content/uploads/2017/07/lightsail01.png" alt="" width="2878" height="1504" class="aligncenter size-full wp-image-542"></p>

<p>OSで<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/Linux">Linux</a>を選択して<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を作成してみました。</p>

<p>作成手順は下記の公式手順もありますが、とりあえずはボタンをポチポチすれば良いだけなので難しいところはないと思います。
作成途中で、既存のEC2と同じような感じでログイン用の<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSH">SSH</a>鍵の作成も行うことになります。鍵の名称の変更はできないようでした。</p>

<ul>
    <li><a href="https://aws.amazon.com/jp/blogs/news/amazon-lightsail-the-power-of-aws-the-simplicity-of-a-vps/" target="_blank" rel="noopener noreferrer">Amazon Lightsail – AWSの力、VPSの簡単さ | Amazon Web Services ブログ</a></li>
</ul>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>作成完了画面。EC2でのElastic IPに相当する「静的 IP」も割り当て可能のようです。</p>

<p><img src="http://blog.dshimizu.jp/wp-content/uploads/2017/07/lightsail02-1.png" alt="" width="2878" height="1504" class="aligncenter size-full wp-image-546"></p>

<p>起動後の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>にログインしてみました。
パッと見普通のEC2<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>と変わらないように見えます。</p>

<pre class="terminal"> [ec2-user@ip-***-***-***-*** work]$ uname -a
Linux ip-***-***-***-*** 4.9.27-14.31.amzn1.x86_64 #1 SMP Wed May 10 01:58:40 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
 </pre>


<h3>まとめ</h3>


<p>Lightsailの東京リージョンの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を起動してみました。
画面が違うだけでほとんどEC2と同じような感じと思います。使ってないですが<a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a>もあるようですし、画面からポチポチする必要もないかもしれません。
他の<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のサービスと連携するにはひと手間かかるものもありますが、個人でEC2<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を使うには現状の最低スペック(t2.micro)でも1か月起動しっぱなしだと地味にお財布に厳しいものがありましたので、<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPS">VPS</a>だとお値段面ではお手頃で助かります。</p>

<ul>
    <li><a href="https://docs.aws.amazon.com/lightsail/2016-11-28/api-reference/amazon-lightsail-api-reference.pdf" target="_brank" rel="noopener noreferrer">amazon-lightsail-api-reference.pdf</a></li>
</ul>

</body>
