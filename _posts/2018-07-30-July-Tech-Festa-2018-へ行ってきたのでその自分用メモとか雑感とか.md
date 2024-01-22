---
layout: post
Categories:  ["tech"]
Description:  " July Tech Festa 2018 へ行ってきたのでそのメモとか雑感とかを書いておく。       July Tech Festa 2018  "
Tags: ["tech", "イベント"]
date: "2018-07-30T23:16:00+09:00"
title: "July Tech Festa 2018 へ行ってきたのでその自分用メモとか雑感とか"
author: "dshimizu"
archive: ["2018"]
draft: "true"
---

<body>
<p>July Tech Festa 2018 へ行ってきたのでそのメモとか雑感とかを書いておく。</p>

<ul>
    <li><a href="https://2018.techfesta.jp/" target="_brank" rel="noopener noreferrer">July Tech Festa 2018</a></li>
</ul>

</body>

<!-- more -->

<body>
<h3>雑感</h3>


<p>聞きたいセッションはたくさんあったが会場Aにずっといてそこのセッション(以下)を聞いていた。</p>

<ul>
    <li>10:00-11:00　　A00: <a herf="https://www.slideshare.net/pfi/tech-inmlcluster20180729-julytechfesta2018" target="_brank" rel="noopener noreferrer">Preferred Networksの機械学習クラスタを支える技術</a>
</li>
    <li>11:15-12:00　　A10: 組織の生産性を上げる役割「VP of Engineering」とは？</li>
    <li>13:00-13:45　　A20: <a href="https://speakerdeck.com/koudaiii/number-jtf2018" target="_brank" rel="noopener noreferrer">Site Reliability･Developer Productivity･技術戦略の取り組み</a>
</li>
    <li>14:00-14:45　　A30: 明日から実践できる！運用自動化に必要な「テスト」にまつわる技術</li>
    <li>15:15-16:00　　A40: <a href="https://speakerdeck.com/katsuhisa91/zui-gao-falseitenziniaringuwozhi-erushou-ritogong-mefalse-she-ji-ji-shu-to-sre" target="_brank" rel="noopener noreferrer">最高のITエンジニアリングを支える守りと攻めの「設計技術」と「SRE」</a>
</li>
    <li>16:15-17:00　　A50: <a href="https://docs.google.com/presentation/d/1S049J92RUTaRJXrE95jWs01IS2SLeIxxWIp2IYBxjuI/mobilepresent?slide=id.g3d042fabc1_0_1768" target="_brank" rel="noopener noreferrer">ミランティスが1万台のサーバーを監視・運用するためにInfrastructure as Codeを利用している話</a>
</li>
    <li>17:30-18:15　　A60: <a href="https://speakerdeck.com/kazeburo/mercari-infrastructure-and-software" target="_brank" rel="noopener noreferrer">ソフトウェアで構築する成長し続けるインフラストラクチャとメルカリの挑戦</a>
</li>
</ul>


<p>SREという役割の隆盛を受けてか、今年はシステム運用管理×ソフトウェアエンジニアリングによる問題解決やマネジメント系の話が去年より増えていた気がする。
インフラエンジニアというとサーバ管理とかハードウェアやネットワークをごちゃごちゃやるような感じをイメージされることも多かったけど、最近だと<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>でいい感じのコンテナ環境作ってたり、Goとか<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a>とかでいろんなツールを作って運用改善しました、みたいな人も多い。必要なスキルセットの幅が増えてきたというかインフラエンジニアの領分が変わってきているのを感じた。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>の台頭で、そこまで低レイヤの深い知識を求められることが少なくなってきているのもあるんだろうな...(前から言われていることではあるが)</p>

<h4>A00: Preferred Networksの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B5%A1%B3%A3%B3%D8%BD%AC">機械学習</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>を支える技術</h4>


<p>PFNさん大村様による基調講演から始まった。
PFN社での大村様の主な取り組みとしては、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B5%A1%B3%A3%B3%D8%BD%AC">機械学習</a>向けの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーの開発・運用と<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a>で書かれた<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AA%A1%BC%A5%D7%A5%F3%A5%BD%A1%BC%A5%B9">オープンソース</a>の深層学習向け<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%EC%A1%BC%A5%E0%A5%EF%A1%BC%A5%AF">フレームワーク</a>「Chainer」を開発とのこと。
分散深層学習でChainerを簡単に使うための「ChainerMN」を<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>(<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>)や<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>上で簡単に使うためのツールを作られているらしい。</p>

<ul>
    <li><a href="https://github.com/chainer/chainermn" target="_blank" rel="noopener noreferrer">GitHub - chainer/chainermn: ChainerMN: Scalable distributed deep learning with Chainer</a></li>
    <li><a href="https://chainer.org/general/2018/06/01/chainermn-on-aws-with-cloudformation.html" target="_blank" rel="noopener noreferrer">ChainerMN on AWS with CloudFormation</a></li>
    <li><a herf="https://github.com/kubeflow/chainer-operator" target="_blank" rel="noopener noreferrer">GitHub - kubeflow/chainer-operator: Repository for chainer operator</a></li>
</ul>


<p>事業として<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B5%A1%B3%A3%B3%D8%BD%AC">機械学習</a>をやられているのは知っていたが、IoT方面で色々な取り組みをしていたことは知らなかった。</p>

<ul>
    <li><a href="https://www.youtube.com/watch?v=ATXJ5dzOcDw" target="_blank" rel="noopener noreferrer">バラ積みロボット: 深層学習で教示レス動作獲得</a></li>
    <li><a href="https://www.youtube.com/watch?v=7A9UwxvgcV0" target="_blank" rel="noopener noreferrer">Autonomous robot car control demonstration in CES2016</a></li>
    <li><a href="https://www.youtube.com/watch?v=5pSq7qFAL0M" target="_blank" rel="noopener noreferrer">PFN's bin-picking demo at IEEE ICRA 2017 - YouTube</a></li>
