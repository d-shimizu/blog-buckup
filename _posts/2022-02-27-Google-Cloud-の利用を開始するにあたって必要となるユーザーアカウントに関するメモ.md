---
title: "Google Cloud の利用を開始するにあたって必要となるユーザーアカウントに関するメモ"
date: 2022-02-27
slug: "Google Cloud の利用を開始するにあたって必要となるユーザーアカウントに関するメモ"
category: blog
tags: [Google Cloud]
---
<h2>はじめに</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud を利用開始するにあたって、自分の <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> アカウント(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Gmail">Gmail</a>) でも利用できるけど、他のやり方もあるようだったものの、最初はよくわからなかった。
なので、とりあえず ユーザーアカウントに関して調べたことを書いておく。認識が合っているかはわからない。
実際には Cloud IAM と絡めて使うことになるけど、いったんそこには触れない。</p>

<p>ちなみに、<a class="keyword" href="http://d.hatena.ne.jp/keyword/GCP">GCP</a> は <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud というブランドに包括されていたらしいけど、今どういう感じになっているのかわかってない。</p>

<ul>
<li><a href="https://cloud.googleblog.com/2016/09/introducing-Google-Cloud.html">Official Google Cloud Blog: Introducing Google Cloud</a></li>
</ul>


<p>一応 <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C0%A5%C3%A5%B7%A5%E5">ダッシュ</a>ボードの左上の表記は、現状「<a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud Platform」となっているが、Compute Engine などの一通りのサービスはドキュメント等では <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud のサービス、という感じのように見受けられる。<a class="keyword" href="http://d.hatena.ne.jp/keyword/GCP">GCP</a> というのがどの部分を指すものなのかちょっとわからなくなったけど <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud のサービスを動かしたり管理する場所が <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud Platform という感じなのかもしれない、と勝手に思っている。</p>

<p>ただ、最近の <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud の書籍を見ると、<a class="keyword" href="http://d.hatena.ne.jp/keyword/GCP">GCP</a> は旧称みたいになっているのも見受けられるので、とりあえず、ここでは <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud とする。</p>

<h2><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud  の利用開始のために必要となるユーザーアカウント</h2>

<p>以下のいずれかが必要となる。</p>

<ul>
<li>個人利用

<ul>
<li><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> アカウント(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Gmail">Gmail</a>)</li>
</ul>
</li>
<li>企業利用

<ul>
<li>Cloud Identity (ビジネスユースのようではあるが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>を持っていれば個人でも利用はできる)</li>
<li><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Workspace</li>
</ul>
</li>
</ul>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud の「リソース階層」の機能を利用する場合、Cloud Identity or <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Workspace を利用する必要がある。そうでない場合は個人アカウントでも良い。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud 上でのユーザーアカウントには <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> アカウント(Cloud Identify または個人)が用いられ、そのユーザーアカウントに対する認証・認可は <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud の Cloud IAM で行われアクセス件の制御がされる)、という感じと思われる。
Cloud Identity(Premium), <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Workspace は有料のサービス。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud の請求周りに関しては設定が必要なので、いずれを用いて <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud 利用し始めてもそれだけで <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud の料金が発生したりはしない。</p>

<h3>Cloud Identity</h3>

<p>Cloud Identity は、企業利用時に <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> アカウントを管理するためのサービス。とは言え、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>を持っていれば個人でも利用できる。
Cloud Identity は Free と Premium の2種類があり、 Free の場合は無料で利用できる。
機能差等は以下あたりに記載されている。</p>

<ul>
<li><a href="https://support.google.com/cloudidentity/answer/7431902?hl=ja">Cloud Identity &#x306E;&#x6A5F;&#x80FD;&#x3068;&#x30A8;&#x30C7;&#x30A3;&#x30B7;&#x30E7;&#x30F3;&#x306E;&#x6BD4;&#x8F03; - Cloud Identity &#x30D8;&#x30EB;&#x30D7;</a></li>
</ul>


<h4>Cloud Identity を開始する手順</h4>

<p>Cloud Identity を利用する場合は以下に沿って対応する。</p>

<ul>
<li><a href="https://support.google.com/cloudidentity/answer/7389973?hl=ja&ref_topic=7555414">Cloud Identity &#x306B;&#x7533;&#x3057;&#x8FBC;&#x3080; - Cloud Identity &#x30D8;&#x30EB;&#x30D7;</a>

<ul>
<li><a href="https://support.google.com/cloudidentity/answer/7390066?hl=ja&ref_topic=7555414">Cloud Identity &#x30A2;&#x30AB;&#x30A6;&#x30F3;&#x30C8;&#x3068; 1 &#x4EBA;&#x76EE;&#x306E;&#x7BA1;&#x7406;&#x30E6;&#x30FC;&#x30B6;&#x30FC;&#x3092;&#x4F5C;&#x6210;&#x3059;&#x308B; - Cloud Identity &#x30D8;&#x30EB;&#x30D7;</a></li>
<li><a href="https://support.google.com/cloudidentity/topic/7390701?hl=ja&ref_topic=7555414">Cloud Identity &#x306E;&#x30C9;&#x30E1;&#x30A4;&#x30F3;&#x6240;&#x6709;&#x6A29;&#x306E;&#x78BA;&#x8A8D; - Cloud Identity &#x30D8;&#x30EB;&#x30D7;</a>

<ul>
<li><a href="https://support.google.com/cloudidentity/answer/7331013?hl=ja&ref_topic=7390701">&#x73FE;&#x5728;&#x6240;&#x6709;&#x3057;&#x3066;&#x3044;&#x308B;&#x30C9;&#x30E1;&#x30A4;&#x30F3;&#x3092;&#x4F7F;&#x7528;&#x3057;&#x305F; Cloud Identity &#x306E;&#x8A2D;&#x5B9A; - Cloud Identity &#x30D8;&#x30EB;&#x30D7;</a></li>
</ul>
</li>
<li><a href="https://support.google.com/cloudidentity/answer/7332836?hl=ja&ref_topic=7555414">Cloud Identity &#x306E;&#x30E6;&#x30FC;&#x30B6;&#x30FC; &#x30A2;&#x30AB;&#x30A6;&#x30F3;&#x30C8;&#x3092;&#x4F5C;&#x6210;&#x3059;&#x308B; - Cloud Identity &#x30D8;&#x30EB;&#x30D7;</a></li>
<li><a href="https://support.google.com/cloudidentity/answer/7389975?hl=ja&ref_topic=7555414">Google Cloud Console &#x3092;&#x4F7F;&#x7528;&#x3057;&#x3066;&#x8A2D;&#x5B9A;&#x624B;&#x9806;&#x3092;&#x5B8C;&#x4E86;&#x3059;&#x308B; - Cloud Identity &#x30D8;&#x30EB;&#x30D7;</a></li>
</ul>
</li>
</ul>


<h3><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Workspace</h3>

<p>旧G Suite。
直接セットアップしたことがないので詳しいことはわからないけど、完全に企業利用なら <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Workspace を選択することになると思う。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Workspace を利用する場合でも、ユーザー管理部分は裏では Cloud Identity が利用されるらしい。</p>

<ul>
<li><a href="https://support.google.com/cloudidentity/answer/7319251?hl=ja">Cloud Identity &#x3068;&#x306F; - Cloud Identity &#x30D8;&#x30EB;&#x30D7;</a></li>
</ul>


<h3>個人 <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> アカウント(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Gmail">Gmail</a>)</h3>

<p>個人の <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> アカウント (<a class="keyword" href="http://d.hatena.ne.jp/keyword/Gmail">Gmail</a>) を使って <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud の管理画面にアクセスすることで利用を開始することができる。
この場合は当然個人の <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> アカウントでの利用となる。
上述の通り <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud 上での「リソース階層」の機能である「組織」などの設定は行えない。<a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud 上で「プロジェクト」を作成することで、いろいろ触れるようになる。(この辺は <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud の機能の話)</p>

<h2>まとめ</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud を利用する場合のユーザーアカウントについて調べたことをザッと整理した。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Workspace とかが <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud とどう関連しているのかがわかりづらくその辺で結構混乱したが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud のサービスを互いに連携させて利用できるもの、というのが分かると理解しやすくなった。</p>

<p>個人で管理機能も含めた一通りの <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud の機能を触りたい場合は<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>を取得して、Cloud Identity(Free) を利用するのがよさそう。
主要な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%A6%A5%C9">クラウド</a>サービス部分(コンピューティングなど)だけであれば個人 <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> アカウントでもいけそう、という感じだった。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud 上でのユーザーは <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> アカウント(Cloud Identify または個人)が用いられ、そのユーザーに対する認証・認可は <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud の Cloud IAM で行われる、という感じだが、この辺が <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud のサービス間の連携で成り立っているっぽいところが、結構わかりづらかった。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> の IAM の概念が頭にあったりするのが良くないのかもしれない。</p>

<h2>参考</h2>

<ul>
<li><a href="https://support.google.com/cloudidentity/answer/33310?hl=ja">&#x65B0;&#x898F;&#x30E6;&#x30FC;&#x30B6;&#x30FC;&#x306E;&#x30A2;&#x30AB;&#x30A6;&#x30F3;&#x30C8;&#x3092;&#x8FFD;&#x52A0;&#x3059;&#x308B; - Cloud Identity &#x30D8;&#x30EB;&#x30D7;</a></li>
<li><a href="https://medium.com/google-cloud-jp/gcp-iam-beginner-b2e1ef7ad9c2">GCP &#x306E; IAM &#x3092;&#x304A;&#x3055;&#x3089;&#x3044;&#x3057;&#x3088;&#x3046;. &#x3053;&#x306E;&#x8A18;&#x4E8B;&#x306F; Google Cloud Japan Customer&hellip; | by Yutty Kawahara | google-cloud-jp | Medium</a></li>
<li><a href="https://medium.com/google-cloud-jp/google-cloud-access-management-40963bea0dfc">Google Cloud &#x306E;&#x30A2;&#x30AF;&#x30BB;&#x30B9;&#x7BA1;&#x7406;&#x3092;&#x304A;&#x3055;&#x3089;&#x3044;&#x3057;&#x3088;&#x3046; 2020&#x30A2;&#x30C3;&#x30D7;&#x30C7;&#x30FC;&#x30C8;&#x7248; | by Yutty Kawahara | google-cloud-jp | Medium</a></li>
<li><a href="https://support.google.com/cloudidentity/topic/7555414?hl=ja&ref_topic=7516500">GCP &#x7BA1;&#x7406;&#x8005;&#x5411;&#x3051;&#x306E;&#x8A2D;&#x5B9A;&#x624B;&#x9806; - Cloud Identity &#x30D8;&#x30EB;&#x30D7;</a></li>
<li><a href="https://workspace.google.com/signup/gcpidentity/welcome#0">Sign up for Cloud Identity</a></li>
</ul>


