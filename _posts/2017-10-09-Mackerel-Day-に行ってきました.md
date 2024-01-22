---
layout: post
Categories:  ["tech"]
Description:  " Mackerel Day　に行ってきたのでそのメモ書きです。       Mackerel Day #mackerelio - connpass   Mackerel Day は株式会社はてな様が開発しているSaaS型の監視サービスである"
Tags: ["Mackerel", "tech", "イベント"]
date: "2017-10-09T15:47:00+09:00"
title: "Mackerel Day に行ってきました"
author: "dshimizu"
archive: ["2017"]
draft: "true"
---

<body>
<p>Mackerel Day　に行ってきたのでそのメモ書きです。</p>

<ul>
    <li><a href="https://mackerelio.connpass.com/event/67292/" target="_blank" rel="noopener noreferrer">Mackerel Day #mackerelio - connpass</a></li>
</ul>


<p>Mackerel Day は<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B3%F4%BC%B0%B2%F1%BC%D2%A4%CF%A4%C6%A4%CA">株式会社はてな</a>様が開発している<a class="keyword" href="http://d.hatena.ne.jp/keyword/SaaS">SaaS</a>型の監視サービスである <a href="https://mackerel.io/" target="_brank" rel="noopener noreferrer">Mackerel</a> の正式リリースから3周年を記念したイベントでした。
セッションの内容としては、まず<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%CF%A4%C6%A4%CA">はてな</a>のMackerelプロダクトマネージャー杉山様からMackerelの製品コンセプト、主な機能、これまでとこれからといった話から始まり、各ユーザ様(<a class="keyword" href="http://d.hatena.ne.jp/keyword/DMM.com">DMM.com</a>ラボ,Freee,<a class="keyword" href="http://d.hatena.ne.jp/keyword/GMO">GMO</a> ペパボ,メルカリ)の導入/利用事例や<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%CF%A4%C6%A4%CA">はてな</a>のMackerel開発チームの方々のMackerel関連のプロジェクトなどの話と続き、最後に、Mackerelが「<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> DevOps Competency」というDevOps領域でのパートナー資格を日本初で取得したことから、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Web Service Japan酒徳様より<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のエコシステム/DevOps関連サービスに関するお話で締めくくられました。</p>

<p>当日のツイートは以下にまとめられています。</p>

<ul>
    <li><a href="https://togetter.com/li/1158402" target="_blank" rel="noopener noreferrer">生誕三周年！Mackerel Day まとめ - Togetterまとめ</a></li>
</ul>


<p>Mackerelは個人の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>上のOSリソース情報を取得しているだけでほとんど初心者に近い状態ですが、お誘いを受けたので参加してみました。</p>
</body>

<!-- more -->

<body>
<h3>聴講セッション</h3>


<h4>Mackerelリリース3周年の振り返りとこれからの成長について <a class="keyword" href="http://d.hatena.ne.jp/keyword/%B3%F4%BC%B0%B2%F1%BC%D2%A4%CF%A4%C6%A4%CA">株式会社はてな</a> 杉山様</h4>


<p>Mackerelについては、元は<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%CF%A4%C6%A4%CA">はてな</a>の中で2007年頃から10年間作り続けられてきたもので、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%CF%A4%C6%A4%CA">はてな</a>の動的なインフラをいかに効率よく管理するかと言う観点で社内で磨いてきた仕組みを、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%EB%A5%B9%A5%AF%A5%E9%A5%C3%A5%C1">フルスクラッチ</a>で新たに製品化したものであるとのことでした。選ばれる理由として、UIのわかりやすさや、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D1%A5%D6%A5%EA%A5%C3%A5%AF%A5%AF%A5%E9%A5%A6%A5%C9">パブリッククラウド</a>との親和性の高さを挙げられてましたが、それらに加えて、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%CF%A4%C6%A4%CA">はてな</a>の方々のシステム運用経験をもとに作られてきたことがプロダクトの完成度を高め、同じような悩みを抱えるエンジニアの共感を得て多くのユーザの導入に繋がっているのかと思いました。</p>

