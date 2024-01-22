---
title: "AWS Health Dashboard のイベントを Slack へ通知する処理を CloudFormation テンプレートで作ってみた"
date: 2023-05-28
slug: "AWS Health Dashboard のイベントを Slack へ通知する処理を CloudFormation テンプレートで作ってみた"
category: blog
tags: [AWS,AWS CloudFormation,AWS Health Dashboard]
---
<h2 id="はじめに">はじめに</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Health <a class="keyword" href="https://d.hatena.ne.jp/keyword/Dashboard">Dashboard</a> のイベントをマネジメントコンソールから確認するのは手間なので、 Slack へ通知する処理を作りたいなと思い、 CloudFormation テンプレートのサンプルを作りました。</p>

<h2 id="目次">目次</h2>

<ul class="table-of-contents">
    <li><a href="#はじめに">はじめに</a></li>
    <li><a href="#目次">目次</a></li>
    <li><a href="#CloudFormation-テンプレートのサンプル">CloudFormation テンプレートのサンプル</a></li>
    <li><a href="#参考">参考</a></li>
</ul>

<h2 id="CloudFormation-テンプレートのサンプル">CloudFormation テンプレートのサンプル</h2>

<p>Slack と <a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Chatbot の連携設定については割愛します。以下あたりのドキュメントに記載があります。</p>

<ul>
<li><a href="https://docs.aws.amazon.com/ja_jp/chatbot/latest/adminguide/slack-setup.html">Tutorial: Get started with Slack - AWS Chatbot</a></li>
</ul>


<p>以下のテンプレートで、<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Chatbotの定義のSlackの<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%EF%A1%BC%A5%AF%A5%B9%A5%DA%A1%BC%A5%B9">ワークスペース</a>とチャンネルIDを環境に合わせて設定し、 <code>us-east-1</code> リージョンにデプロイします。
<a class="keyword" href="https://d.hatena.ne.jp/keyword/AWS">AWS</a> Health <a class="keyword" href="https://d.hatena.ne.jp/keyword/Dashboard">Dashboard</a> のイベントを検知するために <code>us-east-1</code> にデプロイします。</p>

<pre class="code lang-yaml" data-lang="yaml" data-unlink><span class="synIdentifier">AWSTemplateFormatVersion</span><span class="synSpecial">:</span> <span class="synConstant">&quot;2010-09-09&quot;</span>
<span class="synIdentifier">Description</span><span class="synSpecial">:</span> SNS Topic and EventBridge for Health Dashboard

