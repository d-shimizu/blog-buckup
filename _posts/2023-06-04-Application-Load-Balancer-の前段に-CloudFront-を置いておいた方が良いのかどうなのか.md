---
title: "Application Load Balancer の前段に CloudFront を置いておいた方が良いのかどうなのか"
date: 2023-06-04
slug: "Application Load Balancer の前段に CloudFront を置いておいた方が良いのかどうなのか"
category: blog
tags: [AWS,Amazon CloudFront]
---
<h2 id="はじめに">はじめに</h2>

<p>Application Load Balancer の前段に CloudFront があると何が良いのかと稀によく聞かれるので、簡単に整理してみました。</p>

<p>と思っていたら以下のようなツイートを見つけました。</p>

<p><blockquote data-conversation="none" class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">「完全に個人の意見」ですが、特にこだわりがなければ、<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> 上で動かすPublic にアクセス可能なWEBアプリの前段にはCloudFront を置くことをワイはお勧めしますやで！<br><br>理由は以下の３つやで。<br><br>1. ネットワーク転送量コストの観点<br>2. スパイク時の負荷分散<br>3. ネットワーク経路の最適化… <a href="https://t.co/ssRYMwIUN9">pic.twitter.com/ssRYMwIUN9</a></p>&mdash; はまーん / Shinichi <a class="keyword" href="https://d.hatena.ne.jp/keyword/Hama">Hama</a> (@track3jyo) <a href="https://twitter.com/track3jyo/status/1652226055070162944?ref_src=twsrc%5Etfw">2023年4月29日</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  </p>

<p>なのでわざわざ整理する必要もなさそうでしたが、一応自分用メモとして記載しておきます。</p>

<h2 id="目次">目次</h2>

<ul class="table-of-contents">
    <li><a href="#はじめに">はじめに</a></li>
    <li><a href="#目次">目次</a></li>
    <li><a href="#セキュリティの側面">セキュリティの側面</a><ul>
            <li><a href="#AWS-Shield-Standard-で保護される">AWS Shield Standard で保護される</a></li>
            <li><a href="#ネットワーク構成面">ネットワーク構成面</a></li>
            <li><a href="#IP-制限">IP 制限</a></li>
        </ul>
    </li>
    <li><a href="#通信料">通信料</a></li>
    <li><a href="#リクエスト数">リクエスト数</a></li>
    <li><a href="#機能等">機能等</a></li>
    <li><a href="#障害">障害</a></li>
    <li><a href="#まとめ">まとめ</a></li>
    <li><a href="#参考">参考</a></li>
</ul>

<h2 id="セキュリティの側面">セキュリティの側面</h2>

<h3 id="AWS-Shield-Standard-で保護される"><a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Shield Standard で保護される</h3>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> には <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Shield というマネージド型の DDoS 保護サービスがあります。</p>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Shield には Standard と Advance がありますが、Standardの方はデフォルトで有効になっており、CloudFront が前段にいると一定の保護を受けることができます。</p>

<ul>
<li><a href="https://aws.amazon.com/jp/shield/features/#AWS_Shield_Standard">&#x7279;&#x5FB4; - AWS Shield | AWS</a></li>
</ul>


<blockquote><p>すべての <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> のお客様は、追加料金なしで <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Shield Standard の保護の適用を自動的に受けることができます。<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Shield Standard は、ウェブサイトやアプリケーションを標的にした、最も一般的で頻繁に発生するネットワークおよびトランスポートレイヤーの DDoS 攻撃を防御します。<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Shield Standard を <a class="keyword" href="https://d.hatena.ne.jp/keyword/Amazon%20CloudFront">Amazon CloudFront</a> や <a class="keyword" href="https://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Route 53 とともに使用すると、インフラスト<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%E9%A5%AF">ラク</a>チャ (レイヤー 3 および 4) を標的とした既知の攻撃すべてから包括的に保護されます</p></blockquote>

<p>ただ、これは上記にある通りL3-L4に対するものなので、HTTPのようなL7に対しては別途対策を講じる必要があります。</p>