<p>オススメ機能5選はアラート通知、URL外形監視、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Integration、オーガニゼーションを用いた柔軟な権限管理、グラフ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A5%CE%A5%C6%A1%BC%A5%B7%A5%E7%A5%F3">アノテーション</a>。
「アラート通知」　はチャットツールへのアラート通知・グラフ投稿、fubotなどと組み合わせによる運用/開発間の連携のサポート(既存仕組みの効率化・ディスカッションへの発展等)のほか、Twilioなどの外部サービス連携があるようです。
「グラフ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A5%CE%A5%C6%A1%BC%A5%B7%A5%E7%A5%F3">アノテーション</a>(注釈付与)機能」と言うものがあり、これはグラフの特定の箇所に注釈をつけることが可能で、これは例えばこの時間帯で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%ED%A1%BC%A5%C9%A5%A2%A5%D9%A5%EC%A1%BC%A5%B8">ロードアベレージ</a>のグラフが跳ねているのは「リリース直後で負荷が高騰したため」等のコメントを付けておき、後から見返したときに何があったかわかりやすくできるとのことで、他の監視ツールにはない機能で確かに使えると後々の振り返りの時などに便利そうでした。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>関連の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>が充実しており<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>との親和性が高く思えますが、Azureサポートも拡張しているとのことです。<a class="keyword" href="http://d.hatena.ne.jp/keyword/GCP">GCP</a>については言及されていませんでしたが、Mackerelエージェントを入れればプラットフォームに関わらず基本的なことはできそうです。
直近リリース予定の新機能としては、現在丸られた形で400日保存可能なデータを、丸られることなく400日保存可能にする、と言うものがあるそうです。どんだけのデータ量になりまたそれをどう管理されていくのか・・・。
また今後の展望として<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B5%A1%B3%A3%B3%D8%BD%AC">機械学習</a>を利用した異常検知やLamdbaやコンテナツール/サービスのサポート拡張、構成を管理する<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%B8%A5%B9%A5%C8%A5%EA">レジストリ</a>としての仕組みの充実等があるようです。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> DevOps CompetencyというDevOps領域でのパートナー資格を日本で初めて取得したとのことで、今後世界で勝負していくサービスになっていくような印象を受けました。</p>

<ul>
    <li><a href="http://hatenacorp.jp/press/release/entry/2017/09/19/153000" target="_blank" rel="noopener noreferrer">はてなのサーバー監視サービス「Mackerel」が国内初のAWS DevOpsコンピテンシー認定を取得 - プレスリリース - 株式会社はてな</a></li>
</ul>


<script async="" class="speakerdeck-embed" data-id="ce95b219a55844d99018c6f34f257c98" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>


<h4>アプリケーションエンジニアがMackerelで楽しく監視構成している事例 株式会社<a class="keyword" href="http://d.hatena.ne.jp/keyword/DMM.com">DMM.com</a>ラボ 太田様 西岡様</h4>


<p>DMM.make のサービスを、アプリケーションエンジニアが二人でオンプレから<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>(<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>)に移行した中で、監視システムもZabbixからMackerelに変えた時の導入〜現在の利用状況のお話でした。</p>

<p>最初に会場にいる参加者はアプリケーションエンジニアかインフラエンジニアかを聞かれて挙手する場面がありましたが、だいたいアプリエンジニア:インフラエンジニアが4:6くらいのように見えました(違ったらすみません)。開発者にも運用者にも注目されているサービスであることが感じ取れました。</p>

<p>DMM全体としてはシステムをオンプレで統合管理されているようですが、今回のサービスを<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>移行はアプリケーションエンジニアの方二人のみで半年でやることになり、責任分解点も変わって<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の運用管理も含めて全て面倒見ることになったとのことです。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>時代のインフラエンジニアの役割について危機感を抱いています。
今まではZabbixで監視していたそうですが、トライアル利用で2時間程度でやりたいと思っていたリソース監視とログ監視を設定出来たとのことで、アプリケーションエンジニアであっても簡単に導入/設定できるMackerelの良さが伝わりました。</p>

