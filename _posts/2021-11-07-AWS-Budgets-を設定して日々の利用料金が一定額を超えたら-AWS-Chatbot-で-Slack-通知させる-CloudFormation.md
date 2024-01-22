---
layout: post
Categories:  ["tech"]
Description:  " AWS Budgets を設定して、料金が一定額を超えたら AWS Chatbot で Slack へ通知させるような設定ができる CloudFormation テンプレートを作った。 "
Tags: []
date: "2021-11-07T12:47:00+09:00"
title: "AWS Budgets を設定して日々の利用料金が一定額を超えたら AWS Chatbot で Slack 通知させる CloudFormation"
author: "dshimizu"
archive: ["2021"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Budgets を設定して、料金が一定額を超えたら <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Chatbot で Slack へ通知させるような設定ができる CloudFormation テンプレートを作った。</p>
</body>

<!-- more -->

<body>
<h2>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Budgets とは</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> のコスト管理サービスの1つで、日別や月別などで<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の利用料を予算として定義する事ができ、それによって可視化が可能となり、また、設定した予算に対して<a class="keyword" href="http://d.hatena.ne.jp/keyword/%EF%E7%C3%CD">閾値</a>を設定することができ、それを超えるとアラート通知を行うことができる。(と言った感じの理解。詳細はドキュメントを参照のこと)</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fdocs.aws.amazon.com%2Fja_jp%2Fcost-management%2Flatest%2Fuserguide%2Fbudgets-managing-costs.html" title="AWS Budgets を用いてコストを管理する - AWS コスト管理" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;"></iframe><cite class="hatena-citation"><a href="https://docs.aws.amazon.com/ja_jp/cost-management/latest/userguide/budgets-managing-costs.html">docs.aws.amazon.com</a></cite></p>

<h2>設定</h2>

<h3>Slack と <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Chatbot の連携</h3>

<p>Chatbot で Slacke WorkSpace の連携をしていない場合は、マネジメントコンソールで <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Chatbot の画面から Slack WorkSpace の連携を行う。</p>

<p>マネジメントコンソールで <a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Chatbot の画面から、画面右の「チャットクライアントを設定」で 「Slack」 を選択して、「クライアントを設定」ボタンをクリックする。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220403/20220403122044.png" alt="f:id:dshimizu:20220403122044p:plain" width="1200" height="507" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<p>認証画面が出てくるので、連携したい<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EF%A1%BC%A5%AF%A5%B9%A5%DA%A1%BC%A5%B9">ワークスペース</a>にログインし、連携する。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220403/20220403123107.png" alt="f:id:dshimizu:20220403123107p:plain" width="1200" height="560" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Budgets、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon">Amazon</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/SNS">SNS</a>、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Chatbot の設定</h3>

<p>手動での設定手順は探せば出てくるので、とりあえず CloudFormation テンプレートを作った。
Slack の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EF%A1%BC%A5%AF%A5%B9%A5%DA%A1%BC%A5%B9">ワークスペース</a>IDとチャンネルIDは、Slackの画面等から確認してセットする必要がある。</p>

<p>下記は毎日の利用料を $10 として、それに対して実際の利用料が 120% を超えるとアラート通知する、というような設定になっている。</p>

<pre class="code lang-yaml" data-lang="yaml" data-unlink> AWSTemplateFormatVersion: 2010-09-09
Description: &gt;
    Template AWS Budgets, Amazon SNS, AWS Chatbot

Resources:
  # ----------------------------------------------------------------
  # SNS Topic and Topic Policy
  # ----------------------------------------------------------------
  SNSTopicForAWSChatbot:
    Type: AWS::SNS::Topic
    Properties: 
      TopicName: !Sub ${AWS::StackName}-sns-topic

  SNSTopicPolicyForEventBridgeToSNSTopicForAWSChatbot:
    Type: AWS::SNS::TopicPolicy
    DependsOn: SNSTopicForAWSChatbot
    Properties: 
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Sid: SNSTopicPolicyForEventBridgeToSNSTopicForAWSChatbot
            Effect: Allow
            Principal:
              Service: events.amazonaws.com
            Action:
              - SNS:Publish
            Resource: !Ref SNSTopicForAWSChatbot
      Topics: 
        - !Ref SNSTopicForAWSChatbot

  # ----------------------------------------------------------------
  # IAM Role For AWS Chatbot
  # ----------------------------------------------------------------
  IAMRoleForAWSChatbot:
    Type: AWS::IAM::Role
    Properties:
      RoleName: !Sub ${AWS::StackName}-chatbot-iam-role
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              Service: chatbot.amazonaws.com
            Action: sts:AssumeRole
      Policies:
        - PolicyName: !Sub ${AWS::StackName}-chatbot-iam-policy
          PolicyDocument:
            Version: 2012-10-17
            Statement:
              - Effect: Allow
                Action:
                  - cloudwatch:Describe*
                  - cloudwatch:Get*
                  - cloudwatch:List*
                Resource:
                  - "*"

  # ----------------------------------------------------------------
  # Chatbot Slack Channel
  # ----------------------------------------------------------------
  ChatbotSlackChannelConfiguration:
    Type: AWS::Chatbot::SlackChannelConfiguration
    DependsOn:
      - IAMRoleForAWSChatbot
      - SNSTopicForAWSChatbot
    Properties: 
      ConfigurationName: !Sub ${AWS::StackName}-chatbot
      IamRoleArn: !GetAtt IAMRoleForAWSChatbot.Arn
      LoggingLevel: ERROR
      SlackChannelId: **********  # 連携したいチャンネルのID
      SlackWorkspaceId: ********   # Slack ワークスペースのID 
      SnsTopicArns: 
        - !Ref SNSTopicForAWSChatbot

  # ----------------------------------------------------------------
  # AWS Budgets
  # ----------------------------------------------------------------
  DailyBudgets:
    Type: AWS::Budgets::Budget
    DependsOn: SNSTopicForAWSChatbot
    Properties: 
      Budget:
        BudgetLimit: # 予算設定
          Amount: 10  # 毎日 $10 ドルを予算として設定
          Unit: USD
        BudgetType: COST
        BudgetName: !Sub ${AWS::StackName}-daily
        TimeUnit: DAILY
      NotificationsWithSubscribers:   # 通知設定
        - Notification:
            NotificationType: ACTUAL
            ComparisonOperator: GREATER_THAN
            Threshold: 120  # 予算として設定した値(ここでは $100 )の超過率が 120% を超えたら SNS へ通知(Chatbot から Slack へ通知)
            ThresholdType: PERCENTAGE
          Subscribers:
          - SubscriptionType: SNS
            Address:
              !Ref SNSTopicForAWSChatbot
 </pre>

</body>