</ul>


<p>上記の深層学習の研究を支えているのがChainer。(使ったことないから詳しいことはワカラン...)</p>

<p>自社の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B5%A1%B3%A3%B3%D8%BD%AC">機械学習</a>用<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>環境で MN-1 と呼ばれているとのこと。
合計1024個の<a class="keyword" href="http://d.hatena.ne.jp/keyword/GPU">GPU</a>を利用でき、内部のデータ通信にはInfiniBandが用いられた<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>環境。凄まじい...
構築・運用はNTT PC Communications社、NTT PC Communications社(NTT Communicationsの子会社)にも協力いただいているそうだ。</p>

<ul>
    <li><a href="https://www.preferred-networks.jp/ja/news/pr20171114" target="_brank" rel="noopener noreferrer">Preferred Networksのプライベート・スーパーコンピュータが Top 500リストのIndustry領域で国内１位に認定 – Preferred Networks</a></li>
    <li><a href="https://www.preferred-networks.jp/ja/news/pr20171110" target="_brank" rel="noopener noreferrer">Preferred Networks、深層学習の学習速度において世界最速を実現 – Preferred Networks</a></li>
    <li><a href="https://tech.nikkeibp.co.jp/atcl/nxt/news/18/00624/" target="_brank" rel="noopener noreferrer">PFN、最新GPUで拡張したプライベートスパコンを7月に稼働 | 日経 xTECH（クロステック）</a></li>
</ul>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>全盛の時代でPFNが自社で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B5%A1%B3%A3%B3%D8%BD%AC">機械学習</a>環境を持つのには以下の理由があるとのこと。</p>

<ol>
    <li>計算力が競争力の源泉と考えれており、それを自社で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%CA%DD%CD%AD">保有</a>しておきたい。それによって大きな成果をあげたい。かつて<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/GPU">GPU</a>枯渇もあった。</li>
    <li>日々の研究で息をするように使える環境を持ちたい</li>
    <li>ChainerMNで使うInfinibandのような高速な通信環境は<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>でもなかなか使えない</li>
    <li>上から下まで(HW〜アプリまで)のスキルを自社で持つことの重要性</li>
</ol>


<p>こういった明確で強い思いを持った上で自社でこのような規模の環境を構築・運用されているケースはなかなかないと思うので素晴らしいと思った。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%B5%A1%B3%A3%B3%D8%BD%AC">機械学習</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>は全てオンプレではなく、CIツール(jenkins)やHarbor(Private Docker Registy)は<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>で、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A1%BC%A5%F3">インターン</a>の方など用にボット経由で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>を立ち上げるAzureの環境もあるとのこと。
オンプレの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>環境は、ストレージはGlusterFS/<a class="keyword" href="http://d.hatena.ne.jp/keyword/HDFS">HDFS</a>、コンテナはDocker、コンテナ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AA%A1%BC%A5%B1%A5%B9%A5%C8%A5%EC%A1%BC%A5%B7%A5%E7%A5%F3">オーケストレーション</a>ツールは<a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache%20Mesos">Apache Mesos</a>と<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>を別々で利用、その上で内製のジョブツールが動いているような構成。<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>やJupiter Hubも利用者がアクセス可能。その他監視はData Dog、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BD%A1%BC%A5%B9%A5%B3%A1%BC%A5%C9">ソースコード</a>管理にGHE、そして全てSSOでログイン可能とのこと。</p>

<p>内製のジョブスケジューラは、利用者がローカルのGit<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>でコードを管理し、<a class="keyword" href="http://d.hatena.ne.jp/keyword/GPU">GPU</a>をいくつ使うとか、どのライブラリが必要かとかを全て定義したものを<a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a>でジョブを投げるとDockerイメージが起動し、そこでジョブが動くうようになっているとのこと。必要な学習データは<a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a>経由でアップロードできるようになっているらしい。</p>

<p>色々なツールが乱立しているが、PFNの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>環境利用者にはITエンジニア以外にも色々な分野の研究者など、さまざまなバックグラウンドをもった方々がいらっしゃるとのことで、そういった非IT系の研究者の方々にとってはDockerや<a class="keyword" href="http://d.hatena.ne.jp/keyword/Kubernetes">Kubernetes</a>というのは関心事ではなく、自分たちの実行したい計算がおこなえる環境があることが重要であるため、そのために利用者の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%C6%A5%E9%A5%B7%A1%BC">リテラシー</a>レベルやニーズにあわせていくつかの実行環境・操作方法を準備しているとのこと。</p>

<p>後半は実行される深層学習の処理に関するノウハウやMesos/<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>経由での<a class="keyword" href="http://d.hatena.ne.jp/keyword/GPU">GPU</a>やInfinibandの使い方などに関する説明が多かった。こういった環境にはご縁がないためテクニカル面での細かい点は聞いているだけではわからない部分も多かったが、色々な利用者に配慮した設計思想については意識したい。</p>

<h4>A10: 組織の生産性を上げる役割「VP of Engineering」とは？</h4>


<p>株式会社メルカリ 是澤太志様(<a href="https://twitter.com/bsak0620" target="_brank" rel="noopener noreferrer">@sak0620</a>)、株式会社ハートビーツ 高村成道様(<a href="https://twitter.com/nari_ex" target="_brank" rel="noopener noreferrer">@nari_ex</a>)、株式会社<a class="keyword" href="http://d.hatena.ne.jp/keyword/Gunosy">Gunosy</a> 加藤慶一様(<a href="https://twitter.com/s_wool" target="_brank" rel="noopener noreferrer">@s_wool</a>)によるパネルディスカッション形式でのセッション。</p>