<ul>
<li><a href="https://repost.aws/ja/knowledge-center/shield-standard-ddos-attack">Shield Standard &#x3092;&#x4F7F;&#x7528;&#x3057;&#x3066; DDoS &#x653B;&#x6483;&#x3092;&#x9632;&#x3050; | AWS re:Post</a></li>
</ul>


<p>アクセス元が固定で、限定したIPからのみアクセス制限を行うことができるようなものだと、ALB 直でセキュリティグループやネットワーク<a class="keyword" href="https://d.hatena.ne.jp/keyword/ACL">ACL</a>で頑張るというのでも良いのかもしれません。</p>

<h3 id="ネットワーク構成面">ネットワーク構成面</h3>

<p>論理的には、ALB は <a class="keyword" href="https://d.hatena.ne.jp/keyword/VPC">VPC</a> の内部にいます。そのため、インターネットから直接 ALB へ通信を受ける際には、ALBがいる <a class="keyword" href="https://d.hatena.ne.jp/keyword/VPC">VPC</a> のパブリックサブネット内まで通信が来ていることになると思います。
一方で、CloudFrontは<a class="keyword" href="https://d.hatena.ne.jp/keyword/VPC">VPC</a>外にいます。なので、CloudFrontがいると、論理的には、<a class="keyword" href="https://d.hatena.ne.jp/keyword/VPC">VPC</a>の外でまずは通信を受けてくれることになります。</p>

<p>これも限定したIPからのみアクセス制限を行うことができるようなものだと、あまり気にせず、ALB 直というのでも良いのかもしれません。
ただ、CloudFrontが<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a>バックボーンネットワークにいて、そこで通信を受け付けてくれるという一定の安心感はある気がします。</p>

<p>日本向けサービスで東京リージョンで稼働しているサービスで、コンテンツ等はなくシンプルなRESTベースの<a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a>等である場合等に対して、ACloudFront+<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a>バックボーンネットワークを介するケースでどのくらい<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EC%A5%A4%A5%C6%A5%F3%A5%B7%A1%BC">レイテンシー</a>に差があって恩恵を受けられるのかは検証できてないので分かっていない。</p>

<h3 id="IP-制限">IP 制限</h3>

<p>ALBだとセキュリティグループが使えるので、IP制限は結構楽にやれるかと思います。
CloudFrontだと、セキュリティグループは使えないので、<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> WAFを使う形になります。また、この場合の<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> WAFは<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%D0%A1%BC%A5%B8%A5%CB%A5%A2">バージニア</a>リージョン(us-east-1)に作成する必要があります。若干の手間はあります。</p>

<h2 id="通信料">通信料</h2>

<p>ALB からインターネットへのアウトバウンド通信は、通信量に応じて料金がかかります。通信料はEC2の料金が適用されるようです。</p>

<ul>
<li><a href="https://aws.amazon.com/jp/elasticloadbalancing/pricing/">&#x6599;&#x91D1; - Elastic Load Balancing | AWS</a></li>
</ul>


<blockquote><p>データ転送料金</p>

<p> ELB 料金に加えて、標準の <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> データ転送料金が発生します。詳細については、<a class="keyword" href="https://d.hatena.ne.jp/keyword/Amazon%20EC2">Amazon EC2</a> 料金ページのデータ転送セクションをご覧ください。</p></blockquote>

<p>以下の ALB の推奨セキュリティグループ設定から、ALB単体で外部に通信することはないと思われるので、ターゲットグループに登録されているターゲットに来た通信の戻り通信にこの料金がかかると思います。</p>

<ul>
<li><a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-update-security-groups.html#security-group-recommended-rules">Security groups for your Application Load Balancer - Elastic Load Balancing</a></li>
</ul>


<p>続いて、EC2 の料金を見てみます。</p>

<ul>
<li><a href="https://aws.amazon.com/jp/ec2/pricing/on-demand/#Data_Transfer">&#x30AA;&#x30F3;&#x30C7;&#x30DE;&#x30F3;&#x30C9;&#x30A4;&#x30F3;&#x30B9;&#x30BF;&#x30F3;&#x30B9;&#x306E;&#x6599;&#x91D1; - Amazon EC2 (&#x4EEE;&#x60F3;&#x30B5;&#x30FC;&#x30D0;&#x30FC;) | AWS</a></li>
</ul>


