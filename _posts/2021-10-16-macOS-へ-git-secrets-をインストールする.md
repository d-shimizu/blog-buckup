---
layout: post
Categories:  ["tech"]
Description:  " AWS のクレデンシャル情報を誤って git に commit されるのを防ぐために、git-secrets を使う。  github.com "
Tags: []
date: "2021-10-16T23:29:00+09:00"
title: "macOS へ git-secrets をインストールする"
author: "dshimizu"
archive: ["2021"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> のクレデンシャル情報を誤って git に commit されるのを防ぐために、git-secrets を使う。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Fawslabs%2Fgit-secrets" title="GitHub - awslabs/git-secrets: Prevents you from committing secrets and credentials into git repositories" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;"></iframe><cite class="hatena-citation"><a href="https://github.com/awslabs/git-secrets">github.com</a></cite></p>
</body>

<!-- more -->

<body>
<h2>インストール</h2>

<pre class="code" data-lang="" data-unlink> % brew install git-secrets </pre>


<h2>設定(各<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>毎)</h2>

<p>テンプレートのインストールを行う。</p>

<pre class="code" data-lang="" data-unlink> $ cd /path/to/repos

$ git secrets --install
✓ Installed commit-msg hook to .git/hooks/commit-msg
✓ Installed pre-commit hook to .git/hooks/pre-commit
✓ Installed prepare-commit-msg hook to .git/hooks/prepare-commit-msg </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> IAM の <a class="keyword" href="http://d.hatena.ne.jp/keyword/Access">Access</a> Key / Secrets <a class="keyword" href="http://d.hatena.ne.jp/keyword/Access">Access</a> Key の文字列パターンをチェックするオプションを実行する。</p>

<pre class="code" data-lang="" data-unlink> % git secrets --register-aws
OK </pre>


<p><code>/path/to/repos/.git/config</code> に以下がセットされる。</p>

<pre class="code" data-lang="" data-unlink> [secrets]
    providers = git secrets --aws-provider
    patterns = (A3T[A-Z0-9]|AKIA|AGPA|AIDA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}
    patterns = (\"|')?(AWS|aws|Aws)?_?(SECRET|secret|Secret)?_?(ACCESS|access|Access)?_?(KEY|key|Key)(\"|')?\\s*(:|=&gt;|=)\\s*(\"|')?[A-Za-z0-9/\\+=]{40}(\"|')?
    patterns = (\"|')?(AWS|aws|Aws)?_?(ACCOUNT|account|Account)_?(ID|id|Id)?(\"|')?\\s*(:|=&gt;|=)\\s*(\"|')?[0-9]{4}\\-?[0-9]{4}\\-?[0-9]{4}(\"|')?
    allowed = $AccessKey
    allowed = $SecretAccessKey </pre>


<h2>設定(グローバル)</h2>

<p><code>~/.git-templates/git-secrets</code> にテンプレートのインストールを行う。</p>

<pre class="code" data-lang="" data-unlink> % git secrets --install ~/.git-templates/git-secrets
✓ Installed commit-msg hook to /Users/dshimizu/.git-templates/git-secrets/hooks/commit-msg
✓ Installed pre-commit hook to /Users/dshimizu/.git-templates/git-secrets/hooks/pre-commit
✓ Installed prepare-commit-msg hook to /Users/dshimizu/.git-templates/git-secrets/hooks/prepare-commit-msg </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> IAM の <a class="keyword" href="http://d.hatena.ne.jp/keyword/Access">Access</a> Key / Secrets <a class="keyword" href="http://d.hatena.ne.jp/keyword/Access">Access</a> Key の文字列パターンをチェックするオプションを実行する。</p>

<pre class="code" data-lang="" data-unlink> % git secrets --register-aws --global
OK </pre>


<p>git init 実行時に git-secrets も有効にしたい場合は、以下を実行すれば global config に反映される。</p>

<pre class="code" data-lang="" data-unlink> % git config --global init.templateDir ~/.git-templates/git-secrets </pre>


<p><code>~/.gitconfig</code> に以下がセットされる。</p>

<pre class="code" data-lang="" data-unlink> [init]
    templateDir = /Users/dshimizu/.git-templates/git-secrets

[secrets]
    providers = git secrets --aws-provider
    patterns = (A3T[A-Z0-9]|AKIA|AGPA|AIDA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}
    patterns = (\"|')?(AWS|aws|Aws)?_?(SECRET|secret|Secret)?_?(ACCESS|access|Access)?_?(KEY|key|Key)(\"|')?\\s*(:|=&gt;|=)\\s*(\"|')?[A-Za-z0-9/\\+=]{40}(\"|')?
    patterns = (\"|')?(AWS|aws|Aws)?_?(ACCOUNT|account|Account)_?(ID|id|Id)?(\"|')?\\s*(:|=&gt;|=)\\s*(\"|')?[0-9]{4}\\-?[0-9]{4}\\-?[0-9]{4}(\"|')?
    allowed = $AccessKey
    allowed = $SecretAccessKey </pre>


<h2>参考</h2>

<ul>
<li><a href="https://github.com/awslabs/git-secrets">GitHub - awslabs/git-secrets: Prevents you from committing secrets and credentials into git repositories</a></li>
</ul>

</body>
