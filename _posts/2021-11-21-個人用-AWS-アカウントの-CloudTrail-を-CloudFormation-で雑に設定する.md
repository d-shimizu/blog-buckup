---
layout: post
Categories:  ["tech"]
Description:  " CloudFormation で CloudTrail を設定する。 "
Tags: []
date: "2021-11-21T02:01:00+09:00"
title: "個人用 AWS アカウントの CloudTrail を CloudFormation で雑に設定する"
author: "dshimizu"
archive: ["2021"]
draft: "true"
---

<body>
<p>CloudFormation で CloudTrail を設定する。</p>
</body>

<!-- more -->

<body>
<h2>CloudFormation テンプレート</h2>

<p>とりあえず以下を実行すればモノは出来上がる。
※各種名前等は必要に応じて書き換える</p>

<pre class="code lang-yaml" data-lang="yaml" data-unlink> AWSTemplateFormatVersion: 2010-09-09
Description: &gt;
    Template CloudTrail

Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
    DeletionPolicy: Retain
    UpdateReplacePolicy: Retain
    Properties:
      BucketName: !Sub cloudtrail-logs-${AWS::Region}-${AWS::AccountId}
      AccessControl: LogDeliveryWrite
      BucketEncryption:
        ServerSideEncryptionConfiguration:
          - ServerSideEncryptionByDefault:
              SSEAlgorithm: AES256
      PublicAccessBlockConfiguration:
        BlockPublicAcls: true
        BlockPublicPolicy: true
        IgnorePublicAcls: true
        RestrictPublicBuckets: true
      VersioningConfiguration:
        Status: Enabled

  S3BucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket: !Ref S3Bucket
      PolicyDocument:
        Version: 2012-10-17
        Statement:
        - Sid: CloudTrailAclCheck
          Effect: Allow
          Principal:
            Service: cloudtrail.amazonaws.com
          Action: s3:GetBucketAcl
          Resource: !Sub arn:aws:s3:::${S3Bucket}
        - Sid: CloudTrailWrite
          Effect: Allow
          Principal:
            Service: cloudtrail.amazonaws.com
          Action: s3:PutObject
          Resource: !Sub arn:aws:s3:::${S3Bucket}/AWSLogs/*
          Condition:
            StringEquals:
              s3:x-amz-acl: bucket-owner-full-control

  # https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-kms-key.html
  # https://docs.aws.amazon.com/awscloudtrail/latest/userguide/create-kms-key-policy-for-cloudtrail.html
  KMSKey:
    Type: AWS::KMS::Key
    Properties:
      KeyPolicy:
        Version: 2012-10-17
        Id: !Sub ${AWS::StackId}-kms-key
        Statement:
          - Sid: "Allow CloudTrail to Encrypt Logs"
            Effect: Allow
            Principal:
              Service: cloudtrail.amazonaws.com
            Action: "kms:GenerateDataKey*"
            Resource: '*'
            Condition:
              StringLike:
                kms:EncryptionContext:aws:cloudtrail:arn:
                  - !Sub arn:aws:cloudtrail:*:${AWS::AccountId}:trail/*
          - Sid: "Enable Encrypted CloudTrail Log Access"
            Effect: Allow
            Principal:
              AWS: !Sub arn:aws:iam::${AWS::AccountId}:root
            Action: kms:*
            Resource: '*'
          - Sid: "Allow CloudTrail DescribeKey Access"
            Effect: Allow
            Principal:
              Service: cloudtrail.amazonaws.com
            Action: kms:DescribeKey
            Resource: '*'
            Condition:
              StringLike:
                kms:EncryptionContext:aws:cloudtrail:arn:
                  - !Sub arn:aws:cloudtrail:*:${AWS::AccountId}:trail/*
      Tags:
        - Key: Name
          Value: !Sub ${AWS::StackId}-kms
      KeyUsage: ENCRYPT_DECRYPT

  # https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-cloudtrail-trail.html
  CloudTrail:
    Type: AWS::CloudTrail::Trail
    Properties:
      S3BucketName: !Ref S3Bucket
      IsLogging: true
      TrailName: CloudTrailLogs
      EnableLogFileValidation: true
      IncludeGlobalServiceEvents: true
      IsMultiRegionTrail: true
      KMSKeyId: !Ref KMSKey
      Tags:
        - Key: Name
          Value: !Sub ${AWS::StackId}-cloudtrail
      EventSelectors:
        - DataResources:
            - Type: AWS::S3::Object
              Values:
                - !Sub "arn:${AWS::Partition}:s3:::"
          IncludeManagementEvents: true
          ReadWriteType: All
 </pre>


<h2>参考</h2>

<ul>
<li><a href="https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-cloudtrail-trail.html">AWS::CloudTrail::Trail - AWS CloudFormation</a></li>
<li><a href="https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-kms-key.html">AWS::KMS::Key - AWS CloudFormation</a></li>
<li><a href="https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-properties-s3-policy.html">AWS::S3::BucketPolicy - AWS CloudFormation</a></li>
<li><a href="https://docs.aws.amazon.com/ja_jp/awscloudtrail/latest/userguide/create-s3-bucket-policy-for-cloudtrail.html">CloudTrail の Amazon S3 バケットポリシー - AWS CloudTrail</a></li>
<li><a href="https://docs.aws.amazon.com/awscloudtrail/latest/userguide/create-kms-key-policy-for-cloudtrail.html">Configure AWS KMS key policies for CloudTrail - AWS CloudTrail</a></li>
</ul>

</body>