<h5>「VP of Engineering」立ち上げのストーリー</h5>


<p>登壇者様の自己紹介のあと、まずは各社の「VP of Engineering」立ち上げについて。</p>

<p><b><u>メルカリ社 是澤様の場合</u></b></p>

<p>２０１７年4月にCTOとVPoEの2頭体制になったとのこと。エンジニアが１００名を超えてきたあたりでよりグローバルで戦える、スケールできる組織を目指して技術のマネジメントと組織のマネジメントに分離したそうだ。</p>

<ul>
    <li><a href="https://mercan.mercari.com/entry/2017/04/19/133000" target="_brank" rel="noopener noreferrer">メルカリCTO交代、VP of Engineeringの2頭体制に - mercan（メルカン）</a></li>
    <li><a href="http://hrnabi.com/2017/06/29/14500/" target="_brank" rel="noopener noreferrer">メルカリはなぜ「2頭体制」に踏み切ったのか？ 拡大するスタートアップの組織再編成 | HRナビ by リクルート</a></li>
</ul>


<p><b><u>ハートビーツ社 高村様の場合</u></b></p>

<p>２０１０年くらいは30人規模の会社でCTOがVPoEの役割も担っていたが、現在は70人ほどに増えてそれに伴いやることが単純に増えて来たので分離されたとのこと。</p>

<p><b><u>グノシー社 加藤様の場合</u></b></p>

<p>ハートビーツ社同様もともとCTOが同一の役割を担っていた。ただ、メンバーの悩みといったマネジメントに取られる時間が増えて来たため、マネジメントの責任を別な人がやるようにすることで、各々が得意な部分に時間を使えるようにしたとのこと。加えて事業・サービスの責任者をプロダクトごとに複数人、別に設けているとのこと。</p>

<ul>
    <li><a href="https://tech.gunosy.io/entry/cto-vpoe-vpop" target="_brank" rel="noopener noreferrer">CTO・VPoE・VPoPの分立とCTO - Gunosy Tech Blog</a></li>
</ul>


<h5>VPoEとCTOの役割の違いは？</h5>


<p><b><u>メルカリ社 是澤様の場合</u></b></p>

<p>技術のマネジメントと組織のマネジメントでまずは別れる。
CTOは技術的提案と意思決定、その成果に責任を持つ。技術のレベルを引き上げたり、経営に技術的優位性を与えるのがCTOの責務。是澤様としてはアップルの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%C6%A5%A3%A1%BC%A5%D6%A1%A6%A5%A6%A5%A9%A5%BA%A5%CB%A5%A2%A5%C3%A5%AF">スティーブ・ウォズニアック</a>が理想的とのこと。経営に対して<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%D1%A5%AF">インパク</a>トを与えるほどの技術力でプロダクトを作り出した点が理想的。
VPoE/組織のマネジメントは範囲が広い。エンジニアリングに関するあらゆる成果に責任を持つ。ただ、責任範囲が曖昧。経費精算の仕組み化など、エンジニアリングに集中してもらうためになんとかしないといけない面倒臭いことはたくさんある。これらを改善するためにエンジニアリング側のマネージャーに協力してもらう必要があり、VPoEが何人居ても足りないが、CTO、及び経営の成果を最大化するための課題に取り組む、仕組み化して解決する、といったところをエンジニアの視点を持って経営レイヤに食い込んでいくことがVPoEの責務。</p>

<p><b><u>ハートビーツ社 高村様の場合</u></b></p>

<p>CTOは技術方針・ビジョンを掲げる、社内開発・手を動かすところもやられている他、技術的な広報活動もやらているそう。
VPoEは組織マネジメント、具体的には人の配置・裁量・育成・報酬・強化・退職など。あとは事業の運営。そして文化の浸透、醸成。人が増えている中でハートビーツをどういう会社にしていくかといったことを打ち出す。
まだ、CTOがビジョンを描くことに集中できていないかもしれない。CTOが技術方針、先見性を持って技術を選ぶ、VPoEは選んだ技術の執行責任を持ち、価値にするというところに責任を持つべきなので、もう少しVPoEで巻き取れるところがあるかもしれないとのこと。</p>

<p><b><u>グノシー社 加藤様の場合</u></b></p>

<p>グノシー社の技術力というものを無理やり表現すると、技術の幅・深さと、チーム開発力、メンバー層の暑さ・数・リードして教えられるエンジニアを増やすといった2つに分けられるとのこと。その上で、CTOは会社の技術方針に責任を持つ。VPoEはエンジニアの教育。
チームとしてどう成果を上げていくか、といった点は二人三脚で進めていっている。その中で、どのチームも人が足りない中でどうやりくりしていくか、本人の希望とか聴きながら、会社としてどのプロダクトに力を入れていくか、といったことを踏まえながら人の配置などをおこなったり、エンジニアの教育、チームとして強くなるための目標設定なども考えていくのがVPoEの役割。</p>

<h5>VPoEという仕事のやりがいは？</h5>


<p><b><u>メルカリ社　是澤様の場合</u></b></p>

<p>VPoEというよりエンジニア<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%F3%A5%B0%A5%DE">リングマ</a>ネージメントのやりがいと考えているとのこと。CTOの時に感じていたのはマネージメントが楽しいということ。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C9%A5%E9%A5%C3%A5%AB%A1%BC">ドラッカー</a>とか営業と話して<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%C8%A5%E9%A1%BC">コトラー</a>とかの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A1%BC%A5%B1%A5%C6%A5%A3%A5%F3%A5%B0">マーケティング</a>の戦略の本などを読み込んだ時期もあり、それによって可能性を広げた。
色々な法則性を見つけたり、仕組み化したりすることで企業の可能性、エンジニアロールのキャリアの可能性が広げていけるのは面白い。また働き方の仕組みを変えていけるところも面白い。</p>

