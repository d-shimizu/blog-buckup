---
layout: post
Categories:  ["tech"]
Description:  " はじめに   2013年6月5日(水)、6日(木)の2日間、東京で開催されたAmazon Web Serviceのクラウドカンファレンス「AWS Summit Tokyo 2013」のDay2(6日(木))へ行ってきました。      ア"
Tags: []
date: "2013-06-10T23:49:00+09:00"
title: "AWS Summit Tokyo 2013 (Day 2) に行ってきました"
author: "dshimizu"
archive: ["2013"]
draft: "true"
---

<body>
<h4>はじめに</h4> <p>2013年6月5日(水)、6日(木)の2日間、東京で開催された<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Web Serviceの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>カンファレンス「<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Summit Tokyo 2013」のDay2(6日(木))へ行ってきました。 </p>
<ul>  <li><a href="http://www.awssummittokyo.com/">アマゾンのクラウドカンファレンス開催！| AWS SUMMIT TOKYO 2013 (2013/6/5～6）</a></li>
</ul>
<div align="center"><a href="http://4.bp.blogspot.com/-NFGWCwf8KbE/UbSrJov4bwI/AAAAAAAAAVs/om0Fkvyrz0w/s1600/main.png" imageanchor="1"><img border="0" src="http://4.bp.blogspot.com/-NFGWCwf8KbE/UbSrJov4bwI/AAAAAAAAAVs/om0Fkvyrz0w/s720/main.png"></a></div>
<a name="more"></a><br><p>いろいろな話を聞いたり<a class="keyword" href="http://d.hatena.ne.jp/keyword/Twitter">Twitter</a>の内容を見たりしていると、いろいろな理由で現状では<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>(やその他<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>)の利用に乗り出せない企業様も多くいらっしゃるように思いましたが、本カンファレンスではそういった<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>に移行するために、オンプレミスと比較した場合の導入コストや運用方法を中心に、それに伴う人的リソース、セキュリティやデータ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%CA%DD%C1%B4">保全</a>等の障壁をどうクリアしていけば良いのか、といった点に注目が集まっていたように思います。<br>私自身も<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>を全く使ったことはないので、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の既存機能や用語などについて詳しく分かっていない部分も多々ありましたが、上記のような点についてイメージしながら今後使いどころを考えていければと思って参加しました。 </p>
<p>聴講してきたセッションのうち以下について、祖末ながら取ったメモを復習の意味も兼ねて以下に簡単にまとめて残しておきます。 </p>
<ul>  <li>【EP】 中とろより価値あるITを。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%A2%A4%AD%A4%F3%A4%C9%A5%B9%A5%B7%A5%ED%A1%BC">あきんどスシロー</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>活用術</li>  <li>【Tech】<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Redshiftが切り開く<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>・データウェアハウス</li>  <li>【CS】大規模案件の裏側 ～巨大<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>インフラ事例のご紹介～</li>  <li>【Tech】ネット選挙<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>　～<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AA%A5%D0%A5%DE">オバマ</a>大統領選挙の事例：データ解析からネット募金まで～</li>
</ul>
<h4>セッション</h4>
<h5>【EP】 中とろより価値あるITを。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%A2%A4%AD%A4%F3%A4%C9%A5%B9%A5%B7%A5%ED%A1%BC">あきんどスシロー</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>活用術</h5>
<p>株式会社<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%A2%A4%AD%A4%F3%A4%C9%A5%B9%A5%B7%A5%ED%A1%BC">あきんどスシロー</a> 情報システム部 部長の田中 覚 様より、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>上で構築された企業ホームページ、すしの売り上げ分析システム、テイクアウトの受注システムについてのお話でした。 </p>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>選択の経緯や導入後の運用実績、既存の業務などビジネス面を考慮した上でのデータ活用事例などについて具体的にご説明されていて、個人的にはこのセッションが一番面白かったです。 </p>
<p>また、やるべきことや問題点が明確で、その要件に合ったインフラ基盤として<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>がベストでそれを有効活用している、というイメージでした。データ分析をきちんと行っており、実際にどういったことをやっているのかの概要を知ることができたのも良かったです。 </p> <ul>  <li><a href="http://ascii.jp/elem/000/000/795/795978/">ASCII.jp：寿司もITも鮮度が一番！あきんどスシローのAWS活用術 (1/2)</a></li>
</ul>
<h6>企業ホームページの<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>移行</h6>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>を選択した経緯としては、「中トロ販売もIT投資もどちらも投資、どちらがよりお客様に喜ばれるか」という企業としてのIT/システム投資に関する捉え方、従業員3万人超に対して情シス5人という体制からの運用や管理面を考慮してだそうです。 </p>
<p>最初に<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>へ移行したのはホームページだったそうです。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%A2%A4%AD%A4%F3%A4%C9%A5%B9%A5%B7%A5%ED%A1%BC">あきんどスシロー</a>社のホームページにはメニューや店舗の場所などが載っているため、平日では10万PV程のアクセスが土日や大型連休になると来店される方が増えるため18万PVほどになり、TVで同社が放送されるようなことがあると、さらにリアルタイムにアクセスが増え、お願いランキングで紹介されたときは45万PVまで達したとのことでした。<br>テレビ放送があるときは事前に知らされているため、その日にあわせて事前にサーバを増強するようにスケジューリングしておくことで対応したとのこと。具体的には<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のイメージコピーをスケジューリングし、さらに現在は<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%DE%A1%BC%A5%C8%A5%D5%A5%A9%A5%F3">スマートフォン</a>や<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BF%A5%D6%A5%EC%A5%C3%A5%C8">タブレット</a>などのデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>も主流であるため、テレビ放送された瞬間にリアルタイムに突発的なアクセス増が発生するため、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%D5%A5%A3%A5%C3%A5%AF">トラフィック</a>に応じて動的にサーバ増設できるようオートスケールも設定して備えていたそうです。<br>オートスケールは便利だが費用が高いため、基本は低コストで済む事前のサーバ増設で対応し、念のために可用性確保の目的としてオートスケールを仕掛けておき、最終的にはオートスケールも使わずに済んだため、最終機には準備・対応にかかった追加費用は5万円程度で済んだとのことでした。また、イメージバックアップのコピーはDRとしても活用しているそうです。<br>このような特性のWEBサイトの運用は<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のような柔軟性のある基盤はマッチしているんだろうな、と思いました。 </p>
<h6>蓄積された大量データを<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>での分析</h6>
<p>続いて、同社が既に保持しているデータの活用についてでした。<br>新鮮な寿司を提供するために、皿の裏に<a class="keyword" href="http://d.hatena.ne.jp/keyword/IC%A5%BF%A5%B0">ICタグ</a>をつけておき、15分経過すると自動で廃棄する仕組みや、来店するお客様の家族構成や注文内容のデータから、着席後の需要予測を各店舗の店長が見てレーンにネタの内容や量を決めているらしいが、そうしたデータが現状約40億件蓄積されていたが、それを扱う手段が無かったとのこと。そこで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>を使うことを検討し、過去2年分のデータを使い、ウィングアーク社の「Dr.Sum EA」で集計し、BIツール「Motion Board」で可視化するという分析基盤環境を<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>で作成し、それを持って役員を説得し、導入に至ったとのことでした。<br>なお、検証環境の構築にかかった期間は2日、費用は10万円程度だったそうで、ちょっと作って試すという使い方も十分という利点が示されたように思います。これからはこういった使われ方も一般的になっていきそうです。 </p>
<p>この分析基盤を利用して、流通サイドからはネタの売れ筋から<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%ED%A5%F3%A5%B0%A5%C6%A1%BC%A5%EB">ロングテール</a>商品や地域毎の人気ネタを見て店舗ごとに商品の提供をコン<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%ED%A1%BC%A5%EB">トロール</a>したり、営業サイドからは新規店舗と既存店舗の廃棄率の差を確認等を行っているようです。<br></p>
<p>現在は、各店舗内でテイクアウト(持ち帰り)の注文をとれるシステムの導入を検討しているとのこと。手書き伝票で受注管理している既存の方式だと、お客様にも分かりにくく、店舗側もミスや人的コストも発生しがちな点が課題だそうです。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>上で試験的に構築し、うまくいかなければすぐやめるというスタンスでチャレンジしているそうです。 </p>
<h6>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%A2%A4%AD%A4%F3%A4%C9%A5%B9%A5%B7%A5%ED%A1%BC">あきんどスシロー</a>社の開発運用体制</h6>
<p>気になる開発・運用体制ですが、システム設計やデザインは東京のベンダーが、開発やテスト、運用監視は国外(中国)にオフショア、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>利用時に不安点としてあげられがちなセキュリティについては、第3者(NTT Data社)へ委託しているとのこと。また、データ保護や災害対策として、最も低コストで実現できるやり方を<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a>データサービスの営業の方に相談し、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のストレージ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B2%A1%BC%A5%C8%A5%A6%A5%A7%A5%A4">ゲートウェイ</a>を<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%F3%A5%AC%A5%DD%A1%BC%A5%EB">シンガポール</a>のS3にコピーすることで実現しているようです。 </p>
<h5>【Tech】<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Redshiftが切り開く<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>・データウェアハウス</h5>
<p>アマゾンデータサービスジャパンの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C8%AC%CC%DA%B6%B6">八木橋</a> 徹平 様による、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>上のデータウェアハウス(DWH)サービス「<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Redshift」についての紹介でした。 6月5日に東京リージョンでの提供も開始されたそうです。 </p>
<ul>  <li><a href="http://cloud.watch.impress.co.jp/docs/event/20130607_602607.html">AWS日本法人の長崎社長が多様なクラウドの使い方を紹介、Redshiftの実機評価結果も ～AWS Summit Tokyo 2013 2日目レポート </a></li>
</ul>
<p>講演内容の結論としては「スケールすることで性能向上」「<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%C3%A5%C1%BD%E8%CD%FD">バッチ処理</a>は得意」「チューニングは新しい概念で」「利用料課金は安い」。<br>ただ、私はこの分野はまだ勉強不足なので、あまり利用イメージがわきませんでした。以下はとりあえず聞いたことのまとめです。 </p>
<h6>オンプレミスでDWHを構築する場合の課題</h6>
<p>まず、オンプレミスのDWHについての課題として、初期投資や運用管理、データ量増加を見越したシステムを構築しようとした場合の成長予測とその費用対効果が見えない点をあげられていました。 <a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> Redshiftを利用することで、これらの問題点を改善できるだろう、とのことです。 </p>
<h6>Redshiftの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3">アーキテクチャ</a>
</h6>
<p>RedshiftはElastic <a class="keyword" href="http://d.hatena.ne.jp/keyword/MapReduce">MapReduce</a>(EMR)と同じく分析処理向けのサービスであるが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/PostgreSQL">PostgreSQL</a>ベースで<a class="keyword" href="http://d.hatena.ne.jp/keyword/JDBC">JDBC</a>ドライバにより<a class="keyword" href="http://d.hatena.ne.jp/keyword/SQL">SQL</a>を扱える点がEMRと大きく異なるとのことです。<br>構成例として、RDSや<a class="keyword" href="http://d.hatena.ne.jp/keyword/Dynamo">Dynamo</a> DBなどの様々なデータストアからS3/EMRにデータを蓄積してRedshiftで読み込む方法が紹介されました。 </p>
<p>また、カラムナ(列指向)型データベースで集計などの処理を高速に実行可能、ノード数を増やすことで数TB〜数PBまでスケールさせることで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>構成をとることができるそうです。<br>各マルチノード<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>は1台のリーダーノードと2つ以上のコンピュータノードを持つことになり、リーダーノードは接続、クエリの解析、実行計画の構築、およびコンピュータノードでのクエリ実行を管理します。 コンピュータノードはデータを保存し、計算を行い、リーダーノードによって指示されたクエリを実行します。リーダーノードではC+のCodeを生成してクエリを並列化し、全コンピュータノードで分散・並列処理をさせることが可能になります。バックアップや<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>のリサイズは管理画面上から実施可能とのことです。バックアップはS3と連携しての増分/<a class="keyword" href="http://d.hatena.ne.jp/keyword/%BA%B9%CA%AC%A5%D0%A5%C3%A5%AF%A5%A2%A5%C3%A5%D7">差分バックアップ</a>を取得できるようです。<br><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>のリサイズは、バックエンドで別の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>が用意され、そこにデータがコピーされ、<a class="keyword" href="http://d.hatena.ne.jp/keyword/DNS">DNS</a>によりエンドポイントが切り替わりが行われるような動作となるとのことでした。 </p>
<h6>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%CC%EE%C2%BC%C1%ED%B8%A6">野村総研</a>(<a class="keyword" href="http://d.hatena.ne.jp/keyword/NRI">NRI</a>)様によるRedshift検証結果報告</h6>
<p>続いて、2012年末にRedshiftが限定公開されていたときに、先行して評価に参加していた<a class="keyword" href="http://d.hatena.ne.jp/keyword/%CC%EE%C2%BC%C1%ED%B8%A6">野村総研</a>(<a class="keyword" href="http://d.hatena.ne.jp/keyword/NRI">NRI</a>)様による検証結果などを解説いただきました。  </p>
<p>大きなテーブル(500億件のデータ)からの検索処理、データのロード処理、小さな(?)テーブル(1.2億件のデータ)の検索処理の2ノード、4ノード、8ノードでの性能評価で、いずれもスケールアウトすることで性能が向上したようです。唯一小さなテーブルでの検索処理で4ノードから8ノードへスケールした際に性能が落ちたようですが、分散することによるオーバーヘッドではないか、とのことでした。<br>他、EMRとRedshiftの比較においては、1.5TB、500億件でのJOIN＋集計処理ではRedshiftの方が性能が良くなったと紹介されてました。 </p>
<p>Redshiftのチューニングポイントとして、Indexが存在しないため、代わりとしてDistribution Keyの利用をあげられていました。小さなテーブル(1.2億件のデータ)におけるDistribution Keyを指定する場合としない場合の検索処理においてはDistribution Keyを指定した場合の処理時間の向上が見られたとのことです。Sort Keyを入れると各ノードへの割り振りに時間がかるのでデータロードがその分遅くなるようです。 </p>
<p>具体的には分かりませんでしたがRedshiftも不得意な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A1%BC%A5%BF%B7%C1%BC%B0">データ形式</a>があるため、そのようなデータがある場合は、それに適した他の<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>サービスを利用する等が良いようです。<a class="keyword" href="http://d.hatena.ne.jp/keyword/NRI">NRI</a>様ではそのようなデータをEMRで処理するようにした、とのことでした。 </p>
<h6>Redshift対応のソフトウェアの御紹介</h6>
<p>最後に、Redshiftに対応したソフトウェア類を御紹介されていました。現在オンプレミスにあるデータをRedshiftへ移行するツールとしてはインフォテリアの<a class="keyword" href="http://d.hatena.ne.jp/keyword/EAI">EAI</a>製品「<a class="keyword" href="http://d.hatena.ne.jp/keyword/Asteria">Asteria</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/Warp">Warp</a>」、BIツールとしてはKSKアナリティクスが販売する「Pentaho」、ワークブレイン・ジャパンが販売する「Jaspersoft」がRedshiftを正式にサポートしているそうです。 </p>
<h6>参考</h6>
<ul>  <li><a href="http://www.slideshare.net/AmazonWebServicesJapan/amazon-redshift">Amazon redshiftのご紹介</a></li>
</ul>
<h5>【CS】大規模案件の裏側 ～巨大<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>インフラ事例のご紹介～</h5>
<p>アイレット様のcloudback事例の御紹介でした。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B1%A5%F3%A5%B3%A1%BC%A5%B3%A5%E0">ケンコーコム</a>様のSAPの事例をはじめ、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C6%FC%CB%DC%A5%C6%A5%EC%A5%D3">日本テレビ</a>様、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E8%A5%BF">トヨタ</a>様、ローランド様など数々の<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>導入・移行を手がけ、200社超の顧客を手がけているそうです。資料が公開されています。 </p>
<ul>  <li><a href="http://www.slideshare.net/kaz.goto/aws-22531518">大規模案件の裏側 ～巨大AWSインフラ事例のご紹介～ - SlideShare</a></li>
</ul>
<h6>日本TV様のソーシャルテレビエンターテインメント JoinTVのシステムの<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の活用事例</h6>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%C6%FC%CB%DC%A5%C6%A5%EC%A5%D3">日本テレビ</a>が提供する、テレビを見ながら<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%DE%A1%BC%A5%C8%A5%D5%A5%A9%A5%F3">スマートフォン</a>やリモコンで番組に参加できるサービス"JoinTV"の<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>活用事例でした。 </p>
<p>テレビという大きな規模ではピーク時のオートスケールでは処理が間に合わないので、事前のプロビジョニングが必須だったとのことです。具体的にはリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト処理をSQSでキューに蓄積し、非同期処理でレスポンスを返す等の施策をとられた、と御説明されてました。<br>簡単ではないのでしょうけど、正しいタイミングできちんとスケールアウトできる構成にした上で、高負荷時の対策をしっかりやっていれば、この規模のシステムの<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>での柔軟性ある運用もメリットありということでしょうか。高負荷時のパフォーマンスチューニングをどうやったのか等は気になります。 </p>
<h6>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B1%A5%F3%A5%B3%A1%BC%A5%B3%A5%E0">ケンコーコム</a>様のシステムの<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>活用事例</h6>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%C5%EC%C6%FC%CB%DC%C2%E7%BF%CC%BA%D2">東日本大震災</a>を機に、システム全体をオンプレミスから<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>へ移行されたとのことです。構成としてはEC2を50台、RDSを5台。元々SiteGuardを導入していたそうですが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>での利用がライセンス面で問題があったため、CDPのWAF Proxyを固定台数用意する構成をとられたとのこと。 </p>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>移行は実績やノウハウが多い会社(人柱となってきた会社…)に任せたほうがオンプレミス設計のズレなどの吸収、帳尻合わせが容易になるため良いとのことで、これはおっしゃれた通りだと思いました。<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>自体は誰でも手軽に利用できるものだと思いますが、既存システムを何も改変せずに自分たちだけでそのまま移行しようとするのは危険そうです。 </p>
<h6>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/TOYOTA">TOYOTA</a>社の<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>活用事例</h6>
<p>サイトの規模としては月間1億PVと大規模。特に新車発表時のアクセスは3倍になるそうです。DRとしては東京リージョンの障害時に備えて<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%F3%A5%AC%A5%DD%A1%BC%A5%EB">シンガポール</a>リージョンを利用されているそうです。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%A2%A4%AD%A4%F3%A4%C9%A5%B9%A5%B7%A5%ED%A1%BC">あきんどスシロー</a>様もDRに<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%F3%A5%AC%A5%DD%A1%BC%A5%EB">シンガポール</a>リージョンを利用されていたので、やはりそれが一番ということでしょうか。バックアップについては<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>を信用せずオンプレミス環境に取得しているという点も面白いです。CloudFormationを使ってテンプレートから一発で環境構築可能にしているそうです。 </p>
<h6>参考</h6>
<ul>  <li><a href="http://aws.amazon.com/jp/solutions/case-studies/kenkocom/">導入事例: ケンコーコム株式会社 | アマゾン ウェブ サービス（AWS 日本語）</a></li>
</ul>
<h6>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/TOYOTA">TOYOTA</a>様のシステムの<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>活用事例</h6>
<h5>【Tech】ネット選挙<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>　～<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AA%A5%D0%A5%DE">オバマ</a>大統領選挙の事例：データ解析からネット募金まで～</h5>
<p>Miles Ward氏による英語での講演でした。内容としては基本的に以下の記事の内容とほぼ同じような感じだったと思います。 </p>
<ul>  <li><a href="http://www.publickey1.jp/blog/13/obama_for_america.html">「Obama For America」の開発チームが作り上げた大規模な選挙キャンペーンシステムの舞台裏（前編）</a></li>  <li><a href="http://www.publickey1.jp/blog/13/obama_for_america_1.html">「Obama For America」の開発チームが作り上げた大規模な選挙キャンペーンシステムの舞台裏（後編）</a></li>
</ul>
<h6>参考</h6>
<ul>  <li><a href="http://awsofa.info/">システム構成</a></li>
</ul>
<p>作成された<a class="keyword" href="http://d.hatena.ne.jp/keyword/Rails">Rails</a>アプリの一つだそうです。 </p>
<ul>  <li><a href="https://github.com/democrats/voter-registration">democrats/voter-registration · GitHub</a></li>
</ul>
<h4>おわりに</h4>
<p>最後になりましたが、このような機会を設けていただいたアマゾン データ サービス ジャパン株式会社様をはじめとする関係者の皆様、ありがとうございました。大変勉強になり、また、楽しませていただきました。 </p>
<a href="http://4.bp.blogspot.com/-LaR8Dy8jhsE/UbSSS119veI/AAAAAAAAAVM/_jmjhA8VfRQ/s1600/IMG_20130606_191248.jpg" imageanchor="1"><img border="0" src="http://4.bp.blogspot.com/-LaR8Dy8jhsE/UbSSS119veI/AAAAAAAAAVM/_jmjhA8VfRQ/s320/IMG_20130606_191248.jpg"></a><br><p><b><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>いつやるの？<a class="keyword" href="http://d.hatena.ne.jp/keyword/%BA%A3%A4%C7%A4%B7%A4%E7%A1%AA">今でしょ！</a></b></p>
<h4>その他参考</h4>
<p>2日間のセッションの様子は下記の通りにまとめられていました。 </p>
<ul>  <li><a href="http://togetter.com/li/513720">#awssummit AWS Summit Tokyo 2013 Day1 つぶやきまとめ - Togetter</a></li>  <li><a href="http://togetter.com/li/514270">#awssummit AWS Summit Tokyo 2013 Day2 つぶやきまとめ - Togetter</a></li>
</ul>
</body>

<!-- more -->


