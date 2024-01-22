---
layout: post
Categories:  ["tech"]
Description:  " Terraform meetup tokyo #2に行ってきた。     Terraform meetup tokyo#2 - connpass  "
Tags: ["meetup", "tech", "イベント"]
date: "2019-10-06T19:26:00+09:00"
title: "Terraform meetup tokyo #2に行ってきた"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p>Terraform meetup tokyo #2に行ってきた。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://terraform-jp.connpass.com/event/142041/">Terraform meetup tokyo#2 - connpass</a></li>
</ul>

</body>

<!-- more -->

<body>
<p>会場は大崎の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AA%A5%A4%A5%B7%A5%C3%A5%AF%A5%B9">オイシックス</a>・ラ・大地株式会社様で、フードスポンサーであるHashcorp様からの飲み物や軽食のほかに各テーブルにシャインマスカットとVegeel for Womanという野菜ジュースが配られた。ありがとうございます。</p>

<p>当日はLT7本に加えて、間でワールドカフェが行われた。
ワールドカフェは討論のやり方の一つで、数人からなるいくつかのグループに別れて特定のテーマについて議論するもの、らしい</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="http://world-cafe.net/about/">ワールド・カフェとは？ | ワールド・カフェ・ネット</a></li>
</ul>


<p>当日は、参加者が6名ずつのグループに別れてTerraformについて自由に対話する場が設けられた。
懇親会の場ではない中で他の参加者の方々と話する場が設けられたのはよかった。</p>

<h3>前半セッション</h3>




<h4>Sales Director 奥るみ様</h4>


<p>Terraform CloudやTerraformの認定試験のご紹介だった。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" herf="https://www.hashicorp.com/certification/">HashiCorp Product Certification (beta)</a></li>
  <li><a target="_brank" rel="noopener noreferrer" herf="https://www.hashicorp.com/products/terraform/">HashiCorp Terraform - Provision and Manage any Infrastructure</a></li>
</ul>


<p>TerraformはOpenSource版とEnterprise版があるが、加えてTerraform Cloudがリリースされたこと、Terraform Cloudには無料利用枠がありそこで使える機能等についてご説明いただいた。</p>

<h4>LT1 @kanga333 様 個々のアプリの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>でTerraformを管理している話</h4>


<p></p>
<p><script async class="speakerdeck-embed" data-id="a9a0d29e21a64dc8a4a550a0367a7a46" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script></p>

<p>UZOUという広告配信プラットフォームのシステムにおけるTerraformのお話
よくある(と思われる)Terraform用の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>はインフラチーム/SREチームが管理する terraform 専用の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>になっている事が多いと思われるが、Speee様のUZOUではアプリケーションコードの中にTerraform<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを切ってTerraformコードを置いているらしい。
メリットとしては、アプリケーションが利用するイン<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%E9%A5%EA">フラリ</a>ソースがわかりやすいとかアプリとインフラの変更レビューを一緒にできるとか。
デメリットとしては、インフラ構成全体の把握に時間がかかるとか書き方にムラが出やすいとかがあるらしい。「インフラ構成全体の把握に時間がかかる」というのは各サービスが1つの<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>アカウント内にあるということなんだろうか。
Speee様のUZONのチームにはインフラチームがないとのことなので、開発チームがアプリもインフラも管理している、ということかと思うが、スキルセット的には理想的だと思った。</p>

<p></p>
<h4>LT2 @_mpon 様 <a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a>がある外部サービスはTerraformで管理できますよ</h4>
<p><script async class="speakerdeck-embed" data-id="7bd0ae3815d84def8bd22e72ab3db4e2" data-ratio="1.33333333333333" src="//speakerdeck.com/assets/embed.js"></script>
プロバイダーのない外部サービスは画面ポチポチするしかない、、そんなことはないです、カスタムプロパイダーを自作して解決しましょう！というお話でした。Twillioのプロパイダーを作成された際の知見を話されました。</p>

<p>Providerの存在しないサービスをTerraform管理するお話でした。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a>の公開されているサービス(本セッションではTwilio)のTerraformのProviderを自作して管理できるよ、というお話だった。</p>

<p></p>
<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/mpon/terraform-provider-twilio">mpon/terraform-provider-twilio</a></li>
</ul>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>や<a class="keyword" href="http://d.hatena.ne.jp/keyword/GCP">GCP</a>などでインフラを構築する場合、Terraformで構成管理を行うことができるが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>や<a class="keyword" href="http://d.hatena.ne.jp/keyword/GCP">GCP</a>以外の外部サービスを利用するケースも多い。
それらのうち、Providerが存在しないサービスについてはTerraformで一括管理できない。別で画面ポチポチ等...</p>

<p>外部<a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a>が公開されているサービスならそれを利用して自分でカスタムProviderを作成でき、TwilioのProviderを作成されたとのこと。
実際に作った時の方法(ファイルの構成や参考にしたもの)についてお話されていた。</p>

<p>Providerを自作したい、という方は下記の記事が参考になるらしい。</p>

<p></p>
<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="http://febc-yamamoto.hatenablog.jp/entry/terraform-custom-provider-01">Terraform Provider実装 入門(1): Custom Providerの基礎  - febc技術メモ</a></li>
</ul>

