---
title: " Terraform の設計時に考えたりしていること"
date: 2023-06-30
slug: " Terraform の設計時に考えたりしていること"
category: blog
tags: [WIP,Terraform]
---
<h2 id="はじめに">はじめに</h2>

<p>数年振りに Terraform を触っていて、設計時に検討すべきことが色々あっていつも迷うので簡単にまとめてみます。</p>

<h2 id="目次">目次</h2>

<ul class="table-of-contents">
    <li><a href="#はじめに">はじめに</a></li>
    <li><a href="#目次">目次</a></li>
    <li><a href="#検討項目">検討項目</a><ul>
            <li><a href="#命名規則">命名規則</a></li>
            <li><a href="#Terraform-と-provider-のバージョン管理">Terraform と provider のバージョン管理</a></li>
            <li><a href="#terraformlockhcl">.terraform.lock.hcl</a></li>
            <li><a href="#modules-ディレクトリ構成tfstate-の粒度分割範囲">modules /ディレクトリ構成、tfstate の粒度/分割範囲</a></li>
            <li><a href="#その他">その他</a></li>
        </ul>
    </li>
    <li><a href="#参考">参考</a></li>
</ul>

<h2 id="検討項目">検討項目</h2>

<p>以下のようなことを検討します。</p>

<h3 id="命名規則"><a class="keyword" href="https://d.hatena.ne.jp/keyword/%CC%BF%CC%BE%B5%AC%C2%A7">命名規則</a></h3>

<p>細かいルールに関してはチーム内で決める必要はありますが、大枠は以下に則ります。</p>

<ul>
<li><a href="https://www.terraform-best-practices.com/naming/">Naming conventions - Terraform Best Practices</a></li>
<li><a href="https://cloud.google.com/docs/terraform/best-practices-for-terraform?hl=ja#naming-convention">Terraform &#x3092;&#x4F7F;&#x7528;&#x3059;&#x308B;&#x305F;&#x3081;&#x306E;&#x30D9;&#x30B9;&#x30C8; &#x30D7;&#x30E9;&#x30AF;&#x30C6;&#x30A3;&#x30B9; &nbsp;|&nbsp; Google Cloud</a></li>
</ul>


<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/Google">Google</a> Cloud のドキュメントの下記は参考になります。</p>

<blockquote><p>単語の区切りにはアンダースコアを使用します。
リソースタイプの 1 つ（モジュール全体の単一のロードバランサなど）に対する参照を簡素化するため、リソースに main という名前を付けます。
同じタイプのリソースを区別するには（たとえば、primary と secondary）、意味のあるリソース名を指定します。
リソース名を単数形にします。
リソース名でリソースタイプを繰り返さないでください。</p></blockquote>

<h3 id="Terraform-と-provider-のバージョン管理">Terraform と provider のバージョン管理</h3>

<p>下記のような env 毎に<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを細分化する場合、特に初期構築時等は <code>terraform init</code> をするタイミングによって provider のバージョンに微妙な差分が出やすいので、共通のものを利用できるようにパッチバージョンまで定義します。</p>

<pre class="code lang-terraform" data-lang="terraform" data-unlink><span class="synType">terraform</span> <span class="synSpecial">{</span>
  <span class="synComment"># * Terraform のバージョン定義</span>
  <span class="synIdentifier">required_version</span> = <span class="synConstant">&quot;~&gt; 1.2.0&quot;</span>

  <span class="synComment"># * provider とそのバージョンの定義</span>
  <span class="synType">required_providers</span> <span class="synSpecial">{</span>
    <span class="synIdentifier">aws</span> = <span class="synSpecial">{</span>
      <span class="synIdentifier">source</span>  = <span class="synConstant">&quot;hashicorp/aws&quot;</span>
      <span class="synIdentifier">version</span> = <span class="synConstant">&quot;~&gt; 5.8.0&quot;</span>
    <span class="synSpecial">}</span>
  <span class="synSpecial">}</span>

  <span class="synType">backend</span> <span class="synConstant">&quot;s3&quot;</span> <span class="synSpecial">{</span>
    <span class="synIdentifier">bucket</span> = <span class="synConstant">&quot;hogehoge-terraform-bucket&quot;</span>
    <span class="synIdentifier">key</span> = <span class="synConstant">&quot;xxxx/terraform.tfstate&quot;</span>
    <span class="synIdentifier">region</span> = <span class="synConstant">&quot;ap-northeast-1&quot;</span>
  <span class="synSpecial">}</span>
}
</pre>