<p>2023/6 時点では、東京リージョンでは下記のようでした。</p>

<ul>
<li><a class="keyword" href="https://d.hatena.ne.jp/keyword/Amazon%20EC2">Amazon EC2</a> からインターネットへのデータ転送 (アウト)</li>
</ul>


<table>
<thead>
<tr>
<th> </th>
<th> </th>
</tr>
</thead>
<tbody>
<tr>
<td> 最初の 10 TB/月 </td>
<td>  0.114USD/GB </td>
</tr>
<tr>
<td> 次の 40 TB/月 </td>
<td> 0.089USD/GB </td>
</tr>
<tr>
<td> 次の 100 TB/月 </td>
<td> 0.086USD/GB </td>
</tr>
<tr>
<td> 150 TB/月以上 </td>
<td> 0.084USD/GB </td>
</tr>
</tbody>
</table>


<p>一方で、CloudFront の通信料ですが、これもEC2と同等のようでした。</p>

<ul>
<li><a href="https://aws.amazon.com/jp/cloudfront/pricing/">&#x6599;&#x91D1; - Amazon CloudFront | AWS</a></li>
</ul>


<p>ただ、以下の通り毎月の無料枠があり、これに収まる場合は無料で利用できます。</p>

<blockquote><p>常時無料利用枠に含まれます</p>

<p>1 か月あたり 1 TB のデータ転送 (OUT)</p>

<p>1 か月あたり 10,000,000 件の HTTP または <a class="keyword" href="https://d.hatena.ne.jp/keyword/HTTPS">HTTPS</a> リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト</p>

<p>1 か月あたり 200 万件の CloudFront 関数呼び出し</p>

<p>無料の <a class="keyword" href="https://d.hatena.ne.jp/keyword/SSL">SSL</a> 証明書</p>

<p>制限なし、すべての機能が利用可能</p></blockquote>

<p>また、<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> のサービスから CloudFront へのデータ転送 (アウト) の料金は 0 USD/GB です。</p>

<p>そのため、ALB 直の場合だとデータ転送 (アウト) の料金がそのまま適用されますが、CloudFrontを挟むと、ALB -> CloudFront 間のデータ転送 (アウト) の料金は無料で、CloudFrontのデータ転送 (アウト) の料金は最初の 1 TB のデータ転送 (OUT)は無料なので、通信料の点ではメリットがありそうです。</p>

<h2 id="リクエスト数">リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト数</h2>

<p>CloudFront は HTTP リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト数に応じて料金がかかります。</p>

<ul>
<li><a href="https://aws.amazon.com/jp/cloudfront/pricing/">&#x6599;&#x91D1; - Amazon CloudFront | AWS</a></li>
</ul>


<p>上述の通り、「1 か月あたり 10,000,000 件の HTTP または <a class="keyword" href="https://d.hatena.ne.jp/keyword/HTTPS">HTTPS</a> リク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト」は無料ですが、それを上回るリク<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トが発生すると料金がかかります。この場合は、ALB の前段にCloudFrontを置いた場合には追加の費用がかかることになりそうです。</p>

<h2 id="機能等">機能等</h2>

<p>CloudFront でキャッシュやオリジンルーティングなど色々やれて機能は充実していると思います。
一方でキャッシュの設定や管理などは割と慎重にやらないとインシデントになりそうなので、やや運用コストは上がるかもしれません。</p>

<h2 id="障害">障害</h2>

<p>CloudFront の障害対策となると、待機用に別<a class="keyword" href="https://d.hatena.ne.jp/keyword/CDN">CDN</a>を使うとか、<a class="keyword" href="https://d.hatena.ne.jp/keyword/DNS">DNS</a>をALBに割り当ててALB直で疎通できるようにする、といったことは技術的には可能そうですが、なかなか準備するのは大変そうです。ALB の方でも障害となるケースもあり得るので、この点は何をどこまでケアするか、という感じになるように思います。</p>

<h2 id="まとめ">まとめ</h2>

