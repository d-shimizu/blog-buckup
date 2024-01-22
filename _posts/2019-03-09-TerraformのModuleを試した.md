---
layout: post
Categories:  ["tech"]
Description:  " TerraformでModuleを使ってみた。       Modules - Configuration Language - Terraform by HashiCorp   ドキュメントによるとTerraform構成にはルートモジュ"
Tags: ["tech", "Terraform"]
date: "2019-03-09T19:05:00+09:00"
title: "TerraformのModuleを試した"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p>TerraformでModuleを使ってみた。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/docs/configuration/modules.html">Modules - Configuration Language - Terraform by HashiCorp</a></li>
</ul>


<p>ドキュメントによるとTerraform構成にはルートモジュールと呼ばれるモジュールが存在し、メイン作業<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ内の<code>.tf</code>ファイルはすべてルートモジュールになるとのこと。
リソースを別なモジュールに分割し、子モジュールとすることでリソースを簡潔に定義できたり、複数回呼び出して再利用性を高めることができるらしい。</p>
</body>

<!-- more -->

<body>
<h3>Moduleを試す</h3>


<p>今回は<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>を作成し、そこにEC2を1台起動するTerraformをmoduleを使ってやってみる。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ構成は下記としてみる。</p>

<p>terraform(作業<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ)以下にdevelopment<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リとmodules<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成する。</p>

<p>development<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リがルートモジュールの設置場所となる。ここに<code>main.tf</code>,<code>variables.tf</code>,<code>config.tfvars</code>を設置する。
development<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ配下で<code>terraform apply</code>を実行すると、modules配下の<code>*.tf</code>ファイルが読み込まれて実行される。</p>

<p>modules<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ配下には ec2, <a class="keyword" href="http://d.hatena.ne.jp/keyword/vpc">vpc</a> といったリソース単位の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成し、それぞれの配下に<code>main.tf</code>ファイルを設置する。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/vpc">vpc</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ配下には<code>output.tf</code>というファイルを設置する。これはec2リソースの<code>main.tf</code>から<a class="keyword" href="http://d.hatena.ne.jp/keyword/vpc">vpc</a>リソース設定情報を読み込むための情報を出力するためのもの。</p>

<pre class="terminal"> terraform/
├── modules/
|     ├── ec2/
|     |     └── main.tf
|     └── vpc/
|            ├── main.tf
|            └── output.tf
|
└── development/
       ├── main.tf
       ├── variables.tf
       └── config.tfvars
 </pre>


<h4>development以下の構成</h4>


<p><code>development/main.tf</code>を下記とする。</p>

<pre class="terminal"> provider "aws" {
  region  = "${var.region}"
  profile = "${var.profile}"
}

# VPC
module "module_vpc" {
  source = "../modules/vpc"
}

# EC2
module "module_ec2" {
  source = "../modules/ec2"

  vpc_id = "${module.module_vpc.vpc_id}"
  subnet_public_a_id = "${module.module_vpc.subnet_public_a_id}"
  subnet_public_c_id = "${module.module_vpc.subnet_public_c_id}"
}
 </pre>


<p><code>module</code>ブロックは<code>module "module_name" {}</code>の形で定義する。"module_name"の部分はterraform内で使うローカルの名前であり、好きな名前を設定する。</p>

<p>ここではモジュールの中で<code>../module/vpc</code>と<code>../module/ec2</code>を呼び出せるように指定をしている。
これはルートモジュールが存在する<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C1%EA%C2%D0%A5%D1%A5%B9">相対パス</a>となる。</p>

<p>"module_ec2"では"module_<a class="keyword" href="http://d.hatena.ne.jp/keyword/vpc">vpc</a>"で作成した<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/vpc">vpc</a> idやサブネットIDを利用するため、これらの値を<code>../modules/vpc/output.tf</code>から取得する。
code&gt;../modules/<a class="keyword" href="http://d.hatena.ne.jp/keyword/vpc">vpc</a>/output.tfで出力したい情報を<code>output output_name {}</code>の形で定義し、<code>development/main.tf</code>の"module_ec2"ブロックの中で"${module.module_name.output_name}"のような形で変数として定義して参照する。</p>

<p>provider情報は下記のように切り出した。</p>

<ul>
    <li><code>development/variables.tf</code></li>
</ul>
<pre class="terminal"> variable "region" {}
variable "profile" {}
 </pre>
    <li><code>development/config.tfvars</code></li>
<pre class="terminal"> region = "ap-northeast-1"
profile = "default"
 </pre>



<h4>modules以下の構成</h4>


<h5>modules/<a class="keyword" href="http://d.hatena.ne.jp/keyword/vpc">vpc</a>/以下の構成</h5>


<p><code>modules/vpc/main.tf</code>に<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>など必要なリソースを作成するように定義する。</p>

<ul>
    <li><code>modules/vpc/main.tf</code></li>
</ul>
<pre class="terminal"> # VPC
resource "aws_vpc" "this" {
  cidr_block           = "10.1.0.0/16"
  instance_tenancy     = "default"
  enable_dns_support   = "true"
  enable_dns_hostnames = "true"

  tags {
    Name = "tf-sample-vpc"
  }
}

# InternetGateway
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
    Name = "tf-sample-public-rt"
  }
}

# Subnet
resource "aws_subnet" "public_a" {
  vpc_id            = "${aws_vpc.this.id}"
  cidr_block        = "10.1.1.0/24"
  availability_zone = "ap-northeast-1a"

  tags {
    Name = "tf-sample-public-a-subnet"
  }
}

resource "aws_subnet" "public_c" {
  vpc_id            = "${aws_vpc.this.id}"
  cidr_block        = "10.1.2.0/24"
  availability_zone = "ap-northeast-1c"

  tags {
    Name = "tf-sample-public-c-subnet"
  }
}