<p>Terraform 自体のバージョン管理のベストプ<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%E9%A5%AF">ラク</a>ティスは、<code>required_version = "~&gt; 1.0.0"</code> のような形として、 <code>~&gt;</code> を利用してメジャーバージョンとマイナーバージョンを固定することのようです。</p>

<ul>
<li><a href="https://developer.hashicorp.com/terraform/tutorials/configuration-language/versions#terraform-version-constraints">Manage Terraform Versions | Terraform | HashiCorp Developer</a></li>
</ul>


<p>Provider のバージョンは Provider によると思いますが、<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a>用のProviderは結構頻繁にマイナーバージョンもリリースされている印象なので、慎重に行くなら、メジャーバージョンとマイナーバージョンを固定した方が安全かなと思います。
メジャーバージョンがリリースされると原則旧バージョンへの修正はされなくなるようなので、定期的なバージョンアップは必要になりそうです。</p>

<ul>
<li><a href="https://hashicorp.github.io/terraform-provider-aws/faq/#once-a-major-release-is-published-will-new-features-and-fixes-be-backported-to-previous-versions">FAQ - Terraform AWS Provider - Contributor Guide</a></li>
</ul>


<h3 id="terraformlockhcl">.terraform.lock.hcl</h3>

<p><code>.terraform.lock.hcl</code> は、利用している Provider の各プラットフォーム用のバージョンの<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C1%A5%A7%A5%C3%A5%AF%A5%B5%A5%E0">チェックサム</a>を記録したロックファイルになると思ってます。</p>

<p>これの扱いについてはあまりわかっておらず、v1.4 未満の環境では .gitignore で誤魔化してましたが v1.4 から色々変わったようで、特にローカルと外部でCIなどを組み合わせて動かしている場合は運用を検討する必要がありそうでした。</p>

<ul>
<li><a href="https://qiita.com/minamijoyo/items/a3fef269fecbf7f54c2f">Terraform v1.4&#x3067;&#x306E;TF_PLUGIN_CACHE_DIR&#x3068;.terraform.lock.hcl&#x306E;&#x6319;&#x52D5;&#x5909;&#x66F4; - Qiita</a></li>
</ul>


<h3 id="modules-ディレクトリ構成tfstate-の粒度分割範囲">modules /<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ構成、tfstate の粒度/分割範囲</h3>

<p>例では <code>main.tf</code> ファイルとしていて、それのみ記載してますが、<code>variables.tf</code>, <code>outputs.tf</code> など配置します。場合によっては <code>main.tf</code> の中身を <code>hogehoge.tf</code> などに分割していきます。
<code>main.tf</code> には特定の<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a>リソース単位で定義するのではなく、ある程度ライフサイクルの同じものでまとめるようにしてます。
<code>modules</code> もある程度のカタマリになるように検討します。</p>

<p>tfstate を小さく保ちたいので<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ構成自体は結構ごちゃごちゃしてますが、この辺は<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C8%A5%EC%A1%BC%A5%C9%A5%AA%A5%D5">トレードオフ</a>と割り切ってます。やっていくうちに変更したくなると思います。
<code>provider</code> に関しては、1つファイルを作って、必要な<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リに<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B7%A5%F3%A5%DC%A5%EA%A5%C3%A5%AF%A5%EA%A5%F3%A5%AF">シンボリックリンク</a>を貼る、とかが良さそうです。</p>

<ul>
<li>例</li>
</ul>


