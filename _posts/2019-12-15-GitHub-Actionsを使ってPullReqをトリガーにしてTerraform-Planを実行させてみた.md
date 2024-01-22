---
layout: post
Categories:  ["tech"]
Description:  " GitHub Actionsが11/13に正式リリースされた。     Actions | GitHub    GitHub Actionsについて - GitHub ヘルプ   参加はしていないけど Terraform meetup t"
Tags: ["GitHub", "tech", "Terraform"]
date: "2019-12-15T23:57:00+09:00"
title: "GitHub Actionsを使ってPullReqをトリガーにしてTerraform Planを実行させてみた"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a> Actionsが11/13に正式リリースされた。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://github.co.jp/features/actions">Actions | GitHub</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://help.github.com/ja/actions/automating-your-workflow-with-github-actions/about-github-actions">GitHub Actionsについて - GitHub ヘルプ</a></li>
</ul>


<p>参加はしていないけど <a target="_brank" rel="noopener noreferrer" href="https://terraform-jp.connpass.com/event/153286/">Terraform meetup tokyo #3</a> で <a target="_brank" rel="noopener noreferrer" href="https://twitter.com/dehio3">@dehio3</a> 様が以下の発表をされていたそうで、自分は今までローカルでPlan/Applyをやっていたのでこの資料を参考にTerraform × <a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a> Actionsをやってみた。</p>

<script async class="speakerdeck-embed" data-id="2518a2eee62840c992a05b474dd217b7" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

</body>

<!-- more -->

<body>
<h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a> ActionsのWorkflowを作成</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a> Actionsで、TerraformのPlanやApplyを実行するためのWorkflowを作成する。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://help.github.com/ja/actions/automating-your-workflow-with-github-actions/about-github-actions">GitHub Actionsについて - GitHub ヘルプ</a></li>
</ul>
<pre class="text"> ワークフローとは、GitHubで任意のコードプロジェクトをビルド、テスト、パッケージ、リリース、またはデプロイするためにリポジトリで設定できる、カスタムの自動プロセスです。
 </pre>



<p>Terraformコードが格納された<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>を選択し、画面上部メニューの「Actions」タブを押下する。
次の画面で、画面上の方にある「Setup Workflow your self」ボタンを押下する。</p>

<p>Workflowの作成画面に遷移し、Workflowをコード化する画面となる。</p>

<p>ここで独自にWorkflowを定義しても良いが、hashicorp社からサンプルのWorkflowのコードが公開されているため、これをほぼそのまま使うこともできる。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/hashicorp/terraform-github-actions">hashicorp/terraform-github-actions: Terraform GitHub Actions</a></li>
</ul>


<p>この中で<code>actions.yml</code>をそのまま利用できる。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/hashicorp/terraform-github-actions/blob/master/action.yml">terraform-github-actions/action.yml at master · hashicorp/terraform-github-actions</a></li>
</ul>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>からPlanを実行できるようにするために、<code>AWS_ACCESS_KEY_ID</code>、<code>AWS_SECRET_ACCESS_KEY</code>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>をコードにベタ書きするのではなく、<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>のSecretsに登録するように変更する。</p>

<pre class="terminal"> name: 'Terraform GitHub Actions'
on:
  - pull_request    # pull requestが出された時にこのGitHub Actionsを実行する
jobs:
  terraform:
    name: 'Terraform'
    runs-on: ubuntu-latest
    steps:
      - name: 'Checkout'
        uses: actions/checkout@master
      - name: 'Terraform Format'
        uses: hashicorp/terraform-github-actions@master
        with:
          tf_actions_version: 0.12.13
          tf_actions_subcommand: 'fmt'
          tf_actions_working_dir: '.'
          tf_actions_comment: true
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      - name: 'Terraform Init'
        uses: hashicorp/terraform-github-actions@master
        with:
          tf_actions_version: 0.12.13
          tf_actions_subcommand: 'init'
          tf_actions_working_dir: '.'
          tf_actions_comment: true
          args: '-var-file="terraform.tfvars"'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      - name: 'Terraform Validate'
        uses: hashicorp/terraform-github-actions@master
        with:
          tf_actions_version: 0.12.13
          tf_actions_subcommand: 'validate'
          tf_actions_working_dir: '.'
          tf_actions_comment: true
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      - name: 'Terraform Plan'
        uses: hashicorp/terraform-github-actions@master
        with:
          tf_actions_version: 0.12.13
          tf_actions_subcommand: 'plan'
          tf_actions_working_dir: '.'
          tf_actions_comment: true
          args: '-var-file="terraform.tfvars"'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
 </pre>




<h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>へのSecretsの登録</h3>


<p><code>AWS_ACCESS_KEY_ID</code>、<code>AWS_SECRET_ACCESS_KEY</code>を<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>のSecretsに登録する。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://help.github.com/ja/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets">暗号化されたシークレットの作成と利用 - GitHub ヘルプ</a></li>
</ul>


<p>Terraformコードを格納した<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>を選択し、画面上のメニューで「Settings」を選択する。
次のページの画面左メニューから「Secrets」を選択する。
Name、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Value">Value</a>の形で入力する必要があるので、形式に沿って入力する。</p>

<p> </p>
<table>
    <tr>
      <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>_<a class="keyword" href="http://d.hatena.ne.jp/keyword/ACCESS">ACCESS</a>_KEY_ID</td>
      <td>アクセスキーの値</td>
    </tr>
    <tr>
      <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>_SECRET_<a class="keyword" href="http://d.hatena.ne.jp/keyword/ACCESS">ACCESS</a>_KEY</td>
      <td>シークレットキーの値</td>
    </tr>
  </table>

<h3>Actionsの実行</h3>


<p>上述のコードで、pull requestが出された時にActionsを実行するようになっているので、PullReqを出してActions(ここではTerraform plan)が実行されることを確認する。</p>

<pre class="terminal"> on:
  - pull_request
 </pre>


<p>Planが成功すればそのままMergeしてOK。Applyも自動で実行できるかもしれないが、そこまでは試せていないのでわからない。
Planに失敗した場合Terraformコードに問題があることがわかるので、CIを<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>上だけで完結できて便利になった。</p>

<h3>まとめ</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a> Actionsを使ったTerraform Planの自動実行を、記事に記載した資料を参考に設定してみた。
Workflowを使うことで、<a class="keyword" href="http://d.hatena.ne.jp/keyword/GitHub">GitHub</a>上でTerraformのCI/CDを実装できそうで、とても便利そうだった。</p>

<h3>参考</h3>




<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://github.co.jp/features/actions">Actions | GitHub</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://help.github.com/ja/actions/automating-your-workflow-with-github-actions/about-github-actions">GitHub Actionsについて - GitHub ヘルプ</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://help.github.com/ja/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets">暗号化されたシークレットの作成と利用 - GitHub ヘルプ</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://speakerdeck.com/tomohide_tanaka/i-tried-to-plan-and-apply-terraform-with-github-actions">GitHub Actionsで Terraformをplan&amp;applyしてみた / I tried to plan and apply Terraform with GitHub Actions - Speaker Deck</a></li>
</ul>

</body>
