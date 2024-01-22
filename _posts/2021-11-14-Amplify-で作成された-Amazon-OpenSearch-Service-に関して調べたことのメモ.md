---
layout: post
Categories:  ["tech"]
Description:  " Amplify で作成された Amazon OpenSearch Service に関するメモ書き。特に何がどうということはなく、本当にただ調べたことのメモ。 "
Tags: []
date: "2021-11-14T20:34:00+09:00"
title: "Amplify で作成された Amazon OpenSearch Service に関して調べたことのメモ"
author: "dshimizu"
archive: ["2021"]
draft: "true"
---

<body>
<p>Amplify で作成された <a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/OpenSearch">OpenSearch</a> Service に関するメモ書き。
特に何がどうということはなく、本当にただ調べたことのメモ。</p>
</body>

<!-- more -->

<body>
<p>Amplify で <code>@searchable</code> ディレクティブを使用すると、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon%20DynamoDB">Amazon DynamoDB</a> Streams と <a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/OpenSearch">OpenSearch</a> Service がプロビジョニングされる。
該当の DynamoDB Table に Insert されたデータを DynamoDB Streams がフックし、<a class="keyword" href="http://d.hatena.ne.jp/keyword/OpenSearch">OpenSearch</a> へデータが追加される動きになる。</p>

<p>Amplifyで作られる <a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/OpenSearch">OpenSearch</a> Service はデフォルトだと1ノードの t2.small.elasticsearch の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>タイプという貧弱なものになる。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%BF%A5%F3%A5%B9">インスタンス</a>タイプは変更可能で、ノード数も変更可能だが、Mulit-AZ 構成にはできないらしい。少なくとも現時点(2021/11)では。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Faws-amplify%2Famplify-cli%2Fissues%2F2207" title="Support multi-AZ Elasticsearch for high availability · Issue #2207 · aws-amplify/amplify-cli" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;"></iframe><cite class="hatena-citation"><a href="https://github.com/aws-amplify/amplify-cli/issues/2207">github.com</a></cite></p>

<p>また、バージョンも 6.2 と古いものでしか作れない。一応、手動でのバージョンアップで使えそうらしい。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Faws-amplify%2Famplify-cli%2Fissues%2F5229" title="[Enhancement] ES Version Support · Issue #5229 · aws-amplify/amplify-cli" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;"></iframe><cite class="hatena-citation"><a href="https://github.com/aws-amplify/amplify-cli/issues/5229">github.com</a></cite></p>

<p>ただ、こちらでいくつかの改善対応が進んでいる模様。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Faws-amplify%2Famplify-cli%2Fissues%2F7546" title="RFC: `@searchable` directive Enhancements · Issue #7546 · aws-amplify/amplify-cli" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;"></iframe><cite class="hatena-citation"><a href="https://github.com/aws-amplify/amplify-cli/issues/7546">github.com</a></cite></p>

<p>デフォルトの状態だとスペックが低くシングル構成なので、普通にプロセスが停止したりして、そうなるとインデックスが壊れる。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Faws-amplify%2Famplify-cli%2Fissues%2F8123" title="Elasticsearch Lost All Data · Issue #8123 · aws-amplify/amplify-cli" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;"></iframe><cite class="hatena-citation"><a href="https://github.com/aws-amplify/amplify-cli/issues/8123">github.com</a></cite></p>

<p>これを避けるにはスペックを上げたり台数を増やすとかして、今やれるそれなりの構成にするしかないと思う。</p>

<ul>
<li><a href="https://docs.amplify.aws/cli-legacy/graphql-transformer/config-params/#elasticsearchebsvolumegb">https://docs.amplify.aws/cli-legacy/graphql-transformer/config-params/#elasticsearchebsvolumegb</a></li>
</ul>


<p>ちなみに壊れたデータを復旧させたり、後から <code>@searchable</code> を追加した場合などのために、インデックスを作成し直す<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>が公開されている。</p>

<ul>
<li><a href="https://docs.amplify.aws/cli/graphql/troubleshooting/#backfill-opensearch-index-from-dynamodb-table">https://docs.amplify.aws/cli/graphql/troubleshooting/#backfill-opensearch-index-from-dynamodb-table</a></li>
</ul>


<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Faws-amplify%2Famplify-cli%2Fblob%2Fmaster%2Fpackages%2Fgraphql-elasticsearch-transformer%2Fscripts%2Fddb_to_es.py" title="amplify-cli/ddb_to_es.py at master · aws-amplify/amplify-cli" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;"></iframe><cite class="hatena-citation"><a href="https://github.com/aws-amplify/amplify-cli/blob/master/packages/graphql-elasticsearch-transformer/scripts/ddb_to_es.py">github.com</a></cite></p>
</body>
