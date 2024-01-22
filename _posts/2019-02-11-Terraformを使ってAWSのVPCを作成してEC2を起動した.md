---
layout: post
Categories:  ["tech"]
Description:  " AWSの環境を構築する場合に利用するツールとしてはAWS公式のCloudFormationがある。だが、CloudFormation以外ではTerraformが有力な選択肢の1つになると思う。  若干必要に迫られてTerraformを触る"
Tags: ["AWS", "tech", "Terraform"]
date: "2019-02-11T00:02:00+09:00"
title: "Terraformを使ってAWSのVPCを作成してEC2を起動した"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の環境を構築する場合に利用するツールとしては<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>公式のCloudFormationがある。
だが、CloudFormation以外ではTerraformが有力な選択肢の1つになると思う。</p>

<p>若干必要に迫られてTerraformを触ることになったので、自分で試してみたことや調べたことをまとめておく。</p>
</body>

<!-- more -->

<body>
<h3>Terraformとは</h3>


<p>Hashcorp社が開発している<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AA%A1%BC%A5%D7%A5%F3%A5%BD%A1%BC%A5%B9">オープンソース</a>のソフトウェアで、ドキュメントでは「Terraformは、インフラスト<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%E9%A5%AF">ラク</a>チャを安全かつ効率的に構築、変更、およびバージョン管理するためのツールです。」と記載されている。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/intro/index.html#what-is-terraform-">Introduction - Terraform by HashiCorp</a></li>
</ul>


<p>開発は<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>上で行われている。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/hashicorp/terraform">GitHub - hashicorp/terraform: Terraform is a tool for building, changing, and combining infrastructure safely ...</a></li>
</ul>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Amazon%20Web%20Services">Amazon Web Services</a>（以下、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>）だけでなく、<a class="keyword" href="http://d.hatena.ne.jp/keyword/GCP">GCP</a>やAzure、Heroku、OpenStackなどの非常に多くのインフラに対応している。
プロバイダというのが、Terraform上でどの環境を利用するか定義するもの。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/docs/providers/">Providers - Terraform by HashiCorp</a></li>
</ul>


<p>CloudFormationとどちらが良いのかは環境によりけりだと思うが、Terraformの公式ドキュメントには、複数の環境を組み合わせる場合や、事前の実行計画を確認できるところが利点であることが挙げられている。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/intro/vs/cloudformation.html">Terraform vs. CloudFormation, Heat, etc. - Terraform by HashiCorp</a></li>
</ul>


<h4>Terraformのセットアップ</h4>


<p>Terraformを使うには、まずバイナリファイルをダウンロードしてそれを解凍し、できたファイルをPATHの通った場所に配置するだけ。</p>

<pre class="terminal"> $ wget -O /tmp/terraform_0.11.11_linux_amd64.zip https://releases.hashicorp.com/terraform/0.11.11/terraform_0.11.11_linux_amd64.zip
 </pre>


<pre class="terminal"> $ unzip /tmp/terraform_0.11.11_linux_amd64.zip -d /usr/local/bin
 </pre>


<pre class="terminal"> $ terraform -v
Terraform v0.11.11
 </pre>


<p>これでTerraformが使えるようになった。</p>

<h4>Terraformで<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>とEC2作成</h4>


<p>早速<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の環境を構築してみる。</p>

<h5>作業<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リの作成</h5>


<p>まずTerraformを実行するためのテンプレートファイルを管理する<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成する。</p>

<pre class="terminal"> $ mkdir terraform-example
 </pre>


<p>テンプレートはHCL(HashiCorp Configuration Language)というHashiCorp製の独自の<a class="keyword" href="http://d.hatena.ne.jp/keyword/DSL">DSL</a>で記述する。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/hashicorp/hcl">GitHub - hashicorp/hcl: HCL is the HashiCorp configuration language.</a></li>
</ul>


<p>上記のドキュメントにもあるが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/JSON">JSON</a>互換となっている。</p>

<h5>テンプレートファイルの作成</h5>


<p>テンプレートファイルを作成する。拡張子は<code>.tf</code>となり、Terraformは自動でこの拡張子のものを読み込んでくれる。
なので、名前はなんでも良いがここでは<code>main.tf</code>としている。</p>

<pre class="terminal"> $ vim main.tf
 </pre>


<p>別途変数の切り出しとかもできるが、ここでは実施せず、<code>main.tf</code>ファイル1つで管理する。</p>

<h5>プロバイダーの設定</h5>


