---
title: "EC2 Spot Instance, Fargate Spot  が停止したりECSタスクが異常終了した時に Slack に通知する仕組みを CloudFormation で作ってみた"
date: 2023-07-02
slug: "EC2 Spot Instance, Fargate Spot  が停止したりECSタスクが異常終了した時に Slack に通知する仕組みを CloudFormation で作ってみた"
category: blog
tags: [AWS,AWS CloudFormation,Amazon ECS,Amazon EC2]
---
<h2 id="はじめに">はじめに</h2>

<p>ECS on EC2 の Spot Instance や Fargate Spot を結構使っているのですが、停止した時に気づくための仕組みが Lambda だと管理が面倒です。
<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Chatbot が EventBridge をサポートしているので、EventBridge でイベントを拾って <a class="keyword" href="https://d.hatena.ne.jp/keyword/SNS">SNS</a> を経由して Slack に通知させるための仕組みを CFn で作ってみました。</p>

<h2 id="目次">目次</h2>

<ul class="table-of-contents">
    <li><a href="#はじめに">はじめに</a></li>
    <li><a href="#目次">目次</a></li>
    <li><a href="#サンプル-CFn-テンプレート">サンプル CFn テンプレート</a></li>
    <li><a href="#まとめ">まとめ</a></li>
    <li><a href="#参考">参考</a></li>
</ul>

<h2 id="サンプル-CFn-テンプレート">サンプル CFn テンプレート</h2>

<p>早速ですがサンプルの CFn テンプレートです。
ここでは面倒なので各イベントを全て1つのテンプレートにまとめてます。</p>

<p>Slack と <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Chatbot の連携設定については割愛します。以下あたりのドキュメントに記載があります。</p>

<ul>
<li><a href="https://docs.aws.amazon.com/ja_jp/chatbot/latest/adminguide/slack-setup.html">Tutorial: Get started with Slack - AWS Chatbot</a></li>
</ul>


<p>以下のテンプレートで、<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Chatbotの定義のSlackの<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EF%A1%BC%A5%AF%A5%B9%A5%DA%A1%BC%A5%B9">ワークスペース</a>とチャンネルIDを環境に合わせて設定し、スタックを作成します。
ECS <a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーは環境に応じて書き換えます。</p>

<pre class="code lang-yaml" data-lang="yaml" data-unlink><span class="synIdentifier">AWSTemplateFormatVersion</span><span class="synSpecial">:</span> <span class="synConstant">&quot;2010-09-09&quot;</span>
<span class="synIdentifier">Description</span><span class="synSpecial">:</span> SNS Topic and EventBridge for SpotInstance Intteruption Notify