<p><b><u>ハートビーツ社　高村様の場合</u></b></p>

<p>色々な人とのコミュニケーションをとることでその人の価値観を知れるのが面白い。人として学ぶところがある。
責任範囲が広いため結果に納得感がある。人の問題が起きた時、これまでは結果を受け入れるしかなかったが、今は自分の責任範囲で見れるため、どういう結果であっても納得感が高い。
組織マネージメントをたくさん勉強することで色々な理論を知れ、それを実践して<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%A4%A5%A2%A5%F3%A5%C9%A5%A8%A5%E9%A1%BC">トライアンドエラー</a>をする活動が楽しい。会議のやり方を変える時は論点を変えてみたり。人の成長のステップの研究者はたくさんいてそれらの理論を実践するのは楽しい。</p>

<p><b><u>グノシー社　加藤様の場合</u></b></p>

<p>読む本が変わってきた。人事系の本なども読む。
エンジニアをやっていた時は自分自身をどうしていくかという視点が中心だったが、VPoEとなることで周りのエンジニアの成長をどうしていくかというのが中心になりつつある。あとはVPoEがどう評価されるべきかという点や、複数のVPoEを置いてそれを管理する組織を作っても良いかと思っている。</p>

<h5>わかって欲しいVPoEの大変なところ</h5>


<p><b><u>メルカリ社　是澤様の場合</u></b></p>

<p>心を強く持たなければならない。リク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トは上からも下からも横からも来たり、自分で気づいてしまうこともある。やるやらないの決断を人に促したりもするので、人は頼るが人のアウトプットを鵜呑みにはしない＝レビューしないということ。レビューしないということは成果に対して興味がないということだと考えている。信頼と信用の使い分けは重要。「お金は大事。仕事はなんでもやる。でも魂は売らない。 by リッチウーマン・プアウーマン」
ビジネスは大事で仕事には色々な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%C6%A1%BC%A5%AF%A5%DB%A5%EB%A5%C0%A1%BC">ステークホルダー</a>がいるが、VPoEとして引けない部分は経営者にもはっきり言う。忖度なく、相手の立場を理解しリスペクトした上で自分の伝えたいことを要求する必要があり、それは辛い。成果のためには鬼になること。</p>

<p><b><u>ハートビーツ社　高村様の場合</u></b></p>

<p>人と接することにエネルギーを使う。時にはドライに考えてやるべきことに集中すること。パフォーマンスを出すために割り切ること。
他の部署との関わりが増え、そこの責任者と話すことも増えたが、エンジニアと話すときと同じノリで話すとトラブルになることが多い。乱暴な言葉遣いとか。
バックグラウンドが違うことを認識して接することが大事。</p>

<p><b><u>グノシー社　加藤様の場合</u></b></p>

<p>これからVPoEとしてやっていくので不安について。
フェーズとか規模によってマネジメントの正解も変わって来るのでメンバーには寛容に付き合って欲しい。
マネージャー時代はメンバーの評価とかモチベーションアップに取り組んでいたが、今後はメンバーが思ったような成果が出ないとか人間関係のトラブルなども解決しないといけない思うので心を強く持ちたい。</p>

<h5>お互いに聞きたいこと</h5>


<p><b><u>ハートビーツ社　高村様→メルカリ社 是澤様への質問: メルカリ社規模でも文化の醸成をどうやっているか？</u></b></p>

<p>「Go Bold」、「All for One」、「Be Professional」といった言葉をミッションとしており、社外の方でも知っているような言葉を使っているが、こういったところは広報と連携したり、評価制度に組み込んでいる。組織の中でそこに貢献してくれるエンジニアをどうマジョリ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C6%A5%A3%A1%BC">ティー</a>としていくか、全員を変えるんじゃなくどうマジョリ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C6%A5%A3%A1%BC">ティー</a>にするかを重視している。</p>

<p><b><u>ハートビーツ社　高村様→グノシー社 加藤様への質問: エンジニアの採用をどのような体制でやっているか？</u></b></p>

<p>エンジニア採用のマネージャーを兼務でやっている。既存採用チームのメンバーはエンジニアとして働いてきた訳ではないので、わからない部分を補完したりエンジニア採用の意思決定を担当し、実務を採用チームがやっているような分担になっている。</p>

<p><b><u>グノシー社 加藤様→メルカリ社 是澤様への質問: 全エンジニアとコミュニケーションをとる機会を設けられているか？</u></b></p>

<p>今年4月からEM/PM体制と言う仕組みができて、その中でEMが1on1の機会を設けている。他色々検討している模様。</p>

<ul>
    <li><a href="https://mercan.mercari.com/entry/2018/03/16/113000" target="_brank" rel="noopener noreferrer">「よりよい社会をつくる」に挑戦できる環境は整い始めている｜メルカリCTO×メルペイ取締役対談 - mercan（メルカン）</a></li>
</ul>


