---
title: "今更ながら「Amazon Aurora: Design Considerations for High Throughput Cloud-Native Relational Databases」 を読んで調べたことのメモ"
date: 2022-01-29
slug: "今更ながら「Amazon Aurora: Design Considerations for High Throughput Cloud-Native Relational Databases」 を読んで調べたことのメモ"
category: blog
tags: [Tech,AWS,Amazon Aurora]
---
<p>Auroraの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3">アーキテクチャ</a>とその設計上の考慮事項について書かれている論文が 2017 年に公開されている。有名なのでその時読まれた方も多いと思う。私はその時 Aurora を触ってなくて存在を知っていただけだったので今更ながら目を通してみた。</p>

<ul>
<li><a href="https://www.amazon.science/publications/amazon-aurora-design-considerations-for-high-throughput-cloud-native-relational-databases">Amazon Aurora: Design considerations for high throughput cloud-native relational databases - Amazon Science</a></li>
</ul>


<p>自分には結構難しくて理解が合っているかどうかはわからないし2022年の今とは変わっているところもあるのかもしれないけど一応読んで色々調べたりしたことを残しておく。</p>

<h2>THE LOG IS THE DATABASE な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3">アーキテクチャ</a></h2>

<p>論文にTHE LOG IS THE DATABASEと書かれている。多くの機能が<a class="keyword" href="http://d.hatena.ne.jp/keyword/Redo">Redo</a>ログを要としている気がする。
Aurora の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3">アーキテクチャ</a>は <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> の基盤に最適化されているような形になっているように思われる。</p>

<p>前提として、もう当たり前のことではあるが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> は1つのリージョンに、複数の「AZ(Availability Zone)」という、物理的に異なるとされる論理的に異なる単位を持っている。物理的に完全に別な場所にあるのか、内部的に何らかで分離されているのかはわからないが、1つの AZ レベルの障害が、他のAZには影響を及ばさないような形にはなっていると思われる。</p>

<p>Auroraで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーを組む時、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>はプライマリとレプリカをAZを跨いで配置することができる。というか可用性を考えると大抵はこういう配置にすることになると思う。</p>

<p>通常のRDSでも複数の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>をAZを跨いで起動することになると思うが、この時、通常のRDSであれば<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%D7%A5%EA%A5%B1%A1%BC%A5%B7%A5%E7%A5%F3">レプリケーション</a>時のバイナリログ等の多くのデータがネットワーク経由でプライマリからレプリカへ送られるのに対して、Auroraの場合は<a class="keyword" href="http://d.hatena.ne.jp/keyword/Redo">Redo</a>ログのみが送られる。(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Redo">Redo</a>ログは「ページへの行の追加や更新、分割やマージなどの<a class="keyword" href="http://d.hatena.ne.jp/keyword/MTR">MTR</a>(ミニ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%F3%A5%B6%A5%AF%A5%B7%A5%E7%A5%F3">トランザクション</a>)レベルの細かい変更ログで、これを再実行することで同じ操作を再現できるもの」といった感じで理解している)
これによってAZを跨がる<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>間のデータ同期の際のネットワーク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DC%A5%C8%A5%EB%A5%CD%A5%C3%A5%AF">ボトルネック</a>を削減している。</p>

<p>また、CPU・メモリといったコンピューティングリソースと、ストレージ基盤を分離した構成としている。これ自体はオンプレミスであれば共有ストレージを用いた構成と似たような感じかと思っている。
Auroraのストレージ基盤がどうなっているのかはわからないが、データを書き込む際は Protection Groups と呼ばれる 10 GB の論理的なブロック単位で、AZをまたいで6つのストレージ基盤用のノードに配置しているらしい。</p>

<p>ストレージ基盤に送られるのも<a class="keyword" href="http://d.hatena.ne.jp/keyword/Redo">Redo</a>ログのみのようで、これも6つのノードに保存される模様(論文には書かれてないが実際には6つのコピーは全て同一ではなく、3 つはフルセグメント(データページとロ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B0%A5%EC%A5%B3">グレコ</a>ード)、残りにの3つはテールセグメント(ロ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B0%A5%EC%A5%B3">グレコ</a>ードのみ)らしい)。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Redo">Redo</a>ログには様々な種類のLSN(Log Sequence Number)という番号が振られており、これらを用いて、Auroraの各ストレージノードがどこまで変更を反映できているかを把握する。</p>

<p>書き込み時はこの6つのノードにリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トし、内4つまで書き込みができれば、書き込み完了とみなされる。これは AZ 全体の障害に加えてさらに1ノードの障害でも耐えうるものにするためであるとのこと。
読み込み時は6つのノードにリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トし、3つのノードから結果が返されればOKとなる。ただし、読み込みクォーラムを実際に使用するのは、プライマリDB<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>のキャッシュの喪失・再起動、レプリカのプライマリへの昇格などローカルの状態再構築、破損したセグメントの再構築、クォーラムの修復といった場合で、それ以外は予め管理している必要なデータバージョンをもつノードから読み取る。</p>

