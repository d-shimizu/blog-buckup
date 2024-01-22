---
layout: post
Categories:  ["tech"]
Description:  " CFnのテンプレートを1つにしておくと可読性が悪くなったりなどはよく言われているのでNested Stackを使って分割するサンプルを書いてみた。これをaws cliで扱うようにするとネストされたスタックのファイルが扱いやすくなる。 "
Tags: ["AWS", "CloudFormation", "tech"]
date: "2019-06-29T14:08:00+09:00"
title: "ネストされたCFnテンプレートをaws cliで扱う"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p>CFnのテンプレートを1つにしておくと可読性が悪くなったりなどはよく言われているのでNested Stackを使って分割するサンプルを書いてみた。
これを<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/cli">cli</a>で扱うようにするとネストされたスタックのファイルが扱いやすくなる。</p>
</body>

<!-- more -->

<body>
<h3>ネストされたスタック</h3>


<p>CloudFormationの最上位のテンプレート内で、別のCloudFormationテンプレートをリソースとして定義することができる、というもの。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-properties-stack.html">AWS::CloudFormation::Stack - AWS CloudFormation</a></li>
</ul>


<p>これによってテンプレートを分割できる。</p>

<h3>試す</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>の作成とEC2の作成を別テンプレートにして試す。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ構成は以下のようにしてみる。
直下の<code>./master.yml</code>の中に<code>./ec2/master.yml</code>と<code>./vpc/master.yml</code>を呼び出すように定義する。</p>

<pre class="terminal"> ./
┗ master.yml
  ec2/
  ┗ master.yml
  vpc/
  ┗ master.yml
 </pre>


<p>各<code>master.yml</code>の中身は以下のような感じにしている。</p>

<ul>
    <li><code>./master.yml</code></li>
</ul>
<pre class="terminal"> AWSTemplateFormatVersion: '2010-09-09'

Resources:
  vpcStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: ./vpc/master.yml
  EC2Stack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: ./ec2.yml </pre>



<p><code>TemplateURL</code>のところでネストするテンプレートを定義する。
通常この部分はアップロードする先のS3のURLを指定しなければならない。</p>

<ul>
    <li><code>./vpc/master.yml</code></li>
</ul>
<pre class="terminal"> AWSTemplateFormatVersion: 2010-09-09

Parameters:
  ProjectNameParameter:
    Type: String
    Default: 'sample-cfn'
    Description: Project name for tag

Resources:
  # VPC
  vpc1:
    Type: 'AWS::EC2::VPC'
    Properties:
      CidrBlock: 10.1.0.0/16
      InstanceTenancy: default
      EnableDnsSupport: 'true'
      EnableDnsHostnames: 'false'
      Tags:
        - Key: Name
          Value: !Sub "${ProjectNameParameter}-vpc"

  # Inernet Gateway
  igw1:
    Type: 'AWS::EC2::InternetGateway'
    Properties:
      Tags:
        - Key: Name
          Value: !Sub "${ProjectNameParameter}-igw"

  # DHCP Options
  dopt1:
    Type: 'AWS::EC2::DHCPOptions'
    Properties:
      Tags:
        - Key: Name
          Value: !Sub "${ProjectNameParameter}-dopt"
      DomainName: ap-northeast-1.compute.internal
      DomainNameServers:
        - AmazonProvidedDNS

  # RouteTable
  rt1:
    Type: 'AWS::EC2::RouteTable'
    Properties:
      VpcId: !Ref vpc1
      Tags:
        - Key: Name
          Value: !Sub "${ProjectNameParameter}-rt"

  # NetworkACL
  nacl1:
    Type: 'AWS::EC2::NetworkAcl'
    Properties:
      VpcId: !Ref vpc1

  # NetworkACL Entry
  acl1:
    Type: 'AWS::EC2::NetworkAclEntry'
    Properties:
      CidrBlock: 0.0.0.0/0
      Egress: 'true'
      Protocol: '-1'
      RuleAction: allow
      RuleNumber: '100'
      NetworkAclId: !Ref nacl1
  acl2:
    Type: 'AWS::EC2::NetworkAclEntry'
    Properties:
      CidrBlock: 0.0.0.0/0
      Protocol: '-1'
      RuleAction: allow
      RuleNumber: '100'
      NetworkAclId: !Ref nacl1

  # InternetGateway
  gw1:
    Type: 'AWS::EC2::VPCGatewayAttachment'
    Properties:
      VpcId: !Ref vpc1
      InternetGatewayId: !Ref igw1

  # RouteTable
  route1:
    Type: 'AWS::EC2::Route'
    Properties:
      DestinationCidrBlock: 0.0.0.0/0
      RouteTableId: !Ref rt1
      GatewayId: !Ref igw1
    DependsOn: gw1

  # DHCP Options Association
  dchpassoc1:
    Type: 'AWS::EC2::VPCDHCPOptionsAssociation'
    Properties:
      VpcId: !Ref vpc1
      DhcpOptionsId: !Ref dopt1

  # Subnet
  subnet1:
    Type: 'AWS::EC2::Subnet'
    Properties:
      CidrBlock: 10.1.1.0/24
      AvailabilityZone: ap-northeast-1a
      VpcId: !Ref vpc1
      Tags:
        - Key: Name
          Value: !Sub "${ProjectNameParameter}-web-public-1a-subnet"
  mySubnetRouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId: !Ref subnet1
      RouteTableId: !Ref rt1
  subnetacl1:
    Type: 'AWS::EC2::SubnetNetworkAclAssociation'
    Properties:
      SubnetId: !Ref subnet1
      NetworkAclId: !Ref nacl1