resource "aws_subnet" "public_d" {
  vpc_id            = "${aws_vpc.this.id}"
  cidr_block        = "10.1.3.0/24"
  availability_zone = "ap-northeast-1d"

  tags {
    Name = "tf-sample-public-d-subnet"
  }
}

# SubnetRouteTableAssociation
resource "aws_route_table_association" "public_a" {
  subnet_id      = "${aws_subnet.public_a.id}"
  route_table_id = "${aws_route_table.public.id}"
}

resource "aws_route_table_association" "public_c" {
  subnet_id      = "${aws_subnet.public_c.id}"
  route_table_id = "${aws_route_table.public.id}"
}
 </pre>



<p><code>modules/vpc/output.tf</code>で他のmoduleから参照したい情報を<code>output output_name {}</code>の形で定義する。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/value">value</a>変数は、<code>modules/vpc/mian.tf</code>の中の取得したい情報を<code>${resource_type.resource_name.attributes}</code>の形式として指定すれば格納される。</p>

<p>今回のケースでは、<code>modules/ec2/</code>で、<code>modules/vpc/main.tf</code>の<code>resource "aws_vpc" "this" {}</code>で作成した<a class="keyword" href="http://d.hatena.ne.jp/keyword/vpc">vpc</a>のid、<code>resource "aws_route_table_association" "public_c"</code>,<code>resource "aws_route_table_association" "public_a"</code>で作成したサブネットIDを取得して利用したいので、<code>output.tf</code>で以下のような形式で指定する。</p>

<ul>
    <li><code>modules/vpc/output.tf</code></li>
</ul>
<pre class="terminal"> output "vpc_id" {
  value = "${aws_vpc.this.id}"
}

output "subnet_public_a_id" {
  value = "${aws_subnet.public_a.id}"
}

output "subnet_public_c_id" {
  value = "${aws_subnet.public_c.id}"
}
 </pre>



<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>のIDは作成時に割り当てられるので、attributesを参照して取得する。
その他<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>のattributesの内容は適宜Terraformのドキュメントを参照されたし。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.terraform.io/docs/providers/aws/r/vpc.html#attributes-reference">AWS: aws_vpc - Terraform by HashiCorp</a></li>
</ul>


<p>こうしておけば、<code>development/main.tf</code>内のec2モジュールの指定のところで、<code>${module.module_name.output_name}</code> の形、<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>だと<code>${module.module_vpc(development/main.tfのvpcのモジュール名).vpc_id(modules/vpc/output.tfのOutputラベル名)}</code>でを受け取れるようにしておけば、ec2モジュールで<a class="keyword" href="http://d.hatena.ne.jp/keyword/vpc">vpc</a>のidが利用可能になる。
サブネットに関しても同様。</p>

<ul>
    <li><code>development/main.tf</code></li>
</ul>
<pre class="terminal"> module "module_ec2" {
  source = "../modules/ec2"

  // vpc module(../module/vpc/output.tf)の内容を元に値を取得
  vpc_id = "${module.module_vpc.vpc_id}"
  subnet_public_a_id = "${module.module_vpc.subnet_public_a_id}"
  subnet_public_c_id = "${module.module_vpc.subnet_public_c_id}"
}
 </pre>



<h5>modules/ec2/以下の構成</h5>


<p><code>modules/ec2/main.tf</code>は下記となる。
ルートモジュール内で定義した変数を、子モジュール側でinput variableとして定義今回で言えば、<a class="keyword" href="http://d.hatena.ne.jp/keyword/vpc">vpc</a>_id, subnet_public_a_id, subnet_public_c_id の値)する必要があり、今回はmain.tf内に含めている(。</p>

<ul>
    <li><code>modules/ec2/main.tf</code></li>
</ul>
<pre class="terminal"> variable "vpc_id" {}
variable "subnet_public_a_id" {}
variable "subnet_public_c_id" {}

# Security Group
resource "aws_security_group" "this" {
  name   = "tf-example-sg"
  vpc_id = "${var.vpc_id}"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  description = "tf-example-sg"
}

# EC2
resource "aws_instance" "this_t2" {
  ami                     = "ami-2a69be4c"
  instance_type           = "t2.micro"
  disable_api_termination = false
  key_name                = "aws-key-pair"
  vpc_security_group_ids  = ["${aws_security_group.this.id}"]
  subnet_id               = "${var.subnet_public_a_id}"

  tags {
    Name = "tf-example-ec2"
  }
}

resource "aws_eip" "this" {
  instance = "${aws_instance.this_t2.id}"
  vpc      = true
}
 </pre>



<p>これでOK。</p>

<h4>実行</h4>


<p>下記の形で実行してみる。</p>

<pre class="terminal"> $ cd ~/terraform/development

$ terraform init -var-file=config.tfvars

$ terraform plan -var-file=config.tfvars

$ terraform apply -var-file=config.tfvars
 </pre>


<p>削除したい場合は以下を実行する。</p>

<pre class="terminal"> $ cd ~/terraform/development

$ terraform destroy
 </pre>


<h3>まとめ</h3>


<p>TerraformのModule機能を試した。</p>

<p><code>.tf</code>ファイルを簡潔に分割できそうではあるが、モジュール間の変数受け渡しは少々面倒で、子モジュールでoutput valuesで定義した値を、ルートモジュールの子モジュールブロック内で受け取り、別な子モジュール側でinput variablesを定義して利用する必要がある。
変数宣言する部分が増えたこと、それにより複雑化するときの管理コストの増加が懸念ではある。</p>
</body>