<p>データの読み込み時、バッファキャッシュを見に行き、キャッシュに存在しない場合はストレージIOリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トが発生する。
バッファキャッシュがいっぱいの場合、通常であれば持っているページがダーティページ(ストレージに書き込むべき内容)であった場合、捨てる前にストレージに書き込みされるが、Auroraだとストレージに書き込まずに捨てる。この時ページLSNというものがVDL以上の場合、ページをキャッシュから削除することになるらしい。こうなると、ストレージ側が古い情報のままになるが、すぐに<a class="keyword" href="http://d.hatena.ne.jp/keyword/Redo">Redo</a>ログから最新の情報に更新し、ストレージ側の情報を<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>側へ反映するらしい。
Protection Group ごとに最小読み取り Protection Group Min Read Point LSN (PGMRPL)をいつでも計算でき、すべてのノード間でゴシップを実行し、保護グループの Protection Group Min Read Point LSN を「最低水準点」として設定される。</p>

<p>クラッ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%E5%A5%EA%A5%AB">シュリカ</a>バリ時も<a class="keyword" href="http://d.hatena.ne.jp/keyword/Redo">Redo</a>ログを有効に活用するようになっている。<a class="keyword" href="http://d.hatena.ne.jp/keyword/REDO">REDO</a>ログアプリケーターがデータベースから切り離され、ストレージノードで並行して、常にバックグラウンドで動作するため、処理コストを少なく実現できるらしい。読み込みクォーラムはここで使われる。
データベースは、すべてのProtection Group に対して読み取りクォーラムを確立すると、VDL(Volume Durable LSN: <a class="keyword" href="http://d.hatena.ne.jp/keyword/VCL">VCL</a>より小さいが最も大きい<a class="keyword" href="http://d.hatena.ne.jp/keyword/CPL">CPL</a>)を再計算し、新しいVDL以降のすべてのロ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B0%A5%EC%A5%B3">グレコ</a>ードを削除する切り捨て範囲を生成して不要なVDLを計算する。</p>

<h2>まとめ</h2>

<p>Aurora の設計に関する論文を読んで調べたことをまとめた。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の基盤に合わせて、コンピューティングノードとストレージノードを分離し、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Redo">Redo</a>ログをベースにしてのプライマリ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>・レプリカ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>間や、各<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>とストレージノード間のネットワーク通信や、ストレージIOなどの一般的に遅くなる部分を減らし、ストレージノード等のCPUリソースをうまく活用できる感じの構成になっていると思われた。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Redo">Redo</a>ログでは、様々なLSNを活用し、Write/Read/Commit/Recoveryなどを高速かつ安全に行えるような仕組みを実現しているようだが、各LSNの扱いをまだあんまり理解できていないので、また調べてわかったら更新したい。</p>

<h2>参考</h2>