<p>導入に当たっての社内稟議の話もあり、比較検討し設定の容易さ、ドキュメントの充実度といった点から構築、運用管理時の人的コストが大幅に削減される点をアピールしたそうです。事務手続き周りは手間でスルーされがちですが、こういった話も聞けたのはよかったです。</p>

<p>まだ色々試行中らしいですが、充実したドキュメントや多数の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>でつまずくことなく楽に監視設定できること、モニタリングにより改善サイクルを回しやすくなり楽しい運用ができること、グラフから読み取れる異常など当たり前のことに気づけたとのことで、とにかく扱いが容易であることを強調されている印象でした。
また、Mackerelチームにフィードバックを送ったことがあるそうで、その時のフィードバックの速さ、要望した機能リリースのご連絡など、CREのきめ細かい対応の素晴らしさを感じました。</p>

<iframe src="//www.slideshare.net/slideshow/embed_code/key/mI4XF3453U2Q78" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen=""> </iframe>


<div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/KeikoNishioka/mackerel-80523541" title="アプリケーションエンジニアがMackerelで楽しく監視構成している事例" target="_blank" rel="noopener noreferrer">アプリケーションエンジニアがMackerelで楽しく監視構成している事例</a> </strong> from <strong><a href="https://www.slideshare.net/KeikoNishioka" target="_blank" rel="noopener noreferrer">Keiko Nishioka</a></strong>
</div>


<h4>Mackerel インフラ基盤 <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> 移行の舞台裏 <a class="keyword" href="http://d.hatena.ne.jp/keyword/%B3%F4%BC%B0%B2%F1%BC%D2%A4%CF%A4%C6%A4%CA">株式会社はてな</a> 大野様</h4>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%CF%A4%C6%A4%CA">はてな</a>のWebオペレーションエンジニアの大野様より、Mackerelの基盤を<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>への移行、それに関する技術的な側面のお話でした。
主にはMackerelで使われている時系列DBに関して、その他、社内ツールを参照するためのUnbound、DBやRedis Clusterの冗長構成の仕組み、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のネットワークモニタリングについてのお話でした。</p>

<p>もともとオンプレミスで運用されていたMackerelを、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>側のMackerelとデータセンター側のMackerelで、Mackerel <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>, Mackerel DCといった形で内部的にはサービス名を分けて平行稼働させ、最終的に<a class="keyword" href="http://d.hatena.ne.jp/keyword/DNS">DNS</a>切り替えによって移行されたとのことです。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/DNS">DNS</a>切り替え時 mackerel.io <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/TTL">TTL</a>は60秒にしていたようですが、丸一日は旧環境(DC側Mackerel)にアクセスが来続けていたようです。<a class="keyword" href="http://d.hatena.ne.jp/keyword/TTL">TTL</a>を無視してアクセスが続くケースは多い気がします。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>移行の理由としては、Mackerelで扱っている主に時系列データベースのハードウェア的な限界、具体的には多量な監視メトリック情報を書き込むためにWriteが非常に多い時系列DBのために高スペックなハードウェアを調達し、さらに冗長性やスケール仕組みの構築や運用には手間も人的リソースもかかるため、そういった課題を<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>移行することで解決するため、とのことです。</p>

<ul>
    <li><a href="http://blog.yuuk.io/entry/the-rebuild-of-tsdb-on-cloud" target="_blank" rel="noopener noreferrer">時系列データベースという概念をクラウドの技で再構築する - ゆううきブログ</a></li>
</ul>


<p>元の構成がどんなのかわかりませんが、この時系列DBは、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>移行に伴いマネージメントサービスとEC2上の Redis Cluster を組み合わせたものに作り直したとのこと。
Redis Clusterでは送られて来たメトリックデータを一番最初に保存し、直近のデータを素早くブラウザから参照できるようにするためのものであるとのことで、構成や運用ノウハウについて話されていました。複数の Redis Cluster をシャードで分割しているそうですが、シャードが偏ることがあり、均一化を行う必要があるが、現状運用でカバーしているそうで、ElastiCacheがうまく解決してくれればそちらに寄せたいとのことでした。
Redis Cluster のメトリックは Redis <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>で収集できるものもあるが、 Cluster 全体のイベントは収集できず、Redis Cluster <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>も今はないが、最終的にはMackerelでグラフ化できれば良いとのことで、Mackerel基盤の運用で自分たちがぶつかった課題をMackerelサービスの機能拡張に繋げている印象でした。</p>