<pre class="code lang-tex" data-lang="tex" data-unlink>├── backend/
│   ├── environments/
│   │   ├── dev/
│   │   │    └── main.tf
│   │   └── prod/
│   │      └── main.tf
│   └── modules/
│       ├──  backend/
│       │   └── main.tf
│       └──  waf/
│           └── main.tf
├── database/
│   ├── environments/
│   │  ├── dev/
│   │  │    └── main.tf
│   │  └── prod/
│   │     └──  main.tf
│   └── modules/
: 
:  
└── common/
   └── networks/
        ├── environments/
        │  ├── dev/
        │  │   └──  main.tf
        │  └── prod/
        │       └──  main.tf
        └── modules/
                └──  vpc/
                      └──  main.tf
</pre>


<ul>
<li><a href="https://developer.hashicorp.com/terraform/tutorials/modules/pattern-module-creation">Module Creation - Recommended Pattern | Terraform | HashiCorp Developer</a></li>
</ul>


<h3 id="その他">その他</h3>

<p>全体的に複雑にならないように、凝った書き方や<a class="keyword" href="https://d.hatena.ne.jp/keyword/%BB%B0%B9%E0%B1%E9%BB%BB%BB%D2">三項演算子</a>の多用を避けて、シンプルに素朴に書くようにしてます。
この辺も <a class="keyword" href="https://d.hatena.ne.jp/keyword/Google">Google</a> Cloud のドキュメントが参考になります。</p>

<ul>
<li><a href="https://cloud.google.com/docs/terraform/best-practices-for-terraform?hl=ja">Terraform &#x3092;&#x4F7F;&#x7528;&#x3059;&#x308B;&#x305F;&#x3081;&#x306E;&#x30D9;&#x30B9;&#x30C8; &#x30D7;&#x30E9;&#x30AF;&#x30C6;&#x30A3;&#x30B9; &nbsp;|&nbsp; Google Cloud</a></li>
</ul>


<h2 id="参考">参考</h2>

<ul>
<li><a href="https://developer.hashicorp.com/terraform/tutorials/modules/pattern-module-creation">Module Creation - Recommended Pattern | Terraform | HashiCorp Developer</a></li>
<li><a href="https://www.terraform-best-practices.com/naming/">Naming conventions - Terraform Best Practices</a></li>
<li><a href="https://cloud.google.com/docs/terraform/best-practices-for-terraform?hl=ja">Terraform &#x3092;&#x4F7F;&#x7528;&#x3059;&#x308B;&#x305F;&#x3081;&#x306E;&#x30D9;&#x30B9;&#x30C8; &#x30D7;&#x30E9;&#x30AF;&#x30C6;&#x30A3;&#x30B9; &nbsp;|&nbsp; Google Cloud</a></li>
<li><a href="https://tech.excite.co.jp/entry/2022/03/10/090000">Terraform&#x306E;&#x30C7;&#x30A3;&#x30EC;&#x30AF;&#x30C8;&#x30EA;&#x69CB;&#x9020;&#x3068;&#x8A2D;&#x8A08;&#x6307;&#x91DD;&#x306E;&#x4E00;&#x4F8B;&#x3092;&#x7D39;&#x4ECB;&#x3057;&#x307E;&#x3059; - &#x30A8;&#x30AD;&#x30B5;&#x30A4;&#x30C8; TechBlog.</a></li>
<li><a href="https://zenn.dev/sway/articles/terraform_style_srcstructure">&#x8A2D;&#x8A08;&#x3092;&#x7406;&#x89E3;&#x3057;&#x3084;&#x3059;&#x3044;&#x30BD;&#x30FC;&#x30B9;&#x69CB;&#x6210;&#x3067;&#x66F8;&#x304F; - Terraform&#x306E;&#x304D;&#x307B;&#x3093;&#x3068;&#x5FDC;&#x7528;</a></li>
<li><a href="https://developer.hashicorp.com/terraform/tutorials/configuration-language/versions#terraform-version-constraints">Manage Terraform Versions | Terraform | HashiCorp Developer</a></li>
</ul>