<p>本題と関係ないところで、TerraformのProviderってこんなにたくさん種類があったんですね、と改めて知った。</p>

<p></p>
<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/docs/providers/index.html">Providers - Terraform by HashiCorp</a></li>
</ul>

<p></p>
<h4>LT3 @int_tt 様 Terraformerみて感動した話</h4>
<p><script async class="speakerdeck-embed" data-id="d2a62e338db24af9bd25c9759439eae7" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script></p>

<p>Terraformerの概要、簡単な使い方、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3">アーキテクチャ</a>の話、最後に、機能追加でPullReqを出されたときの知見のお話だった。</p>

<p></p>
<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/GoogleCloudPlatform/terraformer/pull/173">add support google_logging_metric by int-tt · Pull Request #173 · GoogleCloudPlatform/terraformer</a></li>
</ul>

<p>Terraformerの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3">アーキテクチャ</a>は大きく以下の3つからなっているらしい。</p>

<p></p>
<ol>
  <li>前処理</li>
    コマンドのParseを行う
    Providerごとの初期設定を行う
    対応しているリソースかのチェック、対応していればリソースの一覧を取得してリソースとIDをセットする
    Providerとの通信の準備を行う。
    <a href="http://github.com/GoogleCloudPlatform/terraformer/blob/master/cmd/import.go">http://github.com/GoogleCloudPlatform/terraformer/blob/master/cmd/import.go</a> のfunc importの前半
  <li>Providerとの通信</li>
    指定したリソースのRefresh(Read)から結果を受け取る
    rpcかgrpcで通信する
    <a href="http://github.com/GoogleCloudPlatform/terraformer/blob/master/cmd/import.go">http://github.com/GoogleCloudPlatform/terraformer/blob/master/cmd/import.go</a> のfunc importの後半
  <li>取得結果のParse</li>
    取得した結果をParseしてtfファイル、tfstateファイルに出力する
    <a href="https://github.com/GoogleCloudPlatform/terraformer/blob/master/cmd/import.go#L139">https://github.com/GoogleCloudPlatform/terraformer/blob/master/cmd/import.go#L139</a> func ImportFromPlan
</ol>

<p></p>
<h4>LT4 @<a class="keyword" href="http://d.hatena.ne.jp/keyword/sion">sion</a>_cojp 様 <a class="keyword" href="http://d.hatena.ne.jp/keyword/FOLIO">FOLIO</a>のterraform運用tips</h4>
<p><script async class="speakerdeck-embed" data-id="0000d38385934cd3b853001a9621f60d" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script></p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/FOLIO">FOLIO</a>様でのTerraformを運用するにあたってのTipsのお話だった。
何故その値にしたか等をできる限りコメントとして書くとか、resource の定義をわかりやすくするとか、後からチームに入ってきた人でもわかりやすくする状態を把握しやすくすることを心がけておられるようです。
また、Terraformはいろんな<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のリソースを細かく定義するため全体像が見えにくくなることの対策として、まずはステージング環境で動かす状態にしてその後に全体設計やレビューをおこなっているとのこと。
ちなみにTerraformの実行/CIにはAtlantisを使われているらしい。</p>

<p></p>
<h3>後半セッション</h3>

<p></p>
<h4>LT5 @HikaruMiyazawa 様 Terraform WorkSpace機能を活用してきたノウハウを一挙公開</h4>
<p><iframe src="//www.slideshare.net/slideshow/embed_code/key/ra7FkMvZuYRiQb" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe></p>

<p></p>
<h4>LT6 @ShandyGaffLover 様 TerraformとAzure Pipelinesを用いたプロビジョニングの自動化</h4>
<ul>
<li><a href="https://onedrive.live.com/view.aspx?resid=67CBB30E4B1673FD!196&amp;ithint=file%2cpptx&amp;authkey=!AE1dVPBd7c6mcbQ" target="_blank" rel="noopener noreferrer">発表資料</a></li>
</ul>

<p></p>
<h4>LT7 @kangaechu 様 terraform-provider-<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a>のコントリビューターになろう</h4>
<ul>
<li><a href="https://docs.google.com/presentation/d/1IB7DB2VoJMJ1aFs7kU4tkunNOOmcUE2ruBExdeWk7HY/edit?usp=sharing" target="_blank" rel="noopener noreferrer">発表資料</a></li>
</ul>

<p></p>
<h4>事前アンケート結果</h4>

<p><iframe src="//www.slideshare.net/slideshow/embed_code/key/jkRxZLmwcFi0Av" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> </p>
<div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/ssuser1f3c12/questionnaireresultsterraformmeetuptokyo2" title="事前アンケート集計 Terraform meetup tokyo#2" target="_blank" rel="noopener noreferrer">事前アンケート集計 Terraform meetup tokyo#2</a> </strong> from <strong><a href="https://www.slideshare.net/ssuser1f3c12" target="_blank" rel="noopener noreferrer">Yukiya Hayashi</a></strong> </div>

<p></p>
<h3>参考</h3>

<p></p>
<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://togetter.com/li/1412022">Terraform meetup tokyo#2 #terraformjp - Togetter</a></li>
</ul>
</body>