<p>他にも色々な観点がありそうですが、個人的には、CloudFrontはとりあえずALBの前段に置いておいた方が良さそうには思います。
一方で設定や検証などはやはり慎重に行う必要がありそうです。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://aws.amazon.com/jp/ec2/pricing/on-demand/#Data_Transfer">&#x30AA;&#x30F3;&#x30C7;&#x30DE;&#x30F3;&#x30C9;&#x30A4;&#x30F3;&#x30B9;&#x30BF;&#x30F3;&#x30B9;&#x306E;&#x6599;&#x91D1; - Amazon EC2 (&#x4EEE;&#x60F3;&#x30B5;&#x30FC;&#x30D0;&#x30FC;) | AWS</a></li>
<li><a href="https://aws.amazon.com/jp/cloudfront/pricing/">&#x6599;&#x91D1; - Amazon CloudFront | AWS</a></li>
<li><a href="https://aws.amazon.com/jp/elasticloadbalancing/pricing/">&#x6599;&#x91D1; - Elastic Load Balancing | AWS</a></li>
<li><a href="https://twitter.com/track3jyo/status/1652226055070162944">https://twitter.com/track3jyo/status/1652226055070162944</a></li>
<li><a href="https://nulab.com/ja/blog/nulab/how-to-overcome-cloudfront-failure/">&#x5B9F;&#x8DF5;&#xFF01;&#x30CC;&#x30FC;&#x30E9;&#x30DC;&#x30B5;&#x30FC;&#x30D3;&#x30B9;&#x3067;&#x306E; CloudFront &#x306E;&#x969C;&#x5BB3;&#x5BFE;&#x7B56; | &#x682A;&#x5F0F;&#x4F1A;&#x793E;&#x30CC;&#x30FC;&#x30E9;&#x30DC;(Nulab inc.)</a></li>
<li><a href="https://noname.work/957.html">[AWS]&#x30AD;&#x30E3;&#x30C3;&#x30B7;&#x30E5;&#x3092;&#x6301;&#x305F;&#x306A;&#x3044;CloudFront&#x306E;&#x5229;&#x70B9;&#x3068;&#x306F;&#xFF1F; | &#x500B;&#x4EBA;&#x5229;&#x7528;&#x3067;&#x59CB;&#x3081;&#x308B;AWS&#x5B66;&#x7FD2;&#x8A18;</a></li>
<li><a href="https://repost.aws/ja/knowledge-center/shield-standard-ddos-attack">Shield Standard &#x3092;&#x4F7F;&#x7528;&#x3057;&#x3066; DDoS &#x653B;&#x6483;&#x3092;&#x9632;&#x3050; | AWS re:Post</a></li>
<li><a href="https://d1.awsstatic.com/whitepapers/compliance/JP_Whitepapers/DDoS_White_Paper_JP.pdf">https://d1.awsstatic.com/whitepapers/compliance/JP_Whitepapers/DDoS_White_Paper_JP.pdf</a></li>
<li><a href="https://www.a10networks.co.jp/news/blog/azurel3l7_ddos.html">Azure&#x30C6;&#x30CA;&#x30F3;&#x30C8;&#x306B;&#x5BFE;&#x3059;&#x308B;L3&#xFF5E;L7 DDoS&#x9632;&#x5FA1;&#x306E;&#x63D0;&#x4F9B; | &#x30D6;&#x30ED;&#x30B0; | &#x6700;&#x65B0;&#x60C5;&#x5831; | A10&#x30CD;&#x30C3;&#x30C8;&#x30EF;&#x30FC;&#x30AF;&#x30B9; &#x30A2;&#x30D7;&#x30EA;&#x30B1;&#x30FC;&#x30B7;&#x30E7;&#x30F3;&#x914D;&#x4FE1;&#xFF5C;&#x30D7;&#x30ED;&#x30AD;&#x30B7;&#xFF5C;SSL&#x53EF;&#x8996;&#x5316;&#xFF5C;DDoS&#x5BFE;&#x7B56;</a></li>
<li><a href="https://www.cloudflare.com/ja-jp/learning/ddos/layer-3-ddos-attacks/">https://www.cloudflare.com/ja-jp/learning/ddos/layer-3-ddos-attacks/</a></li>
</ul>