### Outputs
Outputs:
  VpcId:
    Value: !Ref vpc1
    Export:
      Name: !Sub "${ProjectNameParameter}-vpc"
  RouteTableId:
    Value: !Ref rt1
    Export:
      Name: !Sub "${ProjectNameParameter}-rt"
  NetworkAclId:
    Value: !Ref nacl1
    Export:
      Name: !Sub "${ProjectNameParameter}-nacl"
  SubnetId:
    Value: !Ref subnet1
    Export:
      Name: !Sub "${ProjectNameParameter}-subnet" </pre>



<ul>
    <li><code>./ec2/master.yml</code></li>
</ul>
<pre class="terminal"> AWSTemplateFormatVersion: 2010-09-09
Parameters:
  ProjectNameParameter:
    Type: String
    Default: 'sample-cfn'
    Description: Project name for tag
Resources:
  EC2:
    Type: 'AWS::EC2::Instance'
    Properties:
      DisableApiTermination: 'false'
      InstanceInitiatedShutdownBehavior: stop
      ImageId: ami-00f9d04b3b3092052
      InstanceType: t2.micro
      KeyName: ec2-key-pair-apn1
      Monitoring: 'false'
      Tags:
        - Key: Name
          Value: !Sub "${ProjectNameParameter}-ec2"
      NetworkInterfaces:
        - DeleteOnTermination: 'true'
          Description: Primary network interface
          DeviceIndex: 0
          SubnetId:
            "Fn::ImportValue": !Sub "${ProjectNameParameter}-subnet"
          PrivateIpAddresses:
            - PrivateIpAddress: 10.1.1.45
              Primary: 'true'
          GroupSet:
            - !Ref sg1
          AssociatePublicIpAddress: 'true'
  sg1:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: 'ds-common-ec2-bastion01-sg created 2018-11-15T15:18:31.279+09:00'
      VpcId:
        "Fn::ImportValue": !Sub "${ProjectNameParameter}-vpc"
  ingress1:
    Type: 'AWS::EC2::SecurityGroupIngress'
    Properties:
      GroupId: !Ref sg1
      IpProtocol: tcp
      FromPort: '22'
      ToPort: '22'
      CidrIp: 0.0.0.0/0
  egress1:
    Type: 'AWS::EC2::SecurityGroupEgress'
    Properties:
      GroupId: !Ref sg1
      IpProtocol: '-1'
      CidrIp: 0.0.0.0/0
Description: Create EC2 Template
 </pre>



<h3>まとめ</h3>


<p>ネストされたスタックを<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/cli">cli</a>で扱うやり方について書いた。親テンプレートに定義する<code>TemplateURL</code>は本来、子テンプレートが置いてあるs3の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%B1%A5%C3%A5%C8">バケット</a>URLを指定する必要があるが、ローカルの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを指定して<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/cli">cli</a>を使ってパッケージングすると、子テンプレートをS3にアップロードした上で<code>TemplateURL</code>をs3のURLに書き換えてくれるのでその点は便利だと思う。
ただ、テンプレートが肥大化すると結局変数の受け渡しが煩雑になる気はする。</p>
</body>