<p>また、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>環境から<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%CF%A4%C6%A4%CA">はてな</a>のサービスは社内ツールを使うために、Unboundを立てて社内の権威サーバを参照している構成となっているとのこと。MackerelのサービスもMackerel.ioにメトリックを投稿していおり、io<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>不調時もUnboundのキャッシュのおかげで参照できなくなっていることにすぐに気づけたそうです。</p>

<p>DBやRedis Clusterはアクティブスタンバイの構成となっているそうで、<a class="keyword" href="http://d.hatena.ne.jp/keyword/keepalived">keepalived</a>とルートテーブル(<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>とサブネットのルーティングを制御する設定)を組み合わせており、<a class="keyword" href="http://d.hatena.ne.jp/keyword/keepalived">keepalived</a> がmasterの異常を検知するとルートテーブルを書き換えて faidover させる仕組みを自社内で開発しているとのことでした。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のネットワークモニタリングに関しては、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>側に依存するためオンプレほどは十分に関しできないが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/TCP">TCP</a>パケットの再送や、再送パケットの割合が見れる<a href="https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-snmp" target="_blank" rel="noopener noreferrer">Mackerelプラグイン</a>を使ってUPパケットの再送が<a class="keyword" href="http://d.hatena.ne.jp/keyword/TCP">TCP</a>パケット全体の何割を占めるかなどをモニタリングしているそうです。
またAZ間の通信モニタリングでは<a class="keyword" href="http://d.hatena.ne.jp/keyword/iptables">iptables</a>のnetfilterでどれだけのパケットがKernel空間を通過したかわかるそうです。</p>

<p>Mackerelで使われいる様々な技術に圧倒されましたが、さらにMackerelのシステム基盤自身もMackerelでモニタリングしていることで、その運用ノウハウからさらにMackerelサービスも進化していきそうです。</p>

<h5>Q&amp;A</h5>


<ul>
    <li>Q. 現在の<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のリージョンは東京のみか？外形監視を他のリージョンからやる予定はあるか？</li>
    <li>A. 現在は東京リージョンのみであるがチーム内ではそういう話も出ているので将来的にはやる可能性はある。</li>
</ul>


<ul>
    <li>Q. <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>移行によりコストはどう変わったか?</li>
    <li>A. 当初は同じくらいになる予定だったが、今はそうなるように頑張っている。</li>
</ul>


<ul>
    <li>Q. AZ間の通信は発生するか?</li>
    <li>A. Redis ClusterのAZ間の通信が発生する。</li>
</ul>


<script async="" class="speakerdeck-embed" data-id="b3a93541179e4a7d872f7c8b3194a657" data-ratio="1.33507170795306" src="//speakerdeck.com/assets/embed.js"></script>


<h4>大丈夫！ Mackerel には CRE がいます <a class="keyword" href="http://d.hatena.ne.jp/keyword/%B3%F4%BC%B0%B2%F1%BC%D2%A4%CF%A4%C6%A4%CA">株式会社はてな</a> 井上様</h4>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%CF%A4%C6%A4%CA">はてな</a>CREの井上様による、Mackerelの価値、それらをユーザに届けるためのMackerelプロダクトチームにおけるCREというロールの役割、具体的な取り組みについてのお話でした。</p>

<ul>
    <li><a href="http://developer.hatenastaff.com/entry/2017/08/09/173607" target="_blank" rel="noopener noreferrer">セールスエンジニア 改め Customer Reliability Engineer (CRE) になりました - Hatena Developer Blog</a></li>
</ul>


