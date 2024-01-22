---
layout: post
Categories:  ["tech"]
Description:  " Yahoo Japan Tech Conferecne 2018 に行ってきた。       Yahoo! JAPAN Tech Conference 2018   当日の内容は以下にまとめられている。       Yahoo! JAPA"
Tags: ["other", "tech", "イベント"]
date: "2018-02-03T15:30:00+09:00"
title: "Yahoo Japan Tech Conferecne 2018 に行ってきた"
author: "dshimizu"
archive: ["2018"]
draft: "true"
---

<body>
<p>Yahoo Japan Tech Conferecne 2018 に行ってきた。</p>

<ul>
    <li><a href="https://techconference.yahoo.co.jp/2018/" target="_brank" rel="noopener noreferrer">Yahoo! JAPAN Tech Conference 2018</a></li>
</ul>


<p>当日の内容は以下にまとめられている。</p>

<ul>
    <li><a href="https://togetter.com/li/1194347" target="_brank" rel="noopener noreferrer">Yahoo! JAPAN Tech Conference 2018 - Togetter</a></li>
</ul>


<p>会場は<a class="keyword" href="http://d.hatena.ne.jp/keyword/Yahoo%21%20JAPAN">Yahoo! JAPAN</a>の本社がある<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C5%EC%B5%FE%A5%AC%A1%BC%A5%C7%A5%F3%A5%C6%A5%E9%A5%B9%B5%AA%C8%F8%B0%E6%C4%AE">東京ガーデンテラス紀尾井町</a>　<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B5%AA%C8%F8%B0%E6%A5%BF%A5%EF%A1%BC">紀尾井タワー</a>(東京ガーデンテラス 紀尾井カンファレンス)。
Yahoo Japanの技術的な取り組みに関して知ることが出来、とても豪華で楽しいイベントだった。
聴講セッションについてのメモとか所感などをまとめておく。</p>
</body>

<!-- more -->

<body>
<h3>聴講セッション</h3>


<h4>A-1 データセンターネットワークの取り組みと大規模サーバインフラの戦略 村越 健哉 様、藤見 和英 様</h4>


<p>Yahoo Japanのデータセンター内でのネットワークインフラのお話。一番聞きたかったセッションだった。</p>

<h5>データセンターネットワークの取り組み</h5>


<iframe src="//www.slideshare.net/slideshow/embed_code/key/DJjbUC81nX20F" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen=""> </iframe>


<div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/techblogyahoo/yjtc18-a1" title="YJTC18 A-1 データセンタネットワークの取り組み" target="_blank" rel="noopener noreferrer">YJTC18 A-1 データセンタネットワークの取り組み</a> </strong> from <strong><a href="https://www.slideshare.net/techblogyahoo" target="_blank" rel="noopener noreferrer">Yahoo! JAPAN デベロッパーネットワーク</a></strong>
</div>


<p><strong><u>ネットワーク概要</u></strong></p>

<p>まずはYahoo Jaoanのネットワーク概要について。
インフラ技術3部というところが35名弱のメンバーでプロダクションのネットワークを支えているとのこと。具体的には、<a class="keyword" href="http://d.hatena.ne.jp/keyword/CDN">CDN</a>、バックボーン、データセンター内のネットワークまで全て。管理しているネットワーク機器は7000台超え。データセンターは東日本・西日本それぞれで複数持っていて、東西でASを1つずつ保持しており、<a class="keyword" href="http://d.hatena.ne.jp/keyword/BCP">BCP</a>を取れるようにしているとのこと。米ワシントンにもデータセンターがありASを持って管理しているらしいがワシントンのデータセンターはActapio社が運用しているとのこと。</p>

<p>外部からの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%D5%A5%A3%A5%C3%A5%AF">トラフィック</a>を(Noth-South<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%D5%A5%A3%A5%C3%A5%AF">トラフィック</a>と呼んでいるそう)、これは増加傾向にあり、<a class="keyword" href="http://d.hatena.ne.jp/keyword/GYAO">GYAO</a>やニュースなどの動画<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%D5%A5%A3%A5%C3%A5%AF">トラフィック</a>がメインで500Gbps超え。外部<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%D5%A5%A3%A5%C3%A5%AF">トラフィック</a>の増加に対しては機器のインタフェースの増速やより高スペックな機器を使うなどのスケールアップにより対応してるとのこと。
また、データセンター内の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%D5%A5%A3%A5%C3%A5%AF">トラフィック</a>(East-West<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%D5%A5%A3%A5%C3%A5%AF">トラフィック</a>と呼んでいるそう)についてはNoth-Southの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%D5%A5%A3%A5%C3%A5%AF">トラフィック</a>よりさらに多く、全拠点合わせて10Tbps程もあるそう。これは分散処理(Haboop)やストレージとの通信が増加しているためだそうで、これを解消するためにはスケールアップでは限界に近づいているため、スケールアウトできるようCLOS Fabric Networkの導入を進めているとのこと。</p>

<p>規模の大きさに単純に圧倒された。それに対応するためのCLOSの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3">アーキテクチャ</a>はまだ詳しく理解できていないので以下の資料やブログなどもよく読み込んでみようと思う。
既知のTop of Rackな<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3">アーキテクチャ</a>(Traditional Network)のリプレースには歴史ある機器・構成であるため苦労しているそうですがこの辺りもやっぱりどこも変わらないのか...</p>

<ul>
    <li><a href="https://www.janog.gr.jp/meeting/janog38/download_file/clos.pdf" target="_brank" rel="noopener noreferrer">Janog38 ヤフーのIP CLOS ネットワーク</a></li>
</ul>


<p><strong><u>データセンターネットワークの取り組みについて</u></strong></p>