<span class="synIdentifier">Resources</span><span class="synSpecial">:</span>
 <span class="synComment"> # ----------------------------------------------------------------</span>
 <span class="synComment"> # Define of IAM</span>
 <span class="synComment"> # ----------------------------------------------------------------</span>
  <span class="synIdentifier">SpotInstanceInterruptSlackNotifyIAMRole</span><span class="synSpecial">:</span>
    <span class="synIdentifier">Type</span><span class="synSpecial">:</span> AWS::IAM::Role
    <span class="synIdentifier">Properties</span><span class="synSpecial">:</span>
     <span class="synComment"> #RoleName: !Sub ${AWS::StackName}-iam-role</span>
      <span class="synIdentifier">AssumeRolePolicyDocument</span><span class="synSpecial">:</span>
        <span class="synIdentifier">Version</span><span class="synSpecial">:</span> 2012-10-17
        <span class="synIdentifier">Statement</span><span class="synSpecial">:</span>
          <span class="synStatement">- </span><span class="synIdentifier">Effect</span><span class="synSpecial">:</span> Allow
            <span class="synIdentifier">Principal</span><span class="synSpecial">:</span>
              <span class="synIdentifier">Service</span><span class="synSpecial">:</span> chatbot.amazonaws.com
            <span class="synIdentifier">Action</span><span class="synSpecial">:</span> sts:AssumeRole
      <span class="synIdentifier">ManagedPolicyArns</span><span class="synSpecial">:</span>
        <span class="synStatement">- </span>arn:aws:iam::aws:policy/CloudWatchReadOnlyAccess

 <span class="synComment"> # ----------------------------------------------------------------</span>
 <span class="synComment"> # Define of EventBridge For Spot Instance Stopped</span>
 <span class="synComment"> # ----------------------------------------------------------------</span>
  <span class="synIdentifier">SpotInstanceInterruptEventRule</span><span class="synSpecial">:</span>
    <span class="synIdentifier">Type</span><span class="synSpecial">:</span> AWS::Events::Rule
    <span class="synIdentifier">DependsOn</span><span class="synSpecial">:</span>
      <span class="synStatement">- </span>SpotInstanceInterruptSlackNotifySNSTopic
      <span class="synStatement">- </span>SpotInstanceInterruptSlackNotifySNSTopicPolicy
    <span class="synIdentifier">Properties</span><span class="synSpecial">:</span>
      <span class="synIdentifier">Description</span><span class="synSpecial">:</span> <span class="synConstant">&quot;EventRule&quot;</span>
      <span class="synIdentifier">EventPattern</span><span class="synSpecial">:</span>
        <span class="synIdentifier">source</span><span class="synSpecial">:</span>
          <span class="synStatement">- </span><span class="synConstant">&quot;aws.ec2&quot;</span>
        <span class="synIdentifier">detail-type</span><span class="synSpecial">:</span>
          <span class="synStatement">- </span><span class="synConstant">&quot;EC2 Spot Instance Interruption Warning&quot;</span>
      <span class="synIdentifier">State</span><span class="synSpecial">:</span> <span class="synConstant">&quot;ENABLED&quot;</span>
      <span class="synIdentifier">Targets</span><span class="synSpecial">:</span>
        -
          <span class="synIdentifier">Arn</span><span class="synSpecial">:</span>
            <span class="synIdentifier">Ref</span><span class="synSpecial">:</span> <span class="synConstant">&quot;SpotInstanceInterruptSlackNotifySNSTopic&quot;</span>
          <span class="synIdentifier">Id</span><span class="synSpecial">:</span> <span class="synConstant">&quot;SpotInstanceInterruptSlackNotifySNSTopic&quot;</span>

 <span class="synComment"> # ----------------------------------------------------------------</span>
 <span class="synComment"> # Define of EventBridge For Fargate Spot Stopped</span>
 <span class="synComment"> # ----------------------------------------------------------------</span>
  <span class="synIdentifier">FargateSpotStoppedEventRule</span><span class="synSpecial">:</span>
    <span class="synIdentifier">Type</span><span class="synSpecial">:</span> AWS::Events::Rule
    <span class="synIdentifier">DependsOn</span><span class="synSpecial">:</span>
      <span class="synStatement">- </span>SpotInstanceInterruptSlackNotifySNSTopic
      <span class="synStatement">- </span>SpotInstanceInterruptSlackNotifySNSTopicPolicy
    <span class="synIdentifier">Properties</span><span class="synSpecial">:</span>
      <span class="synIdentifier">Description</span><span class="synSpecial">:</span> <span class="synConstant">&quot;EventRule&quot;</span>
      <span class="synIdentifier">EventPattern</span><span class="synSpecial">:</span>
        <span class="synIdentifier">source</span><span class="synSpecial">:</span>
          <span class="synStatement">- </span><span class="synConstant">&quot;aws.ecs&quot;</span>
        <span class="synIdentifier">detail-type</span><span class="synSpecial">:</span>
          <span class="synStatement">- </span><span class="synConstant">&quot;ECS Task State Change&quot;</span>
        <span class="synIdentifier">detail</span><span class="synSpecial">:</span>
          <span class="synIdentifier">stopCode</span><span class="synSpecial">:</span>
            <span class="synStatement">- </span><span class="synConstant">&quot;SpotInterruption&quot;</span>
        <span class="synIdentifier">clusterArn</span><span class="synSpecial">:</span>
          <span class="synStatement">- </span><span class="synConstant">&quot;arn:aws:ecs:exampleregion:1111222233334444:cluster/examplecluster&quot;</span>
      <span class="synIdentifier">State</span><span class="synSpecial">:</span> <span class="synConstant">&quot;ENABLED&quot;</span>
      <span class="synIdentifier">Targets</span><span class="synSpecial">:</span>
        -
          <span class="synIdentifier">Arn</span><span class="synSpecial">:</span>
            <span class="synIdentifier">Ref</span><span class="synSpecial">:</span> <span class="synConstant">&quot;SpotInstanceInterruptSlackNotifySNSTopic&quot;</span>
          <span class="synIdentifier">Id</span><span class="synSpecial">:</span> <span class="synConstant">&quot;SpotInstanceInterruptSlackNotifySNSTopic&quot;</span>

 <span class="synComment"> # ----------------------------------------------------------------</span>
 <span class="synComment"> # Define of EventBridge For ECS Task Failed</span>
 <span class="synComment"> # ----------------------------------------------------------------</span>
  <span class="synIdentifier">ECSTaskStoppedEventRule</span><span class="synSpecial">:</span>
    <span class="synIdentifier">Type</span><span class="synSpecial">:</span> AWS::Events::Rule
    <span class="synIdentifier">DependsOn</span><span class="synSpecial">:</span>
      <span class="synStatement">- </span>SpotInstanceInterruptSlackNotifySNSTopic
      <span class="synStatement">- </span>SpotInstanceInterruptSlackNotifySNSTopicPolicy
    <span class="synIdentifier">Properties</span><span class="synSpecial">:</span>
      <span class="synIdentifier">Description</span><span class="synSpecial">:</span> <span class="synConstant">&quot;EventRule&quot;</span>
      <span class="synIdentifier">EventPattern</span><span class="synSpecial">:</span>
        <span class="synIdentifier">source</span><span class="synSpecial">:</span>
          <span class="synStatement">- </span><span class="synConstant">&quot;aws.ecs&quot;</span>
        <span class="synIdentifier">detail-type</span><span class="synSpecial">:</span>
          <span class="synStatement">- </span><span class="synConstant">&quot;ECS Task State Change&quot;</span>
        <span class="synIdentifier">detail</span><span class="synSpecial">:</span>
          <span class="synIdentifier">lastStatus</span><span class="synSpecial">:</span> 
            <span class="synStatement">- </span><span class="synConstant">&quot;STOPPED&quot;</span>
          <span class="synIdentifier">stoppedReason</span><span class="synSpecial">:</span>
            <span class="synStatement">- </span><span class="synIdentifier">anything-but</span><span class="synSpecial">:</span>
                <span class="synConstant">&quot;prefix&quot;</span><span class="synSpecial">:</span> <span class="synConstant">&quot;Scaling activity initiated by&quot;</span>
          <span class="synIdentifier">clusterArn</span><span class="synSpecial">:</span>
            <span class="synStatement">- </span><span class="synConstant">&quot;arn:aws:ecs:exampleregion:1111222233334444:cluster/examplecluster&quot;</span>
          <span class="synIdentifier">containers</span><span class="synSpecial">:</span>
            <span class="synIdentifier">exitCode</span><span class="synSpecial">:</span>
              <span class="synStatement">- </span><span class="synConstant">&quot;anything-but&quot;</span><span class="synSpecial">:</span> <span class="synConstant">0</span>
      <span class="synIdentifier">State</span><span class="synSpecial">:</span> <span class="synConstant">&quot;ENABLED&quot;</span>
      <span class="synIdentifier">Targets</span><span class="synSpecial">:</span>
        -
          <span class="synIdentifier">Arn</span><span class="synSpecial">:</span>
            <span class="synIdentifier">Ref</span><span class="synSpecial">:</span> <span class="synConstant">&quot;SpotInstanceInterruptSlackNotifySNSTopic&quot;</span>
          <span class="synIdentifier">Id</span><span class="synSpecial">:</span> <span class="synConstant">&quot;SpotInstanceInterruptSlackNotifySNSTopic&quot;</span>

 <span class="synComment"> # ----------------------------------------------------------------</span>
 <span class="synComment"> # Define of SNS</span>
 <span class="synComment"> # ----------------------------------------------------------------</span>
  <span class="synIdentifier">SpotInstanceInterruptSlackNotifySNSTopic</span><span class="synSpecial">:</span>
    <span class="synIdentifier">Type</span><span class="synSpecial">:</span> AWS::SNS::Topic
    <span class="synIdentifier">Properties</span><span class="synSpecial">:</span>
      <span class="synIdentifier">TopicName</span><span class="synSpecial">:</span> <span class="synType">!Sub</span> ${AWS::StackName}-sns-topic

  <span class="synIdentifier">SpotInstanceInterruptSlackNotifySNSTopicPolicy</span><span class="synSpecial">:</span>
    <span class="synIdentifier">Type</span><span class="synSpecial">:</span> AWS::SNS::TopicPolicy
    <span class="synIdentifier">Properties</span><span class="synSpecial">:</span>
      <span class="synIdentifier">PolicyDocument</span><span class="synSpecial">:</span>
        <span class="synIdentifier">Statement</span><span class="synSpecial">:</span>
          <span class="synStatement">- </span><span class="synIdentifier">Effect</span><span class="synSpecial">:</span> Allow
            <span class="synIdentifier">Principal</span><span class="synSpecial">:</span>
              <span class="synIdentifier">Service</span><span class="synSpecial">:</span> events.amazonaws.com
            <span class="synIdentifier">Action</span><span class="synSpecial">:</span> sns:Publish
            <span class="synIdentifier">Resource</span><span class="synSpecial">:</span> <span class="synConstant">'*'</span>
      <span class="synIdentifier">Topics</span><span class="synSpecial">:</span>
        <span class="synStatement">- </span><span class="synType">!Ref</span> SpotInstanceInterruptSlackNotifySNSTopic

 <span class="synComment"> # ----------------------------------------------------------------</span>
 <span class="synComment"> # Define of Chatbot Slack Channel</span>
 <span class="synComment"> # ----------------------------------------------------------------</span>
  <span class="synIdentifier">SpotInstanceInterruptSlackNotifySlackChannelConfiguration</span><span class="synSpecial">:</span>
    <span class="synIdentifier">Type</span><span class="synSpecial">:</span> AWS::Chatbot::SlackChannelConfiguration
    <span class="synIdentifier">DependsOn</span><span class="synSpecial">:</span>
      <span class="synStatement">- </span>SpotInstanceInterruptSlackNotifySNSTopic
      <span class="synStatement">- </span>SpotInstanceInterruptSlackNotifySNSTopicPolicy
    <span class="synIdentifier">Properties</span><span class="synSpecial">:</span>
      <span class="synIdentifier">ConfigurationName</span><span class="synSpecial">:</span> <span class="synType">!Sub</span> ${AWS::StackName}-chatbot
      <span class="synIdentifier">IamRoleArn</span><span class="synSpecial">:</span> <span class="synType">!GetAtt</span> SpotInstanceInterruptSlackNotifyIAMRole.Arn
      <span class="synIdentifier">LoggingLevel</span><span class="synSpecial">:</span> ERROR
      <span class="synIdentifier">SlackChannelId</span><span class="synSpecial">:</span> <span class="synType">********</span>
      <span class="synIdentifier">SlackWorkspaceId</span><span class="synSpecial">:</span> <span class="synType">********</span>
      <span class="synIdentifier">SnsTopicArns</span><span class="synSpecial">:</span>
        <span class="synStatement">- </span><span class="synType">!Ref</span> SpotInstanceInterruptSlackNotifySNSTopic