<p>印象的だったのは、CREチームのKPIとして、問い合わせに対する"同営業日中の返答率 90%"、"翌営業日中の返答率99%"に加えて、"やりとりの平均回数2回未満"を掲げている点でした。現状の平均値は1.5-1.6回だそうで、お問い合わせ文面から、お客様の環境や状況、問題点を正確に探り当てる能力の高さを感じることができました。
また、エンジニアでもあるため、単なるお客様とのやり取りに止まらず、コードを書いて解決策を提示したり、ドキュメントを作成したりMackerelブログを書いたり、ハンズオンや説明会を開催したりと、CREの幅広い役割について理解できました。</p>

<script async="" class="speakerdeck-embed" data-id="a2f688afa24c4d3387f83c50145c5d81" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>


<h4>freeeでMackerelを使って一年間サービスを運用してみた事例紹介 Freee 浅羽様</h4>


<p>Freee 浅羽様からのFreeeでのMackerelの活用事例のお話でした。</p>

<p>導入前は<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>ごとにZabbixを複数利用しており、Zabbixは機能豊富だがSRE３名で運用するには厳しくなってきたため移行を検討し、プロダクトの成長性、AutoScalingとの親和など多面的に評価された上でMackerelを選ばれたとのこと。また、NewRelicやCloudWatchなど、複数の監視ツールを組み合わせて使われいるそうです。目的に応じて複数のプロダクトを組み合わせて使うというのも良いですね。</p>

<p>Mackerelに関しては普段はSRE/開発チーム共に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C0%A5%C3%A5%B7%A5%E5">ダッシュ</a>ボードやサービス一覧を見たり、定期的に実施するパフォーマンス振り返り会でSREがどういう部分を見て何を判断しているかを開発チームと話して認識合わせする、障害時等にもグラフに投稿機能を使ってslackとかでシェアして見れるようにしている、デプロイした時は特にアラートが出やすいのでデプロイジョブのなかで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A5%CE%A5%C6%A1%BC%A5%B7%A5%E7%A5%F3">アノテーション</a>機能を使って記録しておく、アラート通知は基本的にはslackでWarning, Criticalといったアラートレベルに応じて見る人が異なる、など他チームとのコミュニケーションのためにうまく利用しているようです。
また、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C0%A5%C3%A5%B7%A5%E5">ダッシュ</a>ボードの作成はmkrコマンドで行い、<a class="keyword" href="http://d.hatena.ne.jp/keyword/YAML">YAML</a>形式の設定ファイルをバージョン管理しているとのことです。</p>

<h5>QA</h5>


<ul>
    <li>Q. Zabbixの方が良かったと思うところ</li>
    <li>A. 移行に伴い監視項目を整理して、Mackerelに合わせて変更したのであまり意識はしていない。</li>
</ul>


<script async="" class="speakerdeck-embed" data-id="e9bec28fd1ff42f4b33ecaa922ffecfa" data-ratio="1.44428772919605" src="//speakerdeck.com/assets/embed.js"></script>


<h4>Mackerelを導入して変わったN個のこと <a class="keyword" href="http://d.hatena.ne.jp/keyword/GMO">GMO</a> ペパボ 高石様</h4>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/GMO%A5%DA%A5%D1%A5%DC">GMOペパボ</a>高石様より、ホスト1000台規模の環境でのMackerel活用事例のお話でした。
前半はMackerel導入&amp;活用状況、後半は主に独自実装で様々なメトリックを収集している事例のご紹介でした。</p>

<p>元は複数の<a class="keyword" href="http://d.hatena.ne.jp/keyword/Nagios">Nagios</a>だったそうですが情報の一元管理や動的に変化できる<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>ライクなインフラ構成に対応するためにMackerelに移行したそうです。ただ、メンバーとしてジョインした時からMackerelがあったため、当時の詳細についてはご存知ないとのことでした。</p>

<p>アラート通知は日中の監視はslack、夜中はpagerduty/<a href="https://github.com/ryotarai/waker" target="_blank" rel="noopener noreferrer">waker</a>とtwilioを連携されての電話通知とのこと。wakerを建てる前にpagerdutyを使い始めたため２つあり、使い分けているようです。後になってwakerが作られた経緯など少し気になりました。</p>