<span class="synIdentifier">Resources</span><span class="synSpecial">:</span>
 <span class="synComment"> # ----------------------------------------------------------------</span>
 <span class="synComment"> # SNS トピック の定義</span>
 <span class="synComment"> # ----------------------------------------------------------------</span>
  <span class="synIdentifier">HealthEventSlackNotifySNSTopic</span><span class="synSpecial">:</span>
    <span class="synIdentifier">Type</span><span class="synSpecial">:</span> AWS::SNS::Topic
    <span class="synIdentifier">Properties</span><span class="synSpecial">:</span>
      <span class="synIdentifier">TopicName</span><span class="synSpecial">:</span> <span class="synType">!Sub</span> ${AWS::StackName}-sns-topic

 <span class="synComment"> # ----------------------------------------------------------------</span>
 <span class="synComment"> # SNS トピックポリシーの定義</span>
 <span class="synComment"> # ----------------------------------------------------------------</span>
  <span class="synIdentifier">HealthEventSlackNotifySNSTopicPolicy</span><span class="synSpecial">:</span>
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
        <span class="synStatement">- </span><span class="synType">!Ref</span> HealthEventSlackNotifySNSTopic

 <span class="synComment"> # ----------------------------------------------------------------</span>
 <span class="synComment"> # EventBridge の定義</span>
 <span class="synComment"> # ----------------------------------------------------------------</span>
  <span class="synIdentifier">HealthEventSlackNotifyEventRule</span><span class="synSpecial">:</span>
    <span class="synIdentifier">Type</span><span class="synSpecial">:</span> AWS::Events::Rule
    <span class="synIdentifier">Properties</span><span class="synSpecial">:</span>
      <span class="synIdentifier">EventPattern</span><span class="synSpecial">:</span>
        <span class="synIdentifier">source</span><span class="synSpecial">:</span>
          <span class="synStatement">- </span>aws.health
      <span class="synIdentifier">Targets</span><span class="synSpecial">:</span>
        <span class="synStatement">- </span><span class="synIdentifier">Id</span><span class="synSpecial">:</span> HealthEventSlackNotifySNSTopic
          <span class="synIdentifier">Arn</span><span class="synSpecial">:</span> <span class="synType">!Ref</span> HealthEventSlackNotifySNSTopic

 <span class="synComment"> # ----------------------------------------------------------------</span>
 <span class="synComment"> # IAM ロールの定義</span>
 <span class="synComment"> # ----------------------------------------------------------------</span>
  <span class="synIdentifier">HealthEventSlackNotifyIAMRole</span><span class="synSpecial">:</span>
    <span class="synIdentifier">Type</span><span class="synSpecial">:</span> AWS::IAM::Role
    <span class="synIdentifier">Properties</span><span class="synSpecial">:</span>
      <span class="synIdentifier">RoleName</span><span class="synSpecial">:</span> <span class="synType">!Sub</span> ${AWS::StackName}-iam-role
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
 <span class="synComment"> # AWS ChatbotでのSlackチャンネルの定義</span>
 <span class="synComment"> # ----------------------------------------------------------------</span>
  <span class="synIdentifier">HealthEventSlackNotifySlackChannelConfiguration</span><span class="synSpecial">:</span>
    <span class="synIdentifier">Type</span><span class="synSpecial">:</span> AWS::Chatbot::SlackChannelConfiguration
    <span class="synIdentifier">DependsOn</span><span class="synSpecial">:</span>
      <span class="synStatement">- </span>HealthEventSlackNotifySNSTopic
      <span class="synStatement">- </span>HealthEventSlackNotifySNSTopicPolicy
    <span class="synIdentifier">Properties</span><span class="synSpecial">:</span> 
      <span class="synIdentifier">ConfigurationName</span><span class="synSpecial">:</span> <span class="synType">!Sub</span> ${AWS::StackName}-chatbot
      <span class="synIdentifier">IamRoleArn</span><span class="synSpecial">:</span> <span class="synType">!GetAtt</span> HealthEventSlackNotifyIAMRole.Arn
      <span class="synIdentifier">LoggingLevel</span><span class="synSpecial">:</span> ERROR
      <span class="synIdentifier">SlackChannelId</span><span class="synSpecial">:</span> <span class="synType">********</span> <span class="synComment"> # 通知したいSlack チャンネル IDを設定する</span>
      <span class="synIdentifier">SlackWorkspaceId</span><span class="synSpecial">:</span> <span class="synType">********</span> <span class="synComment"> # 通知したい Slack ワークスペース IDを設定する</span>
      <span class="synIdentifier">SnsTopicArns</span><span class="synSpecial">:</span> 
         <span class="synStatement">- </span><span class="synType">!Ref</span> HealthEventSlackNotifySNSTopic
</pre>


<h2 id="参考">参考</h2>

<ul>
<li><a href="https://docs.aws.amazon.com/ja_jp/chatbot/latest/adminguide/what-is.html">What is AWS Chatbot? - AWS Chatbot</a></li>
<li><a href="https://osm-japan.slack.com/intl/ja-jp/apps/A6L22LZNH-aws-chatbot">AWS Chatbot | Slack App &#x30C7;&#x30A3;&#x30EC;&#x30AF;&#x30C8;&#x30EA;</a></li>
</ul>