</pre>


<h2 id="まとめ">まとめ</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Chatbot が EventBridge をサポートしてくれたおかげで、余計なLambda等を作ることなくいろんな通知を楽に Slack に集約できて良いです。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://repost.aws/ja/knowledge-center/fargate-spot-termination-notice">Fargate Spot &#x30BF;&#x30B9;&#x30AF;&#x3067;&#x30B9;&#x30DD;&#x30C3;&#x30C8;&#x7D42;&#x4E86;&#x901A;&#x77E5;&#x3092;&#x51E6;&#x7406;&#x3059;&#x308B; | AWS re:Post</a></li>
<li><a href="https://docs.aws.amazon.com/ja_jp/chatbot/latest/adminguide/related-services.html">Monitoring AWS services using AWS Chatbot - AWS Chatbot</a></li>
<li><a href="https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/userguide/ecs_cwet2.html">&#x30C1;&#x30E5;&#x30FC;&#x30C8;&#x30EA;&#x30A2;&#x30EB;:&#x30BF;&#x30B9;&#x30AF;&#x505C;&#x6B62;&#x30A4;&#x30D9;&#x30F3;&#x30C8;&#x306B; Amazon &#x30B7;&#x30F3;&#x30D7;&#x30EB; &#x901A;&#x77E5;&#x30B5;&#x30FC;&#x30D3;&#x30B9; &#x30A2;&#x30E9;&#x30FC;&#x30C8;&#x3092;&#x9001;&#x4FE1; - Amazon ECS</a></li>
</ul>