<p>最初に<code>provider</code>という指定をする必要がある。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/docs/providers/">Providers - Terraform by HashiCorp</a></li>
</ul>


<p>複数の環境に対応しているため、「どのプロバイダーを使うのか？」を宣言する。
<code>main.tf</code>に以下のように記述する。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/access">access</a>_key/secret_keyは<code>$HOME/.aws/credentials</code>に記載しておいても良い。
<code>shared_credentials_file</code>で<code>credentials</code>ファイルの場所を指定することもできる</p>

<pre class="terminal"> provider "aws" {
  access_key = "anaccesskey"
  secret_key = "asecretkey"
  #shared_credentials_file = "~/.aws/credentials"
  region = "ap-northeast-1"
  profile = "default"
}
 </pre>


<h5>リソースの設定</h5>


<p>Terraformでのリソースとはインフラの構成要素を指す。
利用するリソースを<code>resource</code>ブロックで設定する。<code>resource "リソースタイプ(最初のパラメータ)" "リソース名(2番目のパラメータ)" {}</code>という構文となる。
タイプと名前の組み合わせは一意である必要がある。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/docs/configuration/resources.html">Configuring Resources - Terraform by HashiCorp</a></li>
</ul>


<p>リソースタイプは、プロバイダーが<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の場合は<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a>_*という名前でTerraformで予め定義されている。<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>であれば<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a>_<a class="keyword" href="http://d.hatena.ne.jp/keyword/vpc">vpc</a>、EC2であれば<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a>_instanceとなる。
その他の<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>で利用可能なリソースはドキュメントを参照のこと。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/docs/providers/aws/index.html">Provider: AWS - Terraform by HashiCorp</a></li>
</ul>


<p>リソース名は任意の名前を設定可能。わかりやすい名前を付ければ良い。</p>

<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>の作成</h5>


<p><code>main.tf</code>に以下を追記する。
リソースタイプを<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a>_<a class="keyword" href="http://d.hatena.ne.jp/keyword/vpc">vpc</a>とする。リソース名はterraform_example_<a class="keyword" href="http://d.hatena.ne.jp/keyword/vpc">vpc</a>としている。
ブロックの中にcidr_blockやTagなどのパラメータを記述する。</p>

<pre class="terminal"> # VPC
resource "aws_vpc" "this" {
  cidr_block = "10.1.0.0/16"
  instance_tenancy = "default"
  enable_dns_support = "true"
  enable_dns_hostnames = "true"
  tags {
    Name = "tf-example-vpc"
  }
}
 </pre>


<h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>に関連するネットワークリソースの作成</h5>


<p><code>main.tf</code>に以下を追記する。
InternetGateway, RouteTable, Subnet, およびSubnetへのRouteTableの割り当てを行う。
他のリソースタイプの値を参照したい場合は、<code>vpc_id = "${aws_vpc.example_vpc.id}"</code>のように、&lt;リソースタイプ&gt;.&lt;リソース名&gt;.&lt;属性名&gt;と指定する。</p>

<pre class="terminal"> # InternetGateway
resource "aws_internet_gateway" "this" {
  vpc_id = "${aws_vpc.this.id}"
}

# RouteTable
resource "aws_route_table" "public" {
  vpc_id = "${aws_vpc.this.id}"
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = "${aws_internet_gateway.this.id}"
  }
  tags {
    Name = "public"
  }
}

# Subnet
resource "aws_subnet" "public_a" {
  vpc_id = "${aws_vpc.this.id}"
  cidr_block = "10.1.1.0/24"
  availability_zone = "ap-northeast-1a"
  tags {
    Name = "public-a"
  }
}

resource "aws_subnet" "public_c" {
  vpc_id = "${aws_vpc.this.id}"
  cidr_block = "10.1.2.0/24"
  availability_zone = "ap-northeast-1c"
  tags {
    Name = "public-c"
  }
}

resource "aws_subnet" "public_d" {
  vpc_id = "${aws_vpc.this.id}"
  cidr_block = "10.1.3.0/24"
  availability_zone = "ap-northeast-1d"
  tags {
    Name = "public-d"
  }
}

# SubnetRouteTableAssociation
resource "aws_route_table_association" "public_a" {
    subnet_id = "${aws_subnet.public_a.id}"
    route_table_id = "${aws_route_table.public.id}"
}

resource "aws_route_table_association" "public_c" {
    subnet_id = "${aws_subnet.public_c.id}"
    route_table_id = "${aws_route_table.public.id}"
}
 </pre>


<h5>EC2の作成</h5>