<p>後半では独自実装で以下のメトリックを収集している事例のご紹介でした。
Mackerelの運用上の課題をMackerelを使って解決する、Mackerelで管理しているホスト情報を使って課題を解決する、取得したいメトリックを自前実装で取得してMackerelに保存する、といったものが主だったかと思います。かなり使い込んでいる印象で圧倒されました。</p>

<ul>
    <li>サービスディスカバリとして使う</li>
    <li>退役忘れ・ロール設定忘れをチェックする</li>
    <li>ロール毎のサーバ数を数える</li>
    <li>Consul未所属サーバの検知</li>
    <li>リリースタイムの計測</li>
    <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%C6%A1%BC%A5%BF%A5%B9%A5%B3%A1%BC%A5%C9">ステータスコード</a>毎のリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト数を取得</li>
    <li>Sidekiqのジョブ数監視</li>
    <li>TreasureDataのジョブ数監視</li>
    <li>GHEのディスク使用量監視</li>
    <li>お問合わせの数を監視</li>
</ul>


<h5>QA</h5>


<ul>
    <li>Q. 運用ツールとしてMackerelでのモニタリングを活用されているということだが、他に面白い使い方しているものはあるか</li>
    <li>A. 金の相場の監視とかは弊社メンバーが個人でやっていた(会場笑)。<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>などの外部サービスの料金を保存して可視化したい。</li>
</ul>


<script async="" class="speakerdeck-embed" data-id="de8ca06ee61345b0acaab7ae13970681" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>


<h4>Driving Mercari with 50+ custom Plugins 株式会社メルカリ 長野様</h4>


<p>メルカリ 長野様からの活用事例のご紹介でした。
細かなService, Roleの設計や様々な独自<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>を使って監視については世界展開するサービスならではという感じで圧巻でした。</p>

<p>Mercariのシステムは、日本ではさくらの専用サーバを、USでは<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>と<a class="keyword" href="http://d.hatena.ne.jp/keyword/GCP">GCP</a>を、UKでは<a class="keyword" href="http://d.hatena.ne.jp/keyword/GCP">GCP</a>が使われており、これらのコアな部分でMackerelが使われているそうですが、US,UKの<a class="keyword" href="http://d.hatena.ne.jp/keyword/GCP">GCP</a>の部分は他のプロメテウス、stackdriverが使われており、さらに<a href="https://github.com/kazeburo/Kurado" target="_brank" rel="noopener noreferrer">kurado</a>と呼ばれる独自ツールやNewRelicも組み合わせているそうです。なお、元は日本、US, UK の環境ごとにZabbixがあったそうですが、複数リージョンで別々のZabbixの管理は手間がかかるためにMackerelに乗り換えたそうです。</p>

<p>Service設計についてはリージョンごとに分け、外形監視用に分けて専用の通知チャネルを用意し、さらにQA環境、マイクロサービスごとにも分けているとのこと。外形監視は頻繁には来ないが影響範囲が大きいので、SREのみならず、CSやプロデューサー、他のエンジニアなども参加しているチャンネルを準備しているそうです。
Role名の設計についてはPrefixつけるようにし、アラート通知の除外フラグなどの意味を持たせており、<code>conf.d</code>以下にRole毎の<code>conf</code>ファイルを置いてincludeさせ、ROLEの自動付与をAnibleを使ってうまくやっているようでした。</p>

<p>セッション内で紹介されたカスタム<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>についてはいくつかは以下で公開されています。
さくらの専用サーバ上でのメモリエラーやHW <a class="keyword" href="http://d.hatena.ne.jp/keyword/RAID">RAID</a>の監視などについても紹介がありましたが、上記の中にはないようで、どんな実装か気になりました。</p>

<ul>
    <li><a href="https://github.com/kazeburo/custom-mackerel-plugins" target="_blank" rel="noopener noreferrer">GitHub - kazeburo/custom-mackerel-plugins: my custom mackerel plugins</a></li>
</ul>