<p><b><u>グノシー社 加藤様→ハートビーツ社 高村様への質問: <a class="keyword" href="http://d.hatena.ne.jp/keyword/MBA">MBA</a>取得されているとのことだが、VPoEに活きているか？</u></b></p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/MBA">MBA</a>を学んでVPoEになった。体系的に学べるところ、学校にいく必要があるため強制力があるのは良い。
もともと組織作りに興味があったが<a class="keyword" href="http://d.hatena.ne.jp/keyword/%BF%CD%B4%D6%C0%AD">人間性</a>が伴っていないと当時の上司に指摘され、その中で<a class="keyword" href="http://d.hatena.ne.jp/keyword/MBA">MBA</a>に出会い、自分に向き合う機会になったのはよかった。
新しい知識がつく訳ではなく、色々な本でも学べるところがあるので<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B6%E4%A4%CE%C3%C6%B4%DD">銀の弾丸</a>ではないと感じる。</p>

<p><b><u>グノシー社 加藤様→メルカリ 是澤様、ハートビーツ社 高村様への質問: マネジメントに加えて新しい技術のキャッチアップが必要と思うが、普段できているか？</u></b></p>

<p>メルカリ 是澤様: 平日はまったく時間が取れてない。CTOに教えてもったり社内での技術的な内容で知らないものがあれば調べたりはしている。休日は副業などで勉強している。時間の使い方として技術に関しては休日になってしまう。</p>

<p>ハートビーツ社 高村様: 平日は仕事では12月から全くできてない。休日は学校に通っているので時間が取れていない。たまたま出会ったデザイナーの方と小さいアプリを作ってみたり、他社のエンジニアの方と交流したりすることで補っている。</p>

<p><b><u>メルカリ 是澤様→ハートビーツ社 高村様、グノシー社 加藤様への質問: VPoE自身がVPoEの先のキャリアを定める必要があると思うが、今後やりたいことがあればお聞きしたい</u></b></p>

<p>ハートビーツ社 高村様: 人の価値を上げていくことが面白いと感じているが、今は自分の組織だけをみているが米国では<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A6%A5%F3%A5%C0%A5%EA%A1%BC">バウンダリー</a>スパナー(組織や部門を超えてつなぎ合わせる役割)が注目されている。日本ではそんな肩書きはないが、やっていきたい。</p>

<p>グノシー社 加藤様: ある程度軌道に乗った組織に対して取り組んでいくことになるが、それまでの組織に対しても取り組んでいきたい。また、エンジニア<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%F3%A5%B0%A5%DE">リングマ</a>ネージャーとして取り組んでいくが、そこから会社全体を拡大させていくことに興味をもっている。</p>

<h4>A20: Site Reliability・Developer Productivity・技術戦略の取り組み</h4>


<p>前半は変化に強いインフラを目指して基盤を<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>に移してマイクロサービス化を進めたということ、中盤はそれによって起こる問題、最後にその問題を解決するために取り組んでいること、に関してお話されていた。
<a href="https://www.wantedly.com/" target="_brank" rel="noopener noreferrer">Wantedly Visit</a>は2014年にHerokuから<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>へ移行し、2016年に<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a>-&gt;Core OSへ切り替えとChef-&gt;Dockerfileへの移行を進め、2017年に<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>へ移行開始したそう。
<a href="https://people.wantedly.com/" target="_brank" rel="noopener noreferrer">Wantedly People</a>はすべて <a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a> で稼働。当初<a class="keyword" href="http://d.hatena.ne.jp/keyword/RoR">RoR</a>のみだったがいろいろな言語や<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%EC%A1%BC%A5%E0%A5%EF%A1%BC%A5%AF">フレームワーク</a>を選択・対応できるようにできるだけ<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>の標準のものを使って構築したらしい。<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>を全然使ったことがないので<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>標準のものというのが全然わからない...</p>

<p>この辺りは以下の記事にもまとまっている。</p>

<ul>
    <li><a href="https://www.wantedly.com/companies/wantedly/post_articles/46089" target="_brank" rel="noopener noreferrer">Docker と Kubernetes を使って『変化に強いインフラ』を作る | Wantedly Engineer Blog</a></li>
    <li><a href="https://codezine.jp/article/detail/10357" target="_brank" rel="noopener noreferrer">Kubernetesを使った変化に強いインフラ――Wantedlyのインフラチームが大切にしていること：CodeZine（コードジン）</a></li>
</ul>


<p>MicroServiceの良いところは「技術に特化するものが作れる」、「サービスに合わせたチームができる」ということを挙げられていた。また<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>は親和性が高いらしい。
一方でレギュレーションをしっかり作っておくことが重要とのこと。
インタフェースが明確で、バックアップがとられていて、モニタリングができて、データの一貫性を保てること、壊れないことが大事。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a> の良いところとしてはスケーラビリティがとりやすいこと、kubectlで基本的な運用可能が可能なこと、一部<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B5%A1%BC%A5%C9%A5%D1%A1%BC%A5%C6%A5%A3%A1%BC">サードパーティー</a>性ツールも利用しているらしい。
インフラ定義はすべて<a class="keyword" href="http://d.hatena.ne.jp/keyword/yaml">yaml</a>のManifestファイルを用いており、プロダクトごとに必ずレギュレーションに沿ったManifestファイルがあるので、ちょっと試すときなどはこれをベースとしているらしい
また、エコシステムの恩恵を受けやすいということを挙げられていた。Istiom Envoy... Prometheus, Datadog, New Relic...minikube、Helm Chart(ymlのサンプル利用)、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>サービス(<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>, Azure <a class="keyword" href="http://d.hatena.ne.jp/keyword/GCP">GCP</a>)など、<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>で動くものが増えてきている。Communityが活発なのもプラスで、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Wantedly">Wantedly</a>は今後どうしていけば良いのか、という議論の中で、参考文献など出しやすく説得しやすい。</p>

<h5>MicroServiceにおける課題</h5>


<p>MicroService にすることでいろいろなことが良くなるという話があるが、考えるべきことはたくさんあるとのこと。
この辺りは実際に経験してみないとわからないことで、大きなサービスならではの知見がたくさんあり、マイクロサービスの経験がない自分にとっては参考になる内容が多かった。</p>