<p>上記のような状況において、Yahoo Jaoanのデータセンターネットワークの取り組みについてのお話が続いた。
まず、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%D5%A5%A3%A5%C3%A5%AF">トラフィック</a>については内部ネットワーク(East-West<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%D5%A5%A3%A5%C3%A5%AF">トラフィック</a>)の方が増加傾向にあるとのことだったが、これはYahoo Japanに限ったことではなく、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Facebook">Facebook</a>や<a class="keyword" href="http://d.hatena.ne.jp/keyword/Microsoft">Microsoft</a>もそういう傾向があるとのこと。</p>

<ul>
    <li><a href="https://code.facebook.com/posts/1782709872057497" target="_brank" rel="noopener noreferrer">Building Express Backbone: Facebook’s new long-haul network | Engineering Blog | Facebook Code</a></li>
</ul>


<p>こういう傾向に先んじて対応してきたのがOTT(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Over%20The%20Top">Over The Top</a>)であるが、Yahoo Japanとしてはこれらをベースにうまく活用していこうとしているとのこと。
具体的なネットワークに対する取り組みに関して<a class="keyword" href="http://d.hatena.ne.jp/keyword/Facebook">Facebook</a>との比較していた。<a class="keyword" href="http://d.hatena.ne.jp/keyword/Facebook">Facebook</a>と比較しているのは<a class="keyword" href="http://d.hatena.ne.jp/keyword/Facebook">Facebook</a>が情報をオープンにしているため。</p>

<p><u>Desigin</u></p>

<p>CLOS Fabric Networkを推進。ただし、CLOS Fabric NetworkはZone Securityのため、コアスイッチに<a class="keyword" href="http://d.hatena.ne.jp/keyword/ACL">ACL</a>を設定すると同じ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BB%A5%AD%A5%E5%A5%EA%A5%C6%A5%A3%A5%DD%A5%EA%A5%B7%A1%BC">セキュリティポリシー</a>でないと構築できない。例えば<a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a>のサーバと<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E4%A5%D5%A5%AA%A5%AF">ヤフオク</a>のサーバを同じCLOS Fabric Network上には設置できないという問題がある。
内部通信もセキュリティ担保のため各コアスイッチに<a class="keyword" href="http://d.hatena.ne.jp/keyword/ACL">ACL</a>を多用しており最大で11万行。ツールによる自動化で運用はできているが、大量に入っているためスケールアップが難しい。分散が必要。コアスイッチから<a class="keyword" href="http://d.hatena.ne.jp/keyword/iptables">iptables</a>ベースの<a class="keyword" href="http://d.hatena.ne.jp/keyword/ACL">ACL</a>システムを構築して開発環境でテスト中。
CROSS Fabirc NetworkをZone Securityではなくサーバのグループ単位で様々な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BB%A5%AD%A5%E5%A5%EA%A5%C6%A5%A3%A5%DD%A5%EA%A5%B7%A1%BC">セキュリティポリシー</a>が共存可能。これにより柔軟なアクセス制御を可能にする</p>

<p><u>Box/Chip</u></p>

<p>メーカースイッチではなくホワイトボックススイッチについてのお話。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Facebook">Facebook</a>は<a class="keyword" href="http://d.hatena.ne.jp/keyword/Wedge">Wedge</a>や<a class="keyword" href="http://d.hatena.ne.jp/keyword/Backpack">Backpack</a>の上にFBOSSを乗せて使っているそうだが、Yahooは<a class="keyword" href="http://d.hatena.ne.jp/keyword/Backpack">Backpack</a>やEdgeCoreを使い、OSの開発は難しいため Cumulus のOSを利用しているとのこと。</p>

<ul>
    <li><a href="https://code.facebook.com/posts/681382905244727/introducing-wedge-and-fboss-the-next-steps-toward-a-disaggregated-network/" target="_brank" rel="noopener noreferrer">Introducing “Wedge” and “FBOSS,” the next steps toward a disaggregated network | Engineering Blog</a></li>
</ul>


<p>かつては<a class="keyword" href="http://d.hatena.ne.jp/keyword/Cisco">Cisco</a>とJuniperでメーカー冗長を取っていたが、最近だとArista Networksと<a class="keyword" href="http://d.hatena.ne.jp/keyword/Cisco">Cisco</a>やWhiteboxは<a class="keyword" href="http://d.hatena.ne.jp/keyword/Broadcom">Broadcom</a>で同じチップなので、チップの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C0%A5%A4%A5%D0%A1%BC%A5%B7%A5%C6%A5%A3">ダイバーシティ</a>(多様性)のためASICにも注目されているとのこと。汎用ASIC、カスタムASICのそれぞれメリットがあるため利用を検討しているそうで、例えば汎用ASICは<a class="keyword" href="http://d.hatena.ne.jp/keyword/Broadcom">Broadcom</a>で安い、タ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%E0%A5%EA">イムリ</a>ーにその速度のインタフェースが利用可能、カスタムASICは大量の<a class="keyword" href="http://d.hatena.ne.jp/keyword/ACL">ACL</a>の投入やTelemetroy(?)が可能、などがあるらしい。</p>

<p>スイッチのシャーシは普通は1つのOSで構成されるが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Facebook">Facebook</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/Backpack">Backpack</a>ではFabricカードとLINEカードそれぞれにOSがあり、これにより1つのシャーシの内部で複数のOSにより論理的にCLOS Fabricを組まれており、1つのOSではないので冗長性が取れているのがメリットらしい...より詳細には以下のブログに書かれているとのことなのでこれも読んでおこうと思う。</p>

<ul>
    <li><a href="https://techblog.yahoo.co.jp/advent-calendar-2017/datacenternetwork_backpack/" target="_brank" rel="noopener noreferrer">データセンタネットワークの取り組みとFacebook Backpack - Yahoo! JAPAN Tech Blog</a></li>
</ul>


<p><u>Automation</u></p>

<p>自動化について。
Yahoo Japanではネットワークに関する自動化、可視化については自分たちでツールを作っており、既存のネットワークについては自動化と可視化ができているとのこと。</p>

<ul>
    <li><a href="https://www.janog.gr.jp/meeting/janog40/application/files/9015/0103/0898/janog40-soft-ando-00.pdf" target="_brank" rel="noopener noreferrer">ソフトウェアエンジニアがネットワーク部隊に来ていろいろやってみた</a></li>
</ul>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/IOS">IOS</a>、Arista EOS NX-OS といったOSごとの違いを抽象化して可視化。ただ、障害の予兆やCLOS Fabricのケーブルの配線ミスの検知は難しいとのこと。
これに関しては、Apstraの製品がCLOS Fabricに特化しているそうで、採用している。intentベースはやりたい設定をコンフィグとして生成する、自前でできないものはカバー。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Hadoop">Hadoop</a>ネットワークリプレースをやっているがApstra製品を使ってCLOS Fabricを作っているらしい。</p>

<p>ネットワークの話は以上だった。
他社のネットワーク構成の詳しい話を聞ける機会は少ないと思っていたので、大規模環境でのネットワークの取り組みの話を聞けたのはとても勉強になった。CROSの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3">アーキテクチャ</a>については資料や過去ブログ、関連情報をみて理解を深めておこうと思う。</p>

<h5>大規模サーバの戦略</h5>


<iframe src="//www.slideshare.net/slideshow/embed_code/key/IROPAjxna9Vy1Y" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen=""> </iframe>


<div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/techblogyahoo/yjtc18-a1-86835995" title="YJTC18 A-1 大規模サーバの戦略" target="_blank" rel="noopener noreferrer">YJTC18 A-1 大規模サーバの戦略</a> </strong> from <strong><a href="https://www.slideshare.net/techblogyahoo" target="_blank" rel="noopener noreferrer">Yahoo! JAPAN デベロッパーネットワーク</a></strong>
</div>


<p>続いてサーバ関連のお話。キーワードとして<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C1%B4%C2%CE%BA%C7%C5%AC">全体最適</a>化とOCPを挙げられており、それに関してのお話。
サイトオペレーション本部というところが商用インフラの担当部隊で、データセンター、ネットワーク、ストレージ、サーバ、OS、インフラのツールやOpenstackなどの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%A4%A5%D9%A1%BC%A5%C8%A5%AF%A5%E9%A5%A6%A5%C9">プライベートクラウド</a>の基盤といったアプリケーションレイヤまで幅広く担当しているとのこと。</p>

<p><strong><u><a class="keyword" href="http://d.hatena.ne.jp/keyword/%C1%B4%C2%CE%BA%C7%C5%AC">全体最適</a>化</u></strong></p>

<p>まずは<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C1%B4%C2%CE%BA%C7%C5%AC">全体最適</a>化について。
サーバは10万台。機種は80モデル以上、パーツ点数は20万点以上の環境で、担当メンバー10人1チームが2チームあり、それに加えて業務委託の方が数10名。構成の検討からデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>や筐体、機種の検証、比較、物理作業、保守・トラブル・故障対応、分析を行い、業務委託の方は配線や<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E9%A5%C3%A5%AD%A5%F3%A5%B0">ラッキング</a>などの物理作業を行うそう。</p>

<p><u>構成の検討</u>
サーバ台数が多いので、サーバ1台ごとに最適化するのではなく<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C1%B4%C2%CE%BA%C7%C5%AC">全体最適</a>化してトータルコスト削減を目指しているとのこと。例えばある時期に<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSD">SSD</a>を購入する場合、容量が80Gとか120Gとかあるが用途等によってバラバラに買うのではなく、その時期に買う型番を決めておき、パーツ管理や問題・バグを引く頻度のリスクを削減しているらしい。</p>

<p><u>検証〜保守</u>
内製の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%C8%A1%BC%A5%E9">インストーラ</a>などがうまく動くか、独自のOSがうまく動作するか、個々のデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>付属のユーティリティが動作するか使い勝手が落ちていないか、物理作業、配線のしやすさ、ボタンの押し間違えなどが起こらないかなどを検証し、それぞれ点数付けして、各モデル・各ベンダーの比較表を作って判断しているとのこと。</p>

<p><u>保守</u>
独自の保守フローがあるので各ベンダー、関係会社にできるか、提供していただけるか、ない場合は作っていただけるかなどを検討するとのこと。</p>

<p><u>分析</u>
大規模サーバの統計的な分析を行ない、将来のサーバやパーツ選定時に利用しているとのこと。
故障時のデータを管理したり、パーツの価格は適正か、市場ではどうかなどを確認できるようにしたり、<a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>やBMC、データを取得するツールなどが使いやすいかなどを数値比較。</p>

<p><strong><u>OCP</u></strong></p>

<p>続いてOCPサーバのお話。
現在、全体の80%程度は一般的な1U/2Uのサーバらしいが1000ノード程度OCPサーバを導入しているとのこと。
HyperScaleの思想で特定のサービス、特定の負荷、ワークロードに特化したスペック・構成にするのではなく、どこでも使いまわせるように構成でき(ex <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E4%A5%D5%A5%AA%A5%AF">ヤフオク</a>で使っていたものを広告用に転用する)、ユーザ手動でパーツ選定やHW設計ができ、ベンダーの影響を受けず、使いたいものを使いたい時に使えるのがメリット。また、物理運用面では<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E9%A5%C3%A5%AD%A5%F3%A5%B0">ラッキング</a>作業時に背面移動の必要なかったり、OCPに準拠しており独自仕様のない　IPMI も良いそう。</p>

<p>また、サーバベンダーやパーツ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B5%A5%D7%A5%E9%A5%A4%A5%E4%A1%BC">サプライヤー</a>、調達やデリバリーなどのサービスを提供している<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%B9%A5%C8%A5%EA%A5%D3%A5%E5%A1%BC%A5%BF%A1%BC">ディストリビューター</a>の各社計30社程と協業しており、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E9%A5%C3%A5%AD%A5%F3%A5%B0">ラッキング</a>業務のナレッジなどを提供したり、様々なパーツの故障率などに関する統計データを提供して、選定するパーツ等の話をしているそう。</p>

<p>将来的には「自立したインフラ」を目指し、外部依存を無くして、Yahooが主体的に管理できるようにしていくとのこと。</p>

<p>大規模インフラ環境におけるサーバの構築から運用までのノウハウが聞けた。
機器選定における検証や分析などかなり細かくしっかりやられている印象でさすがだと思った。検証時の点数付けのポイントや分析データ管理などをどうやっているのか具体的なところが知りたかった。
実際に導入されているOCPサーバが会場に展示されて、見ると小さくて扱いやそうな印象だった。
また、Yahoo Japanほどの規模とそこにいらっしゃるエンジニアだとOCPサーバのメリットも大きい気はするが、一般的には保守の面がかなりネックになりそう。全20名+αの人数で保守周りもどうやっているのか気になった。</p>

<h4>A-3 <a class="keyword" href="http://d.hatena.ne.jp/keyword/Yahoo%21%20JAPAN">Yahoo! JAPAN</a>を支える開発基盤 PaaS 甲斐 遼馬 様/Joshua McKenty 様(Foundry Pivotal Software, Inc.)</h4>


<p>2つ目のセッションは<a class="keyword" href="http://d.hatena.ne.jp/keyword/Yahoo%21%20JAPAN">Yahoo! JAPAN</a>を支える開発基盤のお話。</p>

<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Yahoo%21%20JAPAN">Yahoo! JAPAN</a>を支える開発基盤</h5>


<iframe src="//www.slideshare.net/slideshow/embed_code/key/iBFJmGeuK7H3st" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen=""> </iframe>


<div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/techblogyahoo/yjtc18-a3-yahoo-japan" title="YJTC18 A-3 Yahoo! JAPANを支える開発基盤" target="_blank" rel="noopener noreferrer">YJTC18 A-3 Yahoo! JAPANを支える開発基盤</a> </strong> from <strong><a href="https://www.slideshare.net/techblogyahoo" target="_blank" rel="noopener noreferrer">Yahoo! JAPAN デベロッパーネットワーク</a></strong>
</div>


<p>以下のリンク先にある内容と類似のお話。</p>

<ul>
    <li><a href="https://codezine.jp/article/detail/10059?p=2" target="_brank" rel="noopener noreferrer">Yahoo! JAPANの現場で活躍する社内PaaS、サービス開発を10倍早くするための活用術とは【デブサミ2017】 (2/2)：CodeZine（コードジン）</a></li>
    <li><a href="http://techtarget.itmedia.co.jp/tt/news/1712/14/news01.html" target="_brank" rel="noopener noreferrer">ヤフーの「Cloud Foundry」徹底活用術、使って分かったPaaSの魅力と難しさ － TechTargetジャパン クラウド</a></li>
</ul>


<p>全社共通でCloud Foundryを利用しているとのこと。多くのベンダーで採用されており、他のCloud Foundryのプロダクト、例えば<a class="keyword" href="http://d.hatena.ne.jp/keyword/IBM">IBM</a>のBluemixや<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C9%D9%BB%CE%C4%CC">富士通</a>のCF(?)などのノウハウを活用できるというメリットがあることと、動作環境を選ばず、OpenStack や vSphereの複数のIaaS基盤と組み合わせたり、DBや開発言語を利用できるのがメリットある。</p>

<ul>
    <li><a href="http://jp.fujitsu.com/solutions/cloud/k5/function/paas/cf/" target="_brank" rel="noopener noreferrer">CF | FUJITSU Cloud Service K5 : 富士通</a></li>
    <li><a href="https://www.ibm.com/cloud-computing/bluemix/ja/runtimes" target="_brank" rel="noopener noreferrer">Cloud Foundryアプリ・ランタイム | IBM Cloud</a></li>
</ul>


<p>また、PaaSで動くCI/CDとして以下のツールを利用しているそう。
concourseはWebUIでタスクの繋がりを見ることができ、<a class="keyword" href="http://d.hatena.ne.jp/keyword/YAML">YAML</a>+Dockerで容易に環境構築可能なこととやCloud Foundryにもすでに機能化されているらしい。</p>

<ul>
    <li><a href="https://yahooeng.tumblr.com/post/155765242061/open-sourcing-screwdriver-yahoos-continuous" target="_brank" rel="noopener noreferrer">Open Sourcing Screwdriver, Yahoo’s Continuous... | Yahoo Engineering</a></li>
    <li><a href="https://github.com/concourse/concourse" target="_brank" rel="noopener noreferrer">concourse/concourse · GitHub</a></li>
</ul>


<p>導入した目的としては、これまでの開発手法だと開発後の運用保守に手間がかかっていたため、PaaS上で効率よく開発できるようにし、より付加価値の高いサービスを開発できるようにするとのこと。共<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C4%CC%B2%BD">通化</a>したい部分をCloud FoundryのBuildpackという機能で共<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C4%CC%B2%BD">通化</a>できるそうで、これらによって開発者の負担を減らしていくそう。</p>

<p><strong><u>PaaSの今後</u></strong></p>

<p>2016年に一部導入、その経験を元に本格的に2017年に本格導入、2017年度末は<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>数を5-&gt;10(dev+<a class="keyword" href="http://d.hatena.ne.jp/keyword/stg">stg</a>+prodがサービス開発エンジニア向けに解放されている環境)　30,000リク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トをさばけるような規模の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>を構築していくとのこと。
(sandbox環境はプラットフォーム開発本部のテスト環境)
Cloud Foundryと外部プラットフォームの接続にはCloud FoundryのCredentialsという値を発行してプラットフォーム側に格納して有効化する必要があったが、これを<a class="keyword" href="http://d.hatena.ne.jp/keyword/cli">cli</a>ツールで自動でプロビジョニングをできるようにし、開発者の負担を減らしていくことを目指しているとのこと。</p>

<p>色々できるというのはわかったけど、Cloud FoundryもOpenStackも使ったことがないのでいまいちピンとこなかった...</p>

<ul>
    <li><a href="https://docs.cloudfoundry.org/services/binding-credentials.html" target="_brank" rel="noopener noreferrer">Binding Credentials | Cloud Foundry Docs</a></li>
</ul>


<p>Cloud Foundryは全然触ったこともないので話を聞いただけではどんな良さがあるのかわからなかったけどOpenStack や vSphereのほか、Bluemixなどの商用プロダクトとも連携できるというのは初めて知った。</p>

<h5>Complexity and Autonomy(複雑さと自律性)</h5>


<script async="" class="speakerdeck-embed" data-id="ad463071d0ee49faa368d7c91928fcd7" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>


<p>Pivotal Cloud Foundry Field CTO のJoshua McKenty氏から複雑さと自律性についてのお話。
英語セッションで同時通訳のレシーバーがあったもののなかなか追うのが難しかった。</p>

<ul>
    <li><a href="http://agilemanifesto.org/iso/ja/manifesto.html" target="_brank" rel="noopener noreferrer">アジャイルソフトウェア開発宣言</a></li>
</ul>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Agile">Agile</a> Manifesto にある通り、最も重要なのは価値あるソフトウェアを早くかつ継続的に提供して顧客を満足させること。このためには自動化が不可欠。
しかし実現は難しい。これは人を相手にしているからとのこと。</p>

<p>「<a class="keyword" href="http://d.hatena.ne.jp/keyword/No%20plan">No plan</a> survives contact with the enemy. ( Correlli Barnett(Helmuth von Moltke the Elder))」...いかなる戦術も眼前の敵には無力。</p>

<p><strong><u>複雑さについて</u></strong></p>

<p>簡単な問題とはケーキを作るようなもの。レシピさえあればできる。入り組んだ問題とはロケットを作るようなもの。複数のレシピが必要で、組み合わせる必要がある。複雑な問題とは子育てのようなもの。レシピはなく、突発的な問題が起こる。</p>

<p><strong><u>半自律型のチーム</u></strong></p>

<p>半自律型のチームとは。チームがいろんなことを試している。間違っていても良いという感覚が重要。なぜそれが必要かを全員が分かっており、素早い意思決定ができる。
組織は完璧を求めるべきではない。「半」自律型であることが重要。
また、開発者だけで組織されたチームは効果を発揮しない。</p>

<p><strong><u>実験するマインド</u></strong></p>

<p>継続的に何かをするためには実験を行い続ける必要がある。どんな計画であっても敵の前では無駄。思い通りにはいかない。
人は思い通りには動かない。人と貴方たちは常に対立している。完璧な計画なんてない。</p>

<p><strong><u>必要なスキルの変化</u></strong></p>

<p>コードを書いたり読んだりだけでなく、人の話を聞く、コミュニケーションをとる、振り返る、反省することも必須。
ただツールを使うのではなく、仕事の仕方を常に学び続ける。
盆栽や俳句では、観察者と観察者の距離を短くしようと、不要なものを捨てて調整する。</p>

<p>まとめとしては以上。
ちょっと色々聞き取れなかった部分もあって見返すとよくわからないけど、ITエンジニアが人を相手にしたり複雑な問題に取り組むにはコードの読み書き以外にも多様なスキルが必要ということなのかと思った。そうだとするとその意見には賛成で、Team <a class="keyword" href="http://d.hatena.ne.jp/keyword/Geek">Geek</a>にも似たようなことが書かれていたと思うけど、物事をよりよく進めていくためには一緒にやる相手のやっていることを理解して耳を傾けて歩み寄ることが重要なんだと思っている。</p>

<h4>C-5 アプリケーションの高速デプロイを可能にする技術 - <a class="keyword" href="http://d.hatena.ne.jp/keyword/Yahoo%21%20JAPAN">Yahoo! JAPAN</a> の <a class="keyword" href="http://d.hatena.ne.jp/keyword/Kubernetes">Kubernetes</a> as a Service <a class="keyword" href="http://d.hatena.ne.jp/keyword/%C6%A3%B9%BE">藤江</a> 貴司 様、河 宜成 様(ゼットラボ株式会社)</h4>


<iframe src="//www.slideshare.net/slideshow/embed_code/key/hZoxDiuMU7GHH" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen=""> </iframe>


<div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/techblogyahoo/yjtc18-c5-yahoo-japan-kubernetes-as-a-service" title="YJTC18 C-5 アプリケーションの高速デプロイを可能にする技術 - Yahoo! JAPAN の Kubernetes as a Service" target="_blank" rel="noopener noreferrer">YJTC18 C-5 アプリケーションの高速デプロイを可能にする技術 - Yahoo! JAPAN の Kubernetes as a Service</a> </strong> from <strong><a href="https://www.slideshare.net/techblogyahoo" target="_blank" rel="noopener noreferrer">Yahoo! JAPAN デベロッパーネットワーク</a></strong>
</div>


<p>以下のSplunkのセッションと迷いつつ <a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a> のお話を聴講。</p>

<ul>
    <li><a href="https://www.slideshare.net/techblogyahoo/yjtc18-d5-yahoo-japan-splunk" target="_brank" rel="noopener noreferrer">D-5 日本のインターネットを守る！Yahoo! JAPANの不正利用対策 - Splunkによる不正ログイン検知</a></li>
</ul>


<p>Yahoo Japanでの<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>の利用事例。
100以上のサービスを全てコンテナ化するためのコンテナ実行環境として<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>を検討。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> TrendsでもOpenStackを抜き、DIAMOND&amp;PLATINUMスポンサーにも世界トップクラスのIT企業が名を連ねていることから各社<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>に力を入れていることがわかる。そんな背景から2017年にYahoo Japanも<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>の本格導入を開始したそう。</p>

<ul>
    <li><a href="https://schd.ws/hosted_files/kccncna17/e7/Keynote%20-%20Dan%20Kohn%20-%20KCCNC%20NA%202017%20FINAL.pdf" target="_brank" rel="noopener noreferrer">Keynote: A Community of Builders: CloudNativeCon Opening Keynote - Dan Kohn, Executive Director, Cloud Native Computing Foundation</a></li>
</ul>


<p>導入においてはゼットラボ社が開発した<a class="keyword" href="http://d.hatena.ne.jp/keyword/Kubernetes">Kubernetes</a> as a Serviceを利用してすでにズバトクのサービスでプロダクション環境で稼働しているとのこと。</p>

<ul>
    <li><a href="https://toku.yahoo.co.jp/" target="_brank" rel="noopener noreferrer">Yahoo!ズバトク</a></li>
</ul>


<p>なお、ゼットラボ社はYahoo Japanの完全子会社。ちなみにZコーポレーションとは別会社とのこと。</p>

<ul>
    <li><a href="https://zlab.co.jp/" target="_brank" rel="noopener noreferrer">Z Lab - 技術で新しい世界へシフトする。</a></li>
    <li><a href="https://www.businessinsider.jp/post-160834" target="_brank" rel="noopener noreferrer">ヤフーが若手社長就任でデータ会社として再出発 ── 宮坂現社長は謎のZコーポレーションを牽引 | BUSINESS INSIDER JAPANM</a></li>
</ul>


<p><strong><u>コンテナ化以前のCI/CD</u></strong></p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>へのpushは自動化できていたがデプロイは手動だったとのこと。機能追加やパッケージが増えたりデプロイにかかる時間が増大、さらにアクセス増による<a class="keyword" href="http://d.hatena.ne.jp/keyword/VM">VM</a>の増加でその運用管理コスト増大・故障時の対応増大...などなど、環境維持コストが高まってきた。</p>

<p><strong><u>コンテナ化後(<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>導入後)のCI/CD</u></strong></p>

<p>開発環境を<a class="keyword" href="http://d.hatena.ne.jp/keyword/VM">VM</a>からローカル化に移行、<a class="keyword" href="http://d.hatena.ne.jp/keyword/kubernetes">kubernetes</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>を構築、CI/CDのパイプラインを組みやすいツールに変更して開発からデプロイまでをシームレスに実行可能にし、容易なスケールアウト、高速デプロイを達成。結果、本質的な開発に割くリソース確保が可能になったとのこと。</p>

<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Kubernetes">Kubernetes</a> as a Service の紹介</h5>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Kubernetes">Kubernetes</a> as a Serviceの仕組みや機能の紹介。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/kubernetes">kubernetes</a>導入で解決しないこと、起こる弊害。ノード(<a class="keyword" href="http://d.hatena.ne.jp/keyword/VM">VM</a>)の追加。<a class="keyword" href="http://d.hatena.ne.jp/keyword/VM">VM</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C0%C8%BC%E5%C0%AD">脆弱性</a>、障害。約3ヶ月ごとにリリースされる<a class="keyword" href="http://d.hatena.ne.jp/keyword/kubernetes">kubernetes</a>のバージョンアップ。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%DD%A1%BC%A5%CD%A5%F3%A5%C8">コンポーネント</a>の管理。
特にYahoo Japanのような数万台規模の環境で<a class="keyword" href="http://d.hatena.ne.jp/keyword/Kubernetes">Kubernetes</a>を手動で管理するのは現実的ではない。
これら<a class="keyword" href="http://d.hatena.ne.jp/keyword/Kubernetes">Kubernetes</a>の問題を解決しないことを解決するためのKaaS。<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>の管理コスト削減したり<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーを払い出すフルマネージドのツールとのこと。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>の追加は、開発者がKaaSに対して<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>を追加するというリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを送ることで、YahooのIaaS(OpenStack)から必要な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%DD%A1%BC%A5%CD%A5%F3%A5%C8">コンポーネント</a>を作成され、社内の<a class="keyword" href="http://d.hatena.ne.jp/keyword/DNS">DNS</a>に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>を登録まで行われる。他、構成を変えたり複数の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーを作ったりもできるOpenStackのフレーバーを変えることもできるとのこと。
オートヒーリングや水平スケールなども運用<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B9%A9%BF%F4">工数</a>削減のための機能がたくさんあるらしい。</p>

<p>とにかく運用<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B9%A9%BF%F4">工数</a>削減のニュアンスの言葉がよく出ていた気がする。<a class="keyword" href="http://d.hatena.ne.jp/keyword/Kubernetes">Kubernetes</a>は使ったことなく、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Kubernetes">Kubernetes</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>の運用の大変さを味わったことはないけど、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>というだけでやばそうな気がする。Podなどの単語もその場ではわからなかった。というか今も詳しくわかってない。そんな程度の知識しかないのでまずは<a class="keyword" href="http://d.hatena.ne.jp/keyword/Kubernetes">Kubernetes</a>を触ってみるところから始める必要がありそう...
KaaSは最初から導入するのではなく、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Kubernetes">Kubernetes</a>の運用が手に余るような状態になってきたところで使うイメージなんだろうか...
また、会場の中でも<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>を使っている方はほとんどいないかった。</p>

<h4>B-6 テク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%CE%A5%ED">ノロ</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B8%A1%BC">ジー</a>と<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D6%A5%E9%A5%F3%A5%C7%A5%A3%A5%F3%A5%B0">ブランディング</a> ～人を惹きつける技術～ 内田 伸哉 様</h4>


<iframe src="//www.slideshare.net/slideshow/embed_code/key/HPEv4PI1vcKH6o" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen=""> </iframe>


<div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/techblogyahoo/yjtc-b6" title="YJTC B-6 テクノロジーとブランディング ～人を惹きつける技術～" target="_blank" rel="noopener noreferrer">YJTC B-6 テクノロジーとブランディング ～人を惹きつける技術～</a> </strong> from <strong><a href="https://www.slideshare.net/techblogyahoo" target="_blank" rel="noopener noreferrer">Yahoo! JAPAN デベロッパーネットワーク</a></strong>
</div>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D6%A5%E9%A5%F3%A5%C7%A5%A3%A5%F3%A5%B0">ブランディング</a>の技術についてのお話。</p>

<p>まずは過去のYahoo　Japanのプロモーションの紹介。</p>

<p>2013年9月に行われた「さわれる検索」というプロモーション。
インターネットで送れるものは視覚と聴覚。<a class="keyword" href="http://d.hatena.ne.jp/keyword/3D%A5%D7%A5%EA%A5%F3%A5%BF">3Dプリンタ</a>の登場により触覚も送れるのでは？という発想の元、さわれる検索というものを開発。3Dデータベースを検索してそれがアウトプットされるというサービス。コードが<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>で公開されていてると言ってたような気がして、確かに過去のプレスリリースでもそんなことが書かれていたけど探したけど見つからなかった。</p>

<ul>
    <li><a href="http://www.youtube.com/watch?v=SPbBdqqcsFY" target="_brank" rel="noopener noreferrer">さわれる検索　Yahoo! JAPAN</a></li>
</ul>


<p>2017/3月、3.11から6年、東京の人々が3.11を思い出すための広告。</p>

<ul>
    <li><a href="https://togetter.com/li/1090664" target="_brank" rel="noopener noreferrer">「もしも、東日本大震災の津波が銀座に来たら」の広告の反響（設置から撤去まで） - Togetterまとめ</a></li>
    <li><a href="http://mainichi.jp/articles/20170307/k00/00e/040/223000c" target="_brank" rel="noopener noreferrer">大震災６年：津波の高さ感じて　ヤフーが銀座に巨大広告 - 毎日新聞</a></li>
</ul>


<p>2016/4/1 インターネット20年の歴史の絵巻物。</p>

<ul>
    <li><a href="https://about.yahoo.co.jp/20years/" target="_brank" rel="noopener noreferrer">インターネットの歴史 History of the Internet - Yahoo! JAPAN</a></li>
</ul>


<p>公式キャ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E9%A5%AF">ラク</a>ターの宣伝動画。
若年層に弱いので親しみやすいキャ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E9%A5%AF">ラク</a>ターを作成。</p>

<ul>
    <li><a href="https://kensakutoenjin.yahoo.co.jp/" target="_brank" rel="noopener noreferrer">けんさくとえんじん - Yahoo! JAPAN</a></li>
</ul>


<p>これらの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D6%A5%E9%A5%F3%A5%C7%A5%A3%A5%F3%A5%B0">ブランディング</a>の目的は Yahoo をどう好きになってもらうか。どう選んでもらえるかの心理制御が大事。</p>

<p>続いて以下。</p>

<ul>
    <li><a href="http://nlab.itmedia.co.jp/nl/articles/1707/26/news039.html" target="_brank" rel="noopener noreferrer">「君の名は。」Blu-ray＆DVD発売記念ペア動画がリア充仕様でぼっちには辛い - ねとらぼ</a></li>
</ul>


<p>自分は見ていないが完成動画を見るには2つの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%DE%A5%DB">スマホ</a>が必要らしい。<a class="keyword" href="http://d.hatena.ne.jp/keyword/Twitter">Twitter</a>でも話題に。
作ったきっかけは、"「<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B7%AF%A4%CE%CC%BE%A4%CF%A1%A3">君の名は。</a>」はDVD発売に伴って話題を作りたい。"、"Yahooはユーザに驚く体験を届けたい。"と言う双方の思いを組み合わせて何かできるのではないかと思ったから。</p>

<p>ペア動画でのユーザ間の画像をどうSYNCさせるかと言う課題の技術的な側面。</p>

<ol>
    <li>mp4でテストすると動画プレイヤーが立ち上がり、プレイヤー依存ですぐに再生されたりされなかったり古い端末の方もいたり。連続的にGIFを送る形に変更。</li>
    <li>同期を重要点のみに変更。より多くの利用者のため。動画再生には<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%DE%A5%DB">スマホ</a>のスペックによって色々問題がある。</li>
    <li>リク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト負荷分散。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DF%A5%E5%A1%BC%A5%B8%A5%C3%A5%AF%A5%B9%A5%C6%A1%BC%A5%B7%A5%E7%A5%F3">ミュージックステーション</a>で放映することになり負荷増。対応するための<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3">アーキテクチャ</a>を設計。</li>
</ol>


<p>色々な技術を駆使してペア動画で盛り上がれる世の中を作った。
Yahooが面白いことを提供すれば結果的にYahooを好きになってもらえるという形。
どう世の中を盛り上げるかどう世の中を盛り上げていくかというところを考えてこう言ったものを提供している。</p>

<p><strong><u>ブランドの技術</u></strong></p>

<p><u>1. 広い技術</u></p>

<p>狭い技術とは、ハードウェア、プログラミング、<a class="keyword" href="http://d.hatena.ne.jp/keyword/VR">VR</a>やAR…など。
広い技術とは、デザイン、世の中の心を掴む、炎上させたりバズらせる、モテる、などのファジィなもの。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/PS%20VR">PS VR</a>とうんこドリル、これを同率で考えられるか？最先端の技術かどうかだけではなく、小学生の心をうまくつかむ技術。記事のタイトル等で世の中にウケるかどうかまで見れる技術、設計。互いに尊重するのが大事。自分がどこの技術を持っているのか。
テク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%CE%A5%ED">ノロ</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B8%A1%BC">ジー</a>の技術者、プロモーションの技術者が集まって設計をすることが重要。</p>

<p><u>2. 良さ</u>
日本のマーケットは先端、最新を意識しすぎて定性的な良さが希薄になっている。
「最先端でも面白くなければだめ、根本的な良さを求める」
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%BF%CD%B9%A9%C3%CE%C7%BD">人工知能</a>、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D3%A5%C3%A5%B0%A5%C7%A1%BC%A5%BF">ビッグデータ</a>、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D6%A5%ED%A5%C3%A5%AF%A5%C1%A5%A7%A1%BC%A5%F3">ブロックチェーン</a>の言葉が入っている企画書は面白いか？</p>

<p>言葉だけ踊るのではなく、何が良いのかを具体的に考える。見極める。
良さがある上でテク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%CE%A5%ED">ノロ</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B8%A1%BC">ジー</a>を使うか使わないかを判断している。</p>

<p><u>3. メッセージ</u></p>

<p>一番大事。いつ何を言うか。良さを明確に表せるか。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D6%A5%E9%A5%F3%A5%C7%A5%A3%A5%F3%A5%B0">ブランディング</a>とは人の心を動かす技術。</p>

<h5>D-7 <a class="keyword" href="http://d.hatena.ne.jp/keyword/Yahoo%21%A5%B7%A5%E7%A5%C3%A5%D4%A5%F3%A5%B0">Yahoo!ショッピング</a>のサービスデータ活用事例 藤木 貴之 様</h5>


<iframe src="//www.slideshare.net/slideshow/embed_code/key/INAn3Nqi7Li1uc" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen=""> </iframe>


<div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/techblogyahoo/yjtc18-d7-yahoo" title="YJTC18 D-7 Yahoo!ショッピングのサービスデータ活用事例" target="_blank" rel="noopener noreferrer">YJTC18 D-7 Yahoo!ショッピングのサービスデータ活用事例</a> </strong> from <strong><a href="https://www.slideshare.net/techblogyahoo" target="_blank" rel="noopener noreferrer">Yahoo! JAPAN デベロッパーネットワーク</a></strong>
</div>


<p>(あとで書く...)</p>

<h3>まとめ</h3>


<p>Yahoo Japan Tech Conference 2018 に参加してきたので内容のメモと感想を書いた。
Yahoo Japanの技術的な取り組みに関して知ることが出来た。また、セッション以外にも多数のブース、会場のスタッフの方々のお気遣い、ランチセッション、様々な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%CE%A5%D9%A5%EB%A5%C6%A5%A3">ノベルティ</a>などなど、無償なのにとても豪華で素晴らしい1日を過ごせたイベントだった。ありがとうございました。</p>

<h4>参考</h4>


<ul>
    <li><a href="https://techblog.yahoo.co.jp/event/yjtc18_slide/" target="_brank" rel="noopener noreferrer">Yahoo! JAPAN Tech Conference 2018 のスライドを公開します - Yahoo! JAPAN Tech Blog</a></li>
    <li><a href="http://blog.kushii.net/archives/2072354.html" target="_brank" rel="noopener noreferrer">Yahoo! JAPAN Tech Conference 2018 にさらりと参加してきた #yjtc - 941::blog</a></li>
    <li><a href="https://techblog.yahoo.co.jp/advent-calendar-2017/datacenternetwork_backpack/" target="_brank" rel="noopener noreferrer">データセンタネットワークの取り組みとFacebook Backpack - Yahoo! JAPAN Tech Blog</a></li>
    <li><a href="https://www.janog.gr.jp/meeting/janog38/download_file/clos.pdf" target="_brank" rel="noopener noreferrer">ヤフーのIP CLOSネットワークの取り組み(PDF資料)</a></li>
    <li><a href="http://www.publickey1.jp/blog/15/post_255.html" target="_brank" rel="noopener noreferrer">ホワイトボックススイッチとは何か？ オープン化がすすむネットワーク機器のハードとソフトの動向（前編）。ホワイトボックススイッチユーザ会　第一回勉強会 － Publickey</a></li>
    <li>
<a href="http://www.publickey1.jp/blog/15/post_255.html" target="_brank" rel="noopener noreferrer"></a><a href="http://opencomputejapan.org/wp-content/uploads/2017/10/10_CloudComputingDay2017_02.pdf" target="_brank" rel="noopener noreferrer">OCP ネットワークスイッチ</a>
</li>
    <li><a href="https://www.slideshare.net/npsg/telemetry-80639219" target="_brank" rel="noopener noreferrer">Telemetry事始め</a></li>
    <li><a href="http://www.publickey1.jp/blog/14/facebookwedgelinux_osfboss.html" target="_brank" rel="noopener noreferrer">Facebook、オープンハードなスイッチ「Wedge」と、対応Linux OS「FBOSS」を発表 － Publickey</a></li>
</ul>

</body>