<p>その他の取り組みとして、ペパボ様同様お問い合わせ数や監視漏れしてるサーバの通知などもモニタリングしているとのことでした。</p>

<script async="" class="speakerdeck-embed" data-id="e1b4c44a34ae45bcb4123e31ba634839" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>


<h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Ecosystem with Mackerel <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Japan 酒徳様</h4>


<p>カスタマーユースでの<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%CE%A5%D9%A1%BC%A5%B7%A5%E7%A5%F3">イノベーション</a>(devops周りのサービスのアップデート)と<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D4%A5%C6%A5%F3%A5%B7%A1%BC">コンピテンシー</a>プログラムについてのお話でした。</p>

<p>まず最初の大きなアップデートとしては・・・ロゴが変わった(会場笑)。</p>

<p>今や<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のサービスは90以上あるが、その中でのdevops関連サービスとしては、CodeCommit(<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BD%A1%BC%A5%B9%A5%B3%A1%BC%A5%C9">ソースコード</a>のバージョン管理)、CodeBuild(ビルド自動化)、CodeDeploy/CloudFormation/ElasticBeanstalk(デプロイ自動化)、CodePipeline(ワークフロー管理)とあるが、これらをどう組み合わせれば良いのかというのをよく質問されるとのこと。これらはカスタマーフィードバックに入っており、一元的にデプロイするための <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> CodeStar というサービスがリリースされたのでこれを使うのが良いようです。(ただしまだ東京リージョンにはない)</p>

<p>マルチアカウント、マルチリージョンでのCI/CDに関する質問が多いとのことで、これはCloud Formation スタックという機能によって解決可能、また、CloudFormationで作った設定を誤って消してしまうケースが多いのでこれにプロテクションがかけられるようになったそうです。この辺は使ったことないので全然わからん・・・
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/X-Ray">X-Ray</a>というデプロイした時の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%D0%A5%C3%A5%B0">デバッグ</a>やトレースが取れるサービスも紹介されてましたがこれもわからん・・・</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D4%A5%C6%A5%F3%A5%B7%A1%BC">コンピテンシー</a>プログラムについてですが、業界のデフォルトとなるような仕組みとしての、<a href="https://aws.amazon.com/jp/solutions/solution-providers-japan/" target="_blank" rel="noopener noreferrer">AWSのエコシステムを支える企業は日本だけでも400以上ある</a>そうです。では、これらの中からどれ使えば良いか、というのを判断する基準にもなるのが<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D4%A5%C6%A5%F3%A5%B7%A1%BC">コンピテンシー</a>プログラムだそうです。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%CF%A4%C6%A4%CA">はてな</a>のMackerelはこの中の<a href="https://aws.amazon.com/jp/devops/partner-solutions/" target="_blank" rel="noopener noreferrer">DevOpsコンピテンシープログラム</a>に対して、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Incが定める基準をクリアして取得され、これは日本では事例がない初のものとのこと。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Inc側でも期待値が高く、特に評価されている点として、サービスディスカバリ機能、毎週のアップデート、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>インテグレーションでの<a href="https://mackerel.io/ja/docs/entry/integrations/aws" target="_brank" rel="noopener noreferrer">セキュリティ要件を満たしている点</a>、Mackerelの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3">アーキテクチャ</a>、マネージドサービスをふんだんに利用している点などを挙げられていました。</p>

<p>最後に、パートナー各社の<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>上での実装について説明されている動画(英語)と、re:Invent についてのご紹介で締めくくられました。</p>

<ul>
    <li><a href="https://aws.amazon.com/jp/this-is-my-architecture/" target="_blank" rel="noopener noreferrer">AWS | This Is My Architecture</a></li>
</ul>


<h3>まとめ</h3>


<p>Mackerelはまだそれほど使っていないものの、いろいろな環境での事例を聞けてとても参考になりました。単なるサーバインフラの監視に止まらず、様々なモニタリングを行うためのサービスという印象でした。
企画・運営してくださった<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%CF%A4%C6%A4%CA">はてな</a>の皆様、登壇者の皆様、ありがとうございました。</p>
</body>