<p><b>O(n)で発生/増加する作業</b></p>

<p>一個の変更を複数に反映する必要があるものが多数発生。単に<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>の問題ではなくインフラ業務としてそうなりやすい傾向もあるらしい。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSL%BE%DA%CC%C0%BD%F1">SSL証明書</a>の更新とか、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>化しても来年も動くか不明だったり...ELB+<a class="keyword" href="http://d.hatena.ne.jp/keyword/ACM">ACM</a>で一回で済ませれるように、できるだけO(1)にできないか(1度で済ませられないか)を考えることが必要とのこと。</p>

<p><b>エラー・インシデントのハンドリングの困難さ</b></p>

<p>マイクロサービスが増えてくるとインシデント発生時の切り分けが困難とのこと。
MicroServiceごとにパフォーマンスが異なるためのTimeoutやリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トが詰まるケース、サービスをまたぐ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%D0%A5%C3%A5%B0">デバッグ</a>、リク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トをたたく順番によってエラーになる場合は全部のリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを見ることになったり、スパイクがどこからきているかわからないなど。
モニタリングもできているのか？できていたとしてもわかりづらい。一時的に復旧させるにも一人二人では困難でチームでやる必要が出てくる。</p>

<p><b>管理・取り決め</b></p>

<p>Internal URL/Global URLの管理、MicroService間のふるまい、インタフェースの明確化</p>

<p><b>依存関係の問題</b></p>

<p>どこかの処理が速くなったことで他が耐えられなくなる可能性。
テスト等でもリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを送る先のサービスをケアする必要がある。
不要なデータを問い合わせてくるものもある</p>

<p><b>共通データ</b></p>

<p>サービスの成長に伴ってユーザのデータなど共通で扱うデータが出てくる。どのデータの問い合わせをどこにするのが正しいのか？といったことを考えるきっかけになったそう。</p>

<p><b>知見の共有</b></p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>にissueとか書いてあるが、それだけだと厳しい。<a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a>の作り方とか利用する<a class="keyword" href="http://d.hatena.ne.jp/keyword/OSS">OSS</a>の扱い方とかDockerfileの書き方とか
周知させる場所を検討中。</p>

<p><b>開発環境</b></p>

<p>マイクロサービス単体で開発することはあまりなく、実際にはAサービス、Bサービス、Cサービスがあり、Aからリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トを送ってもらわないとBが動かない...といったケースが多く、A,B,CすべてローカルPCに建てる必要がある。
そうなるとローカルPCがスペック的に厳しくなってくる。一から作るのも大変。
各環境でのデータの扱い方も難しい。リストアの順番とかでデータが壊れたり...
他環境が意図しないブランチで動いていたり</p>

<p><b>ルーティング</b></p>

<p>サービス分離時にパスレベルで変更したいとき、その時都度NginxのConfigを書くのか？インフラレベルでABテストしたいときは？ネットワーク回りでハンドリングしたいときとかどうするのが良いか?
Aサービス→Bサービス→Cサービスというリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トが発生するサービスでのBサービスの開発の難しさ。リク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト発生させずらい。</p>

<h5>どう解決するか</h5>


<p>サイトの信頼性(Site Reliability),エンジニア全体の生産性(Developer Productivity),コアとなる技術基盤(技術戦略)の3つを主軸に、いろいろ取り組んでいるそう。</p>

<p>OKRの明確化、ポストモーテムを実施したり組織として仕組化したり。</p>

<h4>A30: 明日から実践できる！運用自動化に必要な「テスト」にまつわる技術</h4>


<p>テストツールではなく、正確なインフラのテストをできるようにするかといったお話。
開発/検証/QAといった環境を本番環境と同じスペック、構成のもので用意するのは非常に困難(お金面)であるが、運用系の構築<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>やプログラムが本番環境でも正しく動作するのかを事前に確認しておくべきで、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AA%A1%BC%A5%B1%A5%B9%A5%C8%A5%EC%A1%BC%A5%B7%A5%E7%A5%F3">オーケストレーション</a>ツールを使って本番環境と同等構成のテスト環境を構築してテストできるようにしよう、といったお話だった。
テストのために<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AA%A1%BC%A5%B1%A5%B9%A5%C8%A5%EC%A1%BC%A5%B7%A5%E7%A5%F3">オーケストレーション</a>ツール(Terraform, CloudFormation)を使って本番環境を複製できるようにしておこう、というのは<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>をベースにしていればこのアプローチはありだと思うが、オンプレミスだとかなり厳しい...</p>

<p>ポイントとして、以下のようなことを挙げられていた。</p>

<ol>
    <li>仮想ネットワークの活用、つまり本番環境と同じセグメントを持ったネットワーク環境を作ること、ホスト名に加えて、<a class="keyword" href="http://d.hatena.ne.jp/keyword/IP%A5%A2%A5%C9%A5%EC%A5%B9">IPアドレス</a>必要であれば合わせるなど可能な限り同じネットワーク構成として合わせる、ただし<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B0%A5%ED%A1%BC%A5%D0%A5%EBIP">グローバルIP</a>など外と接する部分は異なるものとして、内部構成を一致させること。</li>
    <li>サーバのセットアップ手順を含める。Ansibleでも<a class="keyword" href="http://d.hatena.ne.jp/keyword/bash">bash</a>でも良いので。用心のために<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>サーバやCIサーバ、ビルドサーバなども含めておく。</li>
    <li>マシンイメージを一式作成しておく。packerなどで一式作成しておき、<a class="keyword" href="http://d.hatena.ne.jp/keyword/yum">yum</a>などのパッケージ管理システムでは古いpackageがだんだんinstallできなくなったりするので、自前でパッケージ管理したりイメージで保存しておいた方が良いとのこと。</li>
    <li>上記を1式<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>に含めて環境をビルドできるようにしておく</li>