<p><code>main.tf</code>に以下を追記する。
<code>aws_security_group</code>, <code>aws_instance</code>のリソースタイプを使ってセキュリティグループ、EC2を作成する。
必要なネットワーク情報は上記のリソースタイプを参照するように記述する。</p>

<pre class="terminal"> # Security Group
resource "aws_security_group" "this" {
    name = "APP_SG"
    vpc_id = "${aws_vpc.this.id}"
    ingress {
        from_port = 22
        to_port = 22
        protocol = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
    }
    egress {
        from_port = 0
        to_port = 0
        protocol = "-1"
        cidr_blocks = ["0.0.0.0/0"]
    }
    description = "tf-example-sg"
}

# EC2
resource "aws_instance" "this_t2" {
    ami = "ami-2a69be4c"
    instance_type = "t2.micro"
  disable_api_termination = false
  key_name                = "aws-key-pair"
  vpc_security_group_ids  = ["${aws_security_group.this.id}"]
  subnet_id               = "${aws_subnet.public_a.id}"

  tags {
    Name = "tf-example-ec2"
  }
}

# ElasticIP
resource "aws_eip" "this" {
    instance = "${aws_instance.this_t2.id}"
    vpc = true
}
 </pre>


<h4>実行</h4>


<h5>初期化</h5>


<p><code>terraform init</code>で作業<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを初期化する。初期化といっても作成した<code>.tf</code>が消えることはない。利用するterraform<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>(ここでは<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>)の初期化したり設定内容とバージョンの互換性をチェックしてくれる模様。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/docs/commands/init.html">Command: init - Terraform by HashiCorp</a></li>
</ul>


<pre class="terminal"> $ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.aws: version = "~&gt; 1.57"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
 </pre>


<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/docs/commands/plan.html">Command: plan - Terraform by HashiCorp</a></li>
</ul>


<h5>実行計画の作成</h5>


<p><code>terraform plan</code>実行計画を作成する。
際のリソースや状態を変更することなく、一連の変更の実行計画がユーザーの予想と一致するかどうかを確認できる。</p>

<pre class="terminal"> $ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

    :
    :
    :

Plan: 11 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
 </pre>


<h5>実行</h5>


<p><code>terraform apply</code>でテンプレートを実行してリソースを生成する。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/docs/commands/apply.html">Command: apply - Terraform by HashiCorp</a></li>
</ul>


<pre class="terminal"> $ terraform apply

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

    :
    :
    :

Apply complete! Resources: 11 added, 0 changed, 0 destroyed.
 </pre>


<h5>削除</h5>


<p><code>terraform apply</code>でテンプレートで生成されたリソースを削除する。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/docs/commands/destroy.html">Command: destroy - Terraform by HashiCorp</a></li>
</ul>


<pre class="terminal"> $ terraform destroy
aws_vpc.terraform_example_vpc: Refreshing state... (ID: vpc-084056faf393b50f1)
aws_subnet.terraform_example_subnet_public-c: Refreshing state... (ID: subnet-04568b62453decefa)
aws_security_group.terraform_example_sg: Refreshing state... (ID: sg-0e1dbb4daad93fc80)
aws_subnet.terraform_example_subnet_public-a: Refreshing state... (ID: subnet-0d1c7eed2ead4e307)
aws_subnet.terraform_example_subnet_public-d: Refreshing state... (ID: subnet-08a80915ac04d7b90)
aws_internet_gateway.terraform_example_igw: Refreshing state... (ID: igw-031a1b3bb9b08caaf)
aws_route_table.terraform_example_public_rt: Refreshing state... (ID: rtb-0f6bf85f04c90ecef)
aws_instance.terraform_example_ec2: Refreshing state... (ID: i-052a7a262f0fec3e1)
aws_route_table_association.terraform_example_rt_public-a: Refreshing state... (ID: rtbassoc-0738393cba67fe3bc)
aws_route_table_association.terraform_example_rt_public-c: Refreshing state... (ID: rtbassoc-062a44d929c881bcb)
aws_eip.terraform_example_eip: Refreshing state... (ID: eipalloc-0912a5f0e5dc2ec34)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
- destroy

Terraform will perform the following actions:

- aws_eip.terraform_example_eip

- aws_instance.terraform_example_ec2

- aws_internet_gateway.terraform_example_igw

- aws_route_table.terraform_example_public_rt

- aws_route_table_association.terraform_example_rt_public-a

- aws_route_table_association.terraform_example_rt_public-c

- aws_security_group.terraform_example_sg

- aws_subnet.terraform_example_subnet_public-a

