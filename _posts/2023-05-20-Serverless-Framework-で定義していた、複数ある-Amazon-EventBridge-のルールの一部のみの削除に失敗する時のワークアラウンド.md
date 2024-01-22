---
title: "Serverless Framework で定義していた、複数ある Amazon EventBridge のルールの一部のみの削除に失敗する時のワークアラウンド"
date: 2023-05-20
slug: "Serverless Framework で定義していた、複数ある Amazon EventBridge のルールの一部のみの削除に失敗する時のワークアラウンド"
category: blog
tags: [AWS,Serverless Framework]
---
<h2 id="はじめに">はじめに</h2>

<p>Serverless Framework の <code>serverless.yml</code> で定義していた複数の EventBridge のルールで、一部のルールを削除してデプロイしたところ、エラーが発生してデプロイに失敗しました。
そのことに関して調べたことのメモです。</p>

<h2 id="エラー内容">エラー内容</h2>

<p>具体的には、下記のような 5 個あるルール(設定値は適当)のうちの、3つ目を削除するため <code>serverless.yml</code> から削除してデプロイしたところ、元々 4つ目と5つ目に定義されていたルールで、CloudFormation で内部的に定義されているであろう ID が 3, 4 へと繰り上がろうとし、一時的に同名のリソースが出来上がろうとしたと思われる形でエラーとなります。</p>

<ul>
<li>before</li>
</ul>


<pre class="code lang-yaml" data-lang="yaml" data-unlink><span class="synIdentifier">functions</span><span class="synSpecial">:</span>
  <span class="synIdentifier">hello</span><span class="synSpecial">:</span>
    <span class="synIdentifier">handler</span><span class="synSpecial">:</span> hello
    <span class="synIdentifier">events</span><span class="synSpecial">:</span>
      <span class="synStatement">- </span><span class="synIdentifier">schedule</span><span class="synSpecial">:</span> rate(1 hours)
      <span class="synStatement">- </span><span class="synIdentifier">schedule</span><span class="synSpecial">:</span> cron(0 <span class="synConstant">9</span> * * <span class="synSpecial">?</span> <span class="synType">*)</span>
      <span class="synStatement">- </span><span class="synIdentifier">schedule</span><span class="synSpecial">:</span> cron(30 <span class="synConstant">0</span> * * <span class="synSpecial">?</span> <span class="synType">*)</span>
      <span class="synStatement">- </span><span class="synIdentifier">schedule</span><span class="synSpecial">:</span>
          <span class="synIdentifier">rate</span><span class="synSpecial">:</span>
            <span class="synStatement">- </span>cron(0 0/4 <span class="synSpecial">?</span> * MON-FRI <span class="synType">*)</span>
            <span class="synStatement">- </span>cron(0 <span class="synConstant">2</span> <span class="synSpecial">?</span> * SAT-SUN <span class="synType">*)</span>
      <span class="synStatement">- </span><span class="synIdentifier">schedule</span><span class="synSpecial">:</span> cron(0 <span class="synConstant">13</span> * * <span class="synSpecial">?</span> <span class="synType">*)</span>
</pre>


<ul>
<li>after</li>
</ul>


<pre class="code lang-yaml" data-lang="yaml" data-unlink><span class="synIdentifier">functions</span><span class="synSpecial">:</span>
  <span class="synIdentifier">hello</span><span class="synSpecial">:</span>
    <span class="synIdentifier">handler</span><span class="synSpecial">:</span> hello
    <span class="synIdentifier">events</span><span class="synSpecial">:</span>
      <span class="synStatement">- </span><span class="synIdentifier">schedule</span><span class="synSpecial">:</span> rate(1 hours)
      <span class="synStatement">- </span><span class="synIdentifier">schedule</span><span class="synSpecial">:</span> cron(0 <span class="synConstant">9</span> * * <span class="synSpecial">?</span> <span class="synType">*)</span>
     <span class="synComment"> #- schedule: cron(30 0 * * ? *)  # これを削除した</span>
      <span class="synStatement">- </span><span class="synIdentifier">schedule</span><span class="synSpecial">:</span>
          <span class="synIdentifier">rate</span><span class="synSpecial">:</span>
            <span class="synStatement">- </span>cron(0 0/4 <span class="synSpecial">?</span> * MON-FRI <span class="synType">*)</span>
            <span class="synStatement">- </span>cron(0 <span class="synConstant">2</span> <span class="synSpecial">?</span> * SAT-SUN <span class="synType">*)</span>
      <span class="synStatement">- </span><span class="synIdentifier">schedule</span><span class="synSpecial">:</span> cron(0 <span class="synConstant">13</span> * * <span class="synSpecial">?</span> <span class="synType">*)</span>
</pre>


<p><code>serverless deploy</code> でデプロイすると以下のようなエラーになりました。</p>

<pre class="code shell" data-lang="shell" data-unlink>:
CloudFormation - UPDATE_IN_PROGRESS - AWS::Events::Rule - helloEventsRuleSchedule3
CloudFormation - UPDATE_IN_PROGRESS - AWS::Events::Rule - helloEventsRuleSchedule4
CloudFormation - UPDATE_FAILED - AWS::Events::Rule - helloEventsRuleSchedule3
CloudFormation - UPDATE_FAILED - AWS::Events::Rule - helloEventsRuleSchedule4
:</pre>


<h2 id="ワークアラウンド"><a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EF%A1%BC%A5%AF%A5%A2%A5%E9%A5%A6%A5%F3%A5%C9">ワークアラウンド</a></h2>

<p>Issueやフォーラムを見ると、現状だとそういう動きになってしまうようです。</p>

<ul>
<li><a href="https://forum.serverless.com/t/problem-to-deploy-when-removing-a-schedule-from-a-lambda-function/1315">Problem to deploy when removing a schedule from a lambda function - Serverless Framework - Serverless Forums</a></li>
<li><a href="https://github.com/serverless/serverless/issues/3255">Problem to deploy when removing a schedule from a lambda function &middot; Issue #3255 &middot; serverless/serverless &middot; GitHub</a></li>
</ul>


<p>回避策としては <code>serverless remove</code> で一旦 CloudFormation スタックごと削除して再作成するか、該当の EventBridge のルール以下を全て削除して一度デプロイし、その後必要なルールを定義した状態にしてもう一度デプロイして再作成する形しかないようでした。</p>

<h2 id="まとめ">まとめ</h2>

<p>Serverless Framework で定義していた、複数ある <a class="keyword" href="https://d.hatena.ne.jp/keyword/Amazon">Amazon</a> EventBridge のルールの一部のみの削除に失敗する時の<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EF%A1%BC%A5%AF%A5%A2%A5%E9%A5%A6%A5%F3%A5%C9">ワークアラウンド</a>についてまとめました。
やや面倒ではありますがデプロイのタイミングを見計らって、一度ルールを一度削除して再作成するような形を取るしかなさそうです。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://forum.serverless.com/t/problem-to-deploy-when-removing-a-schedule-from-a-lambda-function/1315">Problem to deploy when removing a schedule from a lambda function - Serverless Framework - Serverless Forums</a></li>
<li><a href="https://github.com/serverless/serverless/issues/3255">Problem to deploy when removing a schedule from a lambda function &middot; Issue #3255 &middot; serverless/serverless &middot; GitHub</a></li>
</ul>