</ol>


<p>あと、セッションの前半に、環境構築ツールとしてはAnsibleなどがあるなかで、手順的に<a class="keyword" href="http://d.hatena.ne.jp/keyword/bash">bash</a>を実行するツールとして bashsteps というものも紹介されていた。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/axsh/bashsteps">GitHub - axsh/bashsteps: Framework for breaking down bash scripts into meaningful steps.</a></li>
<a target="_brank" rel="noopener noreferrer" href="https://github.com/axsh/bashsteps">
</a><a target="_brank" rel="noopener noreferrer" href="https://github.com/axsh/bashsteps"></a>
</ul>


<h4>A40: 最高のITエンジニアリングを支える守りと攻めの「設計技術」と「SRE」</h4>


<p>SREとして自社でどういう取り組みをしてきたのか、0 -&gt; 1 のフェーズのところでどうしてきたかというところが中心としたお話だった。
資料にもある通り、0からスタートした時の事例や話というのはなかなか聞く機会がないように思う。でもこの0-&gt;1へ変わるための取り組みが一番大事であり一番大変なところでもあると思う。</p>

<p>まず目標というか方向性が明確になっているのが大事で、これがないと何をするのが正しいのかよくわからなくなって目の前の業務をやることに意識が向いてしまうように思う。
「ぼくの考えた最高のエンジニアリング 最大限ユーザーへの価値提供に集中できる状態を維持し続ける」という目標に対して、「仕組・エンジニアリングで問題解決しようとする」「人間が頑張って解決しようとしない」という原則のもと、「ユーザーへの価値提供に集中できない状態 減らすための守りの設計」、「ユーザーへの価値提供に集中できる状態 増やすための攻めの設計」という2つの明確な切り口からの取り組みはわかりやすくて良い。</p>

<p>初期フェーズでの、エンジニアリングに注力するという意識、片っ端から勉強、他社のつらみを聞く、(わからないなりに)理論を学ぶ、という取り組み、実際にやってよかったこととしての、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C9%A5%AD%A5%E5%A5%E1%A5%F3%A5%C6%A1%BC%A5%B7%A5%E7%A5%F3">ドキュメンテーション</a>、スペックアップ(簡単に解決できるものをつぶす)、ルーチン・<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%C6%A5%AD%A5%B9%A5%C8%A5%B9%A5%A4%A5%C3%A5%C1">コンテキストスイッチ</a>を減らすため適用範囲の見極めに注意したうえでの使い捨て前提での自動化用コードを書くというのも、何から取り組めば良いかわからない状態からすれば道筋の1つになりそうで参考になった。</p>

<p>紹介されてた以下の書籍も読んでいきたい。</p>

<ul>
    <li>
<a target="_blank" href="https://www.amazon.co.jp/gp/product/4320025636/ref=as_li_tl?ie=UTF8&amp;camp=247&amp;creative=1211&amp;creativeASIN=4320025636&amp;linkCode=as2&amp;tag=dshimizu05-22&amp;linkId=2807b49868995bb4bdd18826bb59af28" rel="noopener noreferrer">スーパーエンジニアへの道―技術リーダーシップの人間学</a><img src="//ir-jp.amazon-adsystem.com/e/ir?t=dshimizu05-22&amp;l=am2&amp;o=9&amp;a=4320025636" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;">
</li>
    <li>
<a target="_blank" href="https://www.amazon.co.jp/gp/product/4320023684/ref=as_li_tl?ie=UTF8&amp;camp=247&amp;creative=1211&amp;creativeASIN=4320023684&amp;linkCode=as2&amp;tag=dshimizu05-22&amp;linkId=9165b25f5ddfa081c601b59bd36aafdb" rel="noopener noreferrer">ライト、ついてますか―問題発見の人間学</a><img src="//ir-jp.amazon-adsystem.com/e/ir?t=dshimizu05-22&amp;l=am2&amp;o=9&amp;a=4320023684" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;">
</li>
    <li>
<a target="_blank" href="https://www.amazon.co.jp/gp/product/4822281116/ref=as_li_tl?ie=UTF8&amp;camp=247&amp;creative=1211&amp;creativeASIN=4822281116&amp;linkCode=as2&amp;tag=dshimizu05-22&amp;linkId=3419ce67728fb7ed6258bbc9643aa161" rel="noopener noreferrer">ゆとりの法則 － 誰も書かなかったプロジェクト管理の誤解</a><img src="//ir-jp.amazon-adsystem.com/e/ir?t=dshimizu05-22&amp;l=am2&amp;o=9&amp;a=4822281116" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;">
</li>
    <li>
<a target="_blank" href="https://www.amazon.co.jp/gp/product/4799320238/ref=as_li_tl?ie=UTF8&amp;camp=247&amp;creative=1211&amp;creativeASIN=4799320238&amp;linkCode=as2&amp;tag=dshimizu05-22&amp;linkId=c568877d283e57e5b315d8654dca08e4" rel="noopener noreferrer">失敗の科学 失敗から学習する組織、学習できない組織</a><img src="//ir-jp.amazon-adsystem.com/e/ir?t=dshimizu05-22&amp;l=am2&amp;o=9&amp;a=4799320238" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;">
</li>
    <li>
