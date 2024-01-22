---
layout: post
Categories:  ["tech"]
Description:  " Terraform Cloud × GitHubを試した  Terraform meetup tokyo #2へ行ったときにTerraform CloudをおすすめされていたのでGitHubと連携させて試した。       Terrafo"
Tags: ["tech", "Terraform"]
date: "2019-12-07T19:39:00+09:00"
title: "Terraform Cloudを使ってみた"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p>Terraform Cloud × <a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>を試した</p>

<p>Terraform meetup tokyo #2へ行ったときにTerraform Cloudをおすすめされていたので<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>と連携させて試した。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://app.terraform.io/">Terraform Cloud</a></li>
</ul>

</body>

<!-- more -->

<body>
<h3>Terraform Cloudを試す</h3>




<h4>事前準備</h4>




<h5>アカウント登録</h5>


<p>まず、Terraform Cloudのアカウントを作成する。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://app.terraform.io/signup/account">Terraform Cloud</a></li>
</ul>


<p>アカウント作成の詳細は割愛する。</p>

<h5>Organizationの作成</h5>


<p>続いてOrganizationを作成する。
OrganizationはTerraform Cloudをチーム内で扱うための共有スペースみたいなもの。
名前は何でも良いけど、全ユーザでユニークである必要がある。
特に難しいことはないので作り方の詳細は割愛する。</p>

<h5>WorkSpaceの作成</h5>


<p>WorkSpaceはTrraform Clouod内で1つのTerraformコード群(ex <a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>単位)を実行する場所、という認識。</p>

<ul>
  <li><a target="_brank" rel="nooepner noopener noreferrer" href="https://www.terraform.io/docs/cloud/workspaces/index.html">Workspaces - Terraform Cloud - Terraform by HashiCorp</a></li>
</ul>


<p>連携は<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>、GitLab、BitBucket、Azure DevOpsを選択可能。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>との接続しか試していないが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>の場合は特定の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>を選択して、そこにあるTerraformコードを実行させる形になる。</p>

<p>空の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>は選択するとWorkSpaceの作成でエラーになるため、あらかじめコードを設置しておく必要がある。</p>

<p>ここでは以下のようなコードで<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPC">VPC</a>、サブネット、インターネット<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B2%A1%BC%A5%C8%A5%A6%A5%A7%A5%A4">ゲートウェイ</a>、ルートテーブルを作成するコードを実行させる</p>

<p>main.tf</p>

<pre class="terminal"> provider "aws" {
    access_key = var.aws_access_key_id
    secret_key = var.aws_secret_access_key
    region = "ap-northeast-1"
}


// Create VPC 
resource "aws_vpc" "main" {
    cidr_block = local.vpc_cidr.dev

    instance_tenancy = "default"

    tags = {
        Name = local.project_name
    }
}

// Create Public Subnet
resource "aws_subnet" "public" {

    for_each          = local.subnet_numbers

    vpc_id            = aws_vpc.main.id

    availability_zone = each.key
    cidr_block        = cidrsubnet(aws_vpc.main.cidr_block, 8, each.value)
}

// Create Internet Gateway
resource "aws_internet_gateway" "main" {
    vpc_id = aws_vpc.main.id
}

// Attach IGW on Route Table for Public Subnet
resource "aws_route_table" "public" {
    vpc_id = aws_vpc.main.id
    route {
      cidr_block = local.vpc_cidr.prod
      gateway_id = aws_internet_gateway.main.id
   }
}

// Create Route Table
resource "aws_route_table_association" "puclic-1a" {
    for_each       = local.subnet_numbers

    subnet_id = aws_subnet.public[each.key].id
    route_table_id = aws_route_table.public.id
}
 </pre>


<p>variable.tf</p>

<pre class="terminal"> variable "aws_access_key_id" {}
variable "aws_secret_access_key" {}


locals {
  project_name = "tf-example"

  vpc_cidr = {
    dev = "10.2.0.0/16"
    stg = "10.1.0.0/16"
    prod = "10.0.0.0/16"
  }

  subnet_numbers = {
    "ap-northeast-1a" = 1
    "ap-northeast-1c" = 2
    "ap-northeast-1d" = 3
  }
}
 </pre>




<h5>Terraform Cluod側への<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>設定</h5>


<p>Terraformの実行には<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/Access">Access</a> key, Secret keyが必要だが、variables.tfとかに直接書くとセキュリティ的にも良くないので、Terraform CloudからTerraformを実行する際に利用する<a class="keyword" href="http://d.hatena.ne.jp/keyword/Access">Access</a> key, Secret keyをTerraform Cloudの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>にセットする。</p>

<p>WorkSpaceを選択し、画面上のメニューのVariablesから設定する。</p>

<p><img src="http://blog.dshimizu.jp/wp-content/uploads/terraform-cloud-workspace_variables_2019110701.png" alt="" width="1827" height="1129" class="aligncenter size-full wp-image-1805"></p>

<p>真ん中の「Terraform Variables」で「Add Variables」ボタンを押下して、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/Access">Access</a> key, Secret keyの変数と値をセットする。
変数名はtfファイルの変数名(ここでは<code>aws_access_key_id</code>, <code>aws_secret_access_key</code>)と合わせる必要がある。セットするときに右側のSensitiveの項目にチェックを入れると画面上は値が表示されなくなる。</p>

<p><img src="http://blog.dshimizu.jp/wp-content/uploads/terraform-cloud-workspace_variables_2019110702.png" alt="" width="1827" height="1129" class="aligncenter size-full wp-image-1807"></p>

<h5>手動でのPlanの実行</h5>


<p>この状態で、画面右上の「Queue Plan」ボタンを押下すると、Terraform Planを実行できる。</p>

<p><img src="http://blog.dshimizu.jp/wp-content/uploads/Terraform-Cloud-Plan-Manual-2019-11-07-20_07_27.png" alt="" width="1827" height="1179" class="aligncenter size-full wp-image-1808"></p>

<h5>自動でのPlanの実行</h5>


<p>この状態で、<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>へPullRequestを出すと自動でPlanが実行される。</p>

<p><img src="http://blog.dshimizu.jp/wp-content/uploads/Terraform-Cloud-Plan-Auto-2019-11-07-20_08_12.png" alt="" width="1827" height="1284" class="aligncenter size-full wp-image-1809"></p>

<p>マージしたら自動でApplyさせるとかも設定によっては可能。
地味に悩ましいTerraformでのCI/CDを簡単に組むことができてとても便利。</p>

<h3>まとめ</h3>


<p>Terraform Cloudの基本的な設定の仕方、<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>と連携させることでPullReqを出したときに自動でPlanさせることができる旨を書いた。</p>

<h3>参考</h3>




<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://app.terraform.io/signup/account">Terraform Cloud</a></li>
    <li><a target="_brank" rel="nooepner noopener noreferrer" href="https://www.terraform.io/docs/cloud/index.html">Home - Terraform Cloud - Terraform by HashiCorp</a></li>
    <li><a target="_brank" rel="noopener noreferrer" herf="https://dev.classmethod.jp/cloud/aws/terraform-cloud/">5人まで無料！ Terraform Cloudを使ってみた ｜ Developers.IO</a></li>
</ul>

</body>