<ul>
<li><a href="https://www.amazon.science/publications/amazon-aurora-design-considerations-for-high-throughput-cloud-native-relational-databases">Amazon Aurora: Design considerations for high throughput cloud-native relational databases - Amazon Science</a></li>
<li><a href="https://assets.amazon.science/dc/2b/4ef2b89649f9a393d37d3e042f4e/amazon-aurora-design-considerations-for-high-throughput-cloud-native-relational-databases.pdf">https://assets.amazon.science/dc/2b/4ef2b89649f9a393d37d3e042f4e/amazon-aurora-design-considerations-for-high-throughput-cloud-native-relational-databases.pdf</a></li>
<li><a href="https://qiita.com/kumagi/items/67f9ac0fb4e6f70c056d">Amazon Aurora&#x306E;&#x5148;&#x9032;&#x6027;&#x3092;&#x8AB0;&#x3082;&#x89E3;&#x8AAC;&#x3057;&#x3066;&#x304F;&#x308C;&#x306A;&#x3044;&#x304B;&#x3089;&#x89E3;&#x8AAC;&#x3059;&#x308B; - Qiita</a></li>
<li><a href="https://speakerdeck.com/maroon1st/cloud-native-rdbms-aurora-jin-hua-hazhi-marazu-1?slide=44">Cloud Native RDBMS Aurora &#x301C;&#x9032;&#x5316;&#x306F;&#x6B62;&#x307E;&#x3089;&#x305A;&#x301C; - Speaker Deck</a></li>
<li><a href="https://nryotaro.dev/posts/amazon_aurora/">&#x8AD6;&#x6587;&#x30E1;&#x30E2; Amazon Aurora: Design Considerations for High Throughput Cloud-Native Relational Databases</a></li>
<li><a href="https://mrasu.hatenablog.jp/entry/2019/04/29/143206">Aurora - &#x30AF;&#x30E9;&#x30A6;&#x30C9;&#x6642;&#x4EE3;&#x306E;DB&#x30A2;&#x30FC;&#x30AD;&#x30C6;&#x30AF;&#x30C1;&#x30E3; - &#x767A;&#x660E;&#x306E;&#x305F;&#x3081;&#x306E;&#x518D;&#x767A;&#x660E;</a></li>
<li><a href="https://spring-mt.hatenablog.com/entry/2021/03/15/164956">Amazon Aurora: Design Considerations for High Throughput Cloud-Native Relational Databases&#x3092;&#x8AAD;&#x3080;(&#x305D;&#x306E;4 THE LOG MARCHES FORWARD) - CubicLouve</a></li>
<li><a href="https://yoshidashingo.hatenablog.com/entry/2014/11/26/032121">Aurora&#x306E;&#x30B9;&#x30C8;&#x30EC;&#x30FC;&#x30B8;&#x306E;Quorum&#x539F;&#x7406; - yoshidashingo</a></li>
<li><a href="https://aws.amazon.com/jp/blogs/news/amazon-aurora-under-the-hood-quorum-and-correlated-failure/">Amazon Aurora under the hood: &#x30AF;&#x30AA;&#x30FC;&#x30E9;&#x30E0;&#x3068;&#x969C;&#x5BB3; | Amazon Web Services &#x30D6;&#x30ED;&#x30B0;</a></li>
<li><a href="https://aws.amazon.com/jp/blogs/news/amazon-aurora-under-the-hood-quorum-reads-and-mutating-state/">Amazon Aurora Under the Hood: &#x30AF;&#x30AA;&#x30FC;&#x30E9;&#x30E0;&#x306E;&#x8AAD;&#x307F;&#x53D6;&#x308A;&#x3068;&#x72B6;&#x614B;&#x306E;&#x9077;&#x79FB; | Amazon Web Services &#x30D6;&#x30ED;&#x30B0;</a></li>
<li><a href="https://aws.amazon.com/jp/blogs/news/amazon-aurora-under-the-hood-quorum-and-correlated-failure/">Amazon Aurora under the hood: &#x30AF;&#x30AA;&#x30FC;&#x30E9;&#x30E0;&#x3068;&#x969C;&#x5BB3; | Amazon Web Services &#x30D6;&#x30ED;&#x30B0;</a></li>
<li><a href="https://aws.amazon.com/jp/blogs/news/amazon-aurora-under-the-hood-reducing-costs-using-quorum-sets/">Amazon Aurora Under the Hood: &#x30AF;&#x30AA;&#x30FC;&#x30E9;&#x30E0;&#x30BB;&#x30C3;&#x30C8;&#x3092;&#x4F7F;&#x3063;&#x305F;&#x30B3;&#x30B9;&#x30C8;&#x524A;&#x6E1B; | Amazon Web Services &#x30D6;&#x30ED;&#x30B0;</a></li>
<li><a href="https://tech.nri-net.com/entry/comparison_of_aws_quorum_model_databases">&#x30AF;&#x30A9;&#x30FC;&#x30E9;&#x30E0;&#x30E2;&#x30C7;&#x30EB;&#x3092;&#x4F7F;&#x7528;&#x3057;&#x305F;AWS&#x30C7;&#x30FC;&#x30BF;&#x30D9;&#x30FC;&#x30B9;&#x30B5;&#x30FC;&#x30D3;&#x30B9;&#x306E;&#x9055;&#x3044;&#x3001;&#x5171;&#x901A;&#x70B9;&#x306E;&#x6BD4;&#x8F03; &#xFF0D;Amazon Aurora&#x3001;Amazon DocumentDB&#x3001;Amazon Neptune&#x306E;&#x6BD4;&#x8F03;&#x8868; &#xFF0D; - NRI&#x30CD;&#x30C3;&#x30C8;&#x30B3;&#x30E0;Blog</a></li>
<li><a href="https://pages.awscloud.com/rs/112-TZM-766/images/01_Amazon%20Aurora%20%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%81%E3%83%A3%E6%A6%82%E8%A6%81.pdf">https://pages.awscloud.com/rs/112-TZM-766/images/01_Amazon%20Aurora%20%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%81%E3%83%A3%E6%A6%82%E8%A6%81.pdf</a></li>
<li><a href="https://d1.awsstatic.com/events/jp/2018/summit/tokyo/aws/06.pdf">https://d1.awsstatic.com/events/jp/2018/summit/tokyo/aws/06.pdf</a></li>
<li><a href="https://pages.awscloud.com/rs/112-TZM-766/images/B3-06.pdf">https://pages.awscloud.com/rs/112-TZM-766/images/B3-06.pdf</a></li>
<li><a href="https://github.com/lzl7/paper-reading/blob/master/database/Amazon-Aurora-Design-Considerations-for-High-Throughput-Cloud-Native-Relational-Databases.md">paper-reading/Amazon-Aurora-Design-Considerations-for-High-Throughput-Cloud-Native-Relational-Databases.md at master &middot; lzl7/paper-reading &middot; GitHub</a></li>
</ul>