<a target="_blank" href="https://www.amazon.co.jp/gp/product/3950307842/ref=as_li_tl?ie=UTF8&amp;camp=247&amp;creative=1211&amp;creativeASIN=3950307842&amp;linkCode=as2&amp;tag=dshimizu05-22&amp;linkId=cfccf68a389aa5500bf727476041c831" rel="noopener noreferrer">SQLパフォーマンス詳解</a><img src="//ir-jp.amazon-adsystem.com/e/ir?t=dshimizu05-22&amp;l=am2&amp;o=9&amp;a=3950307842" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;">
</li>
    <li>
<a target="_blank" href="https://www.amazon.co.jp/gp/product/4873117917/ref=as_li_tl?ie=UTF8&amp;camp=247&amp;creative=1211&amp;creativeASIN=4873117917&amp;linkCode=as2&amp;tag=dshimizu05-22&amp;linkId=cff602256152ae0d3e40eef5c713d69d" rel="noopener noreferrer">SRE サイトリライアビリティエンジニアリング ―Googleの信頼性を支えるエンジニアリングチーム</a><img src="//ir-jp.amazon-adsystem.com/e/ir?t=dshimizu05-22&amp;l=am2&amp;o=9&amp;a=4873117917" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;">
</li>
</ul>


<ul>
    <li><a target="_blank" rel="noopener noreferrer" href="https://www.facebook.com/notes/facebook-engineering/making-facebook-self-healing/10150275248698920/">Making Facebook Self-Healing</a></li>
</ul>


<pre> &lt;li&gt;&lt;a target="_blank" rel="noopener noreferrer" href="http://hatenacorp.jp/recruit/operation_engineer"&gt;10年続くサービスを、インフラ技術で支える――Webオペレーションエンジニア座談会 - 株式会社はてな&lt;/a&gt;&lt;/li&gt;
&lt;li&gt;&lt;a target="_blank" rel="noopener noreferrer" href="https://ihara2525.tumblr.com/post/17029509298/%E4%B8%80%E8%A1%8C%E3%81%AE%E3%83%AD%E3%82%B0%E3%81%AE%E5%90%91%E3%81%93%E3%81%86%E3%81%AB%E3%81%AF%E4%B8%80%E4%BA%BA%E3%81%AE%E3%83%A6%E3%83%BC%E3%82%B6%E3%81%8C%E3%81%84%E3%82%8B"&gt;一行のログの向こうには、一人のユーザがいる - ihara2525's blog&lt;/a&gt;ドキュメンテーション、モニタリング、自動化やInfrastractuer as a Codeといったところは自分の中でも課題となっているがなかなかうまく仕組化できていないところだったが、それがなぜできないのか何となくわかった。
 </pre>

<p>最初は泥臭く粘り強くやっていくしかないということ、そして結局は技術力だけでは組織や業務を変えれなくて、自分たちにとっての問題とは何か、本来やるべきことは何かを明確にして取り組むこと、それを関係者と意識合わせすることが重要なんだなというのを改めて感じた。</p>

<h4>A50: <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DF%A5%E9%A5%F3">ミラン</a>ティスが1万台のサーバーを監視・運用するためにInfrastructure as Codeを利用している話</h4>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DF%A5%E9%A5%F3">ミラン</a>ティスのOpsCareというサービスは<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%A4%A5%D9%A1%BC%A5%C8%A5%AF%A5%E9%A5%A6%A5%C9">プライベートクラウド</a>を構築するサービスらしく、現在物理サーバが1万台以上(仮想サーバはそれ以上)で構成されており、また多数の<a class="keyword" href="http://d.hatena.ne.jp/keyword/OSS">OSS</a>も使っており、厳しい<a class="keyword" href="http://d.hatena.ne.jp/keyword/SLA">SLA</a>もある中で、それを少人数(2桁らしい)で運用するためのInfrastructure as a Codeについてのお話だった。会社としてはOpenStackや<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a>を得意としているとのこと。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.mirantis.co.jp/services/operate/">OpenStack Managed Services | Managed Cloud | Mirantis</a></li>
</ul>


<p>構成管理ツールとしては Salt を使っている。コードの管理自体はgitで行い、変更レビューをGerritで、このレビューが通ったものをJenkinsで自動的にデプロイしているとのこと。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/saltstack/salt">OpenStack Managed Services | Managed Cloud | Mirantis</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.gerritcodereview.com/">Gerrit Code Review | Gerrit Code Review</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://jenkins.io/">Jenkins</a></li>
</ul>


<p>監視にはPrometheusを使っているが、大量の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>や細かい設定ファイルを扱っていると監視項目の定義忘れや設定漏れ、エージェントへの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>の配布漏れが出てきてしまうので、コード化しているインフラ設定群から何の設定が入っているかを読み解くSaltのコードからPrometheus設定ファイルを自動生成しているとのこと。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://prometheus.io/">Prometheus - Monitoring system &amp; time series database</a></li>
</ul>


<p>最初から良かったわけではなく、ここに至るまでにいろいろなことをやってきたとのことだった。</p>

<p>Saltは使ったことがないが、対象サーバの台数が増えてくるとAnsibleでは手に余りそう、というのはよく聞くので(実際にそんな環境に遭遇したことはないが)、Saltのほうがよさそう(適当)。ツールは置いておいても、物理サーバ1万台規模でこの仕組みを作ってうまく運用を回されているところは素晴らしかった。</p>

<h4>A60: ソフトウェアで構築する成長し続けるインフラスト<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E9%A5%AF">ラク</a>チャとメルカリの挑戦</h4>


<p>あとでかく(多分)</p>

<h3>参考</h3>


<ul>
    <li><a href="https://togetter.com/li/1251525" target="_brank" rel="noopener noreferrer">July Tech Festa 2018 #JTF2018 ツイートまとめ - Togetter</a></li>
</ul>


<p></p>
</body>