- aws_subnet.terraform_example_subnet_public-c

- aws_subnet.terraform_example_subnet_public-d

- aws_vpc.terraform_example_vpc

Plan: 0 to add, 0 to change, 11 to destroy.

Do you really want to destroy all resources?
Terraform will destroy all your managed infrastructure, as shown above.
There is no undo. Only 'yes' will be accepted to confirm.

Enter a value: yes

aws_eip.terraform_example_eip: Destroying... (ID: eipalloc-0912a5f0e5dc2ec34)
aws_subnet.terraform_example_subnet_public-d: Destroying... (ID: subnet-08a80915ac04d7b90)
aws_route_table_association.terraform_example_rt_public-c: Destroying... (ID: rtbassoc-062a44d929c881bcb)
aws_route_table_association.terraform_example_rt_public-a: Destroying... (ID: rtbassoc-0738393cba67fe3bc)
aws_route_table_association.terraform_example_rt_public-a: Destruction complete after 0s
aws_route_table_association.terraform_example_rt_public-c: Destruction complete after 0s
aws_subnet.terraform_example_subnet_public-c: Destroying... (ID: subnet-04568b62453decefa)
aws_route_table.terraform_example_public_rt: Destroying... (ID: rtb-0f6bf85f04c90ecef)
aws_subnet.terraform_example_subnet_public-d: Destruction complete after 1s
aws_route_table.terraform_example_public_rt: Destruction complete after 1s
aws_internet_gateway.terraform_example_igw: Destroying... (ID: igw-031a1b3bb9b08caaf)
aws_subnet.terraform_example_subnet_public-c: Destruction complete after 1s
aws_eip.terraform_example_eip: Destruction complete after 1s
aws_instance.terraform_example_ec2: Destroying... (ID: i-052a7a262f0fec3e1)
aws_internet_gateway.terraform_example_igw: Still destroying... (ID: igw-031a1b3bb9b08caaf, 10s elapsed)
aws_instance.terraform_example_ec2: Still destroying... (ID: i-052a7a262f0fec3e1, 10s elapsed)
aws_internet_gateway.terraform_example_igw: Destruction complete after 10s
aws_instance.terraform_example_ec2: Still destroying... (ID: i-052a7a262f0fec3e1, 20s elapsed)
aws_instance.terraform_example_ec2: Still destroying... (ID: i-052a7a262f0fec3e1, 30s elapsed)
aws_instance.terraform_example_ec2: Still destroying... (ID: i-052a7a262f0fec3e1, 40s elapsed)
aws_instance.terraform_example_ec2: Still destroying... (ID: i-052a7a262f0fec3e1, 50s elapsed)
aws_instance.terraform_example_ec2: Still destroying... (ID: i-052a7a262f0fec3e1, 1m0s elapsed)
aws_instance.terraform_example_ec2: Destruction complete after 1m1s
aws_subnet.terraform_example_subnet_public-a: Destroying... (ID: subnet-0d1c7eed2ead4e307)
aws_security_group.terraform_example_sg: Destroying... (ID: sg-0e1dbb4daad93fc80)
aws_subnet.terraform_example_subnet_public-a: Destruction complete after 1s
aws_security_group.terraform_example_sg: Destruction complete after 1s
aws_vpc.terraform_example_vpc: Destroying... (ID: vpc-084056faf393b50f1)
aws_vpc.terraform_example_vpc: Destruction complete after 0s

Destroy complete! Resources: 11 destroyed.
 </pre>


<h3>まとめ</h3>


<p>Terraformのセットアップと<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>/EC2作成用テンプレートを作成して削除するまでをやってみた。
慣れればCloudFormationより使いやすいかもしれない。</p>

<p>サンプルコードを<a class="keyword" href="http://d.hatena.ne.jp/keyword/Github">Github</a>に挙げておいた。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/d-shimizu/terraform-sample">GitHub - d-shimizu/terraform-sample</a></li>
</ul>


<h3>参考</h3>


<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/">Terraform by HashiCorp</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/hashicorp/terraform">GitHub - hashicorp/terraform: Terraform is a tool for building, changing, and combining infrastructure safely ...</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/hashicorp/hcl">GitHub - hashicorp/hcl: HCL is the HashiCorp configuration language.</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://thinkit.co.jp/story/2015/07/14/6212">インフラの構成管理を自動化するTerraform入門 | Think IT（シンクイット）</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://dev.classmethod.jp/cloud/terraform-getting-started-with-aws/">AWSでTerraformに入門 | DevelopersIO</a></li>
</ul>

</body>
