---
title: "tfenv を macOS にインストールしつつ、内部の動きを見てみた"
date: 2022-02-13
slug: "tfenv を macOS にインストールしつつ、内部の動きを見てみた"
category: blog
tags: [Terraform,tfenv]
---
<h2>はじめに</h2>

<p>今まで Terraform を、Terraform の Docker イメージを<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EF%A5%F3%A5%E9%A5%A4%A5%CA%A1%BC">ワンライナー</a>的に使っていたけど、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>の扱いとかが面倒で一行が長い。というところでやっぱり tfenv が良さそうと思いつつまだ使ったことなかったのでとりあえず <a class="keyword" href="http://d.hatena.ne.jp/keyword/macOS">macOS</a> にインストールする...だけなのもイマイチなのでとりあえず内部がどんな感じなのかちょっと見てみた。</p>

<p><iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fgithub.com%2Ftfutils%2Ftfenv" title="GitHub - tfutils/tfenv: Terraform version manager" class="embed-card embed-webcard" scrolling="no" frameborder="0" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;"></iframe><cite class="hatena-citation"><a href="https://github.com/tfutils/tfenv">github.com</a></cite></p>

<h2>tfenv</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>の README にも書かれているとおり、Terraform のバージョン管理ツール。これで複数のバージョンの Terraform を共存させて使い分けたりなどができる。
rbenv や pyenv などあるけど、rbenv に触発されたと書かれている。
中身をさらっと見ると <a class="keyword" href="http://d.hatena.ne.jp/keyword/bash">bash</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>だった。</p>

<h2>tfenv インストール</h2>

<p>Homebrew でインストールできる。</p>

<pre class="code bash" data-lang="bash" data-unlink>$ brew install tfenv</pre>


<p>Homebrew でインストールすると、必要なファイルが以下のような感じで配置される。<code>/usr/local/Cellar/tfenv/2.2.0/bin/tfenv</code> は <code>/usr/local/bin/tfenv</code> に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%F3%A5%DC%A5%EA%A5%C3%A5%AF%A5%EA%A5%F3%A5%AF">シンボリックリンク</a>される。</p>

<pre class="code" data-lang="" data-unlink>/usr/local/Cellar/tfenv
/usr/local/Cellar/tfenv/2.2.0/bin/tfenv
/usr/local/Cellar/tfenv/2.2.0/.brew/tfenv.rb
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-list-remote
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-init
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv---version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-uninstall
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-exec
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-list
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-resolve-version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-use
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-name
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-help
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-min-required</pre>


<p>バージョン確認。</p>

<pre class="code" data-lang="" data-unlink>$ tfenv --version
tfenv 2.2.0</pre>


<p>内部的には <code>bin/tfenv</code> から libexec の PATH をチェックしつつExportしている。</p>

<pre class="code bash" data-lang="bash" data-unlink># Ensure libexec and bin are in $PATH
for dir in libexec bin; do
  case &#34;:${PATH}:&#34; in
    *:${TFENV_ROOT}/${dir}:*) log &#39;debug&#39; &#34;\$PATH already contains &#39;${TFENV_ROOT}/${dir}&#39;, not adding it again&#34;;;
    *) 
      log &#39;debug&#39; &#34;\$PATH does not contain &#39;${TFENV_ROOT}/${dir}&#39;, prepending and exporting it now&#34;;
      export PATH=&#34;${TFENV_ROOT}/${dir}:${PATH}&#34;;
      ;;
  esac;
done;</pre>


<p>そこにある <code>libexec/tfenv---version</code> のコマンドが呼ばれている模様。</p>

<pre class="code" data-lang="" data-unlink>case &#34;${arg}&#34; in
  &#34;&#34;)
    log &#39;debug&#39; &#39;No argument provided, dumping version and help and aborting&#39;;
    {
      tfenv---version;
      tfenv-help;
    } | abort &amp;&amp; exit 1;
exit 1;
    ;;
  -v | --version )
    log &#39;debug&#39; &#39;tfenv version requested...&#39;;
    exec tfenv---version;
    ;;
  :</pre>


<p>ちなみに <code>$TFENV_ROOT</code> は、 <code>tfenv</code> がある場所(<code>/usr/local/Cellar/tfenv/2.2.0/bin/</code>) の一つ上の階層(<code>/usr/local/Cellar/tfenv/2.2.0/</code>)を見ているっぽいたぶん。</p>

<pre class="code" data-lang="" data-unlink>if [ -z &#34;${TFENV_ROOT:-&#34;&#34;}&#34; ]; then
  # http://stackoverflow.com/questions/1055671/how-can-i-get-the-behavior-of-gnus-readlink-f-on-a-mac
  readlink_f() {
    local target_file=&#34;${1}&#34;;
    local file_name;

    while [ &#34;${target_file}&#34; != &#34;&#34; ]; do
      cd &#34;$(dirname ${target_file})&#34; || early_death &#34;Failed to &#39;cd \$(dirname ${target_file})&#39; while trying to determine TFENV_ROOT&#34;;
      file_name=&#34;$(basename &#34;${target_file}&#34;)&#34; || early_death &#34;Failed to &#39;basename \&#34;${target_file}\&#34;&#39; while trying to determine TFENV_ROOT&#34;;
      target_file=&#34;$(readlink &#34;${file_name}&#34;)&#34;;
    done;

    echo &#34;$(pwd -P)/${file_name}&#34;;
  };

  TFENV_ROOT=&#34;$(cd &#34;$(dirname &#34;$(readlink_f &#34;${0}&#34;)&#34;)/..&#34; &amp;&amp; pwd)&#34;;
  [ -n ${TFENV_ROOT} ] || early_death &#34;Failed to &#39;cd \&#34;\$(dirname \&#34;\$(readlink_f \&#34;${0}\&#34;)\&#34;)/..\&#34; &amp;&amp; pwd&#39; while trying to determine TFENV_ROOT&#34;;
else
  TFENV_ROOT=&#34;${TFENV_ROOT%/}&#34;;
fi;
export TFENV_ROOT;</pre>


<h2>tfevn で利用可能な Terraform バージョン取得</h2>

<p><code>tfenv list-remote</code> で利用可能なバージョンを取得できる。</p>

<pre class="code" data-lang="" data-unlink>$ tfenv list-remote
1.1.5
1.1.4
:</pre>


<p>これは <code>https://releases.hashicorp.com/terraform/</code> の中からバージョン一覧を取得してコネコネしているようだった。</p>

<pre class="code" data-lang="" data-unlink>TFENV_REMOTE=&#34;${TFENV_REMOTE:-https://releases.hashicorp.com}&#34;
log &#39;debug&#39; &#34;TFENV_REMOTE: ${TFENV_REMOTE}&#34;;

declare remote_versions;
remote_versions=&#34;$(curlw -sSf &#34;${TFENV_REMOTE}/terraform/&#34;)&#34; \
  || log &#39;error&#39; &#34;Failed to download remote versions from ${TFENV_REMOTE}/terraform/&#34;;

#log &#39;debug&#39; &#34;Remote versions available: ${remote_versions}&#34;; # Even in debug mode this is too verbose

grep -o -E &#34;[0-9]+\.[0-9]+\.[0-9]+(-(rc|beta|alpha|oci)[0-9]*)?&#34; &lt;&lt;&lt;&#34;${remote_versions}&#34; | uniq;</pre>


<p><code>curlw</code> は <code>lib/helpers.sh</code> で function が定義されている。<a class="keyword" href="http://d.hatena.ne.jp/keyword/TLS">TLS</a> のオプションが追加されている。</p>

<pre class="code" data-lang="" data-unlink># Curl wrapper to switch TLS option for each OS
function curlw () {
  local TLS_OPT=&#34;--tlsv1.2&#34;;

  # Check if curl is 10.12.6 or above
  if [[ -n &#34;$(command -v sw_vers 2&gt;/dev/null)&#34; &amp;&amp; (&#34;$(sw_vers)&#34; =~ 10\.12\.([6-9]|[0-9]{2}) || &#34;$(sw_vers)&#34; =~ 10\.1[3-9]) ]]; then
    TLS_OPT=&#34;&#34;;
  fi;

  curl ${TLS_OPT} &#34;$@&#34;;
}
export -f curlw;</pre>


<h2>tfevn で Terraform インストール</h2>

<p>インストールコマンドは以下。</p>

<pre class="code" data-lang="" data-unlink>$ tfenv install 1.1.5</pre>


<p>Debug レベルを上げて実行してみると、内部的は <code>libexec/tfenv-install</code> や <code>tfenv-resolve-version</code>, <code>tfenv-list-remote</code> なども呼び出されて実行されている。
Debug レベル は TFENV_DEBUG で 0-3 で指定できる。0がなし。</p>

<pre class="code" data-lang="" data-unlink>% TFENV_DEBUG=2 tfenv install 1.1.5
[DEBUG] Sourcing helpers from /usr/local/Cellar/tfenv/2.2.0/lib/helpers.sh
/usr/local/bin/tfenv: DEBUG trap set
/usr/local/bin/tfenv: Helpers sourced successfully
/usr/local/bin/tfenv: $PATH does not contain &#39;/usr/local/Cellar/tfenv/2.2.0/libexec&#39;, prepending and exporting it now
/usr/local/bin/tfenv: $PATH does not contain &#39;/usr/local/Cellar/tfenv/2.2.0/bin&#39;, prepending and exporting it now
/usr/local/bin/tfenv: Setting TFENV_DIR to /Users/daisukeshimizu
/usr/local/bin/tfenv: tfenv argument is: install
/usr/local/bin/tfenv: Long argument provided: install
/usr/local/bin/tfenv: Resulting command-path: /usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install
/usr/local/bin/tfenv: Exec: &#34;/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install&#34; &#34;1.1.5&#34;
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install: TFENV_HELPERS is set, not sourcing helpers again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/libexec&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/bin&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install: Resolving version with: tfenv-resolve-version 1.1.5
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-resolve-version: TFENV_HELPERS is set, not sourcing helpers again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-resolve-version: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/libexec&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-resolve-version: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/bin&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-resolve-version: Version Requested: 1.1.5
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-resolve-version: Version is explicit: 1.1.5. Regex enforces the version: ^1.1.5$
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install: Processing install for version 1.1.5, using regex ^1.1.5$
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-list-remote: TFENV_HELPERS is set, not sourcing helpers again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-list-remote: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/libexec&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-list-remote: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/bin&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-list-remote: TFENV_REMOTE: https://releases.hashicorp.com
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install: Installing Terraform v1.1.5
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install: Setting curl progress bar with &#34;-#&#34;
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install: Downloading release tarball from https://releases.hashicorp.com/terraform/1.1.5/terraform_1.1.5_darwin_amd64.zip
######################################################################################################################################################################################################################################################### 100.0%
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install: Downloading SHA hash file from https://releases.hashicorp.com/terraform/1.1.5/terraform_1.1.5_SHA256SUMS
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install: No keybase install found, skipping OpenPGP signature verification
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install: Archive:  /var/folders/6_/yxwcl8kn6dz_fpt7r0z1qd8h0000gn/T/tfenv_download.XXXXXX.OtnmaKHo/terraform_1.1.5_darwin_amd64.zip
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install:   inflating: /usr/local/Cellar/tfenv/2.2.0/versions/1.1.5/terraform
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-install: Installation of terraform v1.1.5 successful. To make this your default version, run &#39;tfenv use 1.1.5&#39;</pre>


<p>あんまり読めてないけど引数で受け取ったバージョンからリンク(<code>https://releases.hashicorp.com/terraform/1.1.5/terraform_1.1.5_darwin_amd64.zip</code>)を組み立てて、<code>mktemp</code> で一時<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成してそこに落としている。<a class="keyword" href="http://d.hatena.ne.jp/keyword/macOS">macOS</a> だと <code>$TMPDIR</code> が <code>/var/folders/XXX</code> 以下になっているのでそこになる。
その後、unzip で展開して <code>${TFENV_CONFIG_DIR}/versions/${version}</code> に配置している、
<code>${TFENV_CONFIG_DIR}</code> は <code>lib/helpers.sh</code> で指定されるようになっていた。</p>

<pre class="code" data-lang="" data-unlink>if [ -z &#34;${TFENV_CONFIG_DIR:-&#34;&#34;}&#34; ]; then
  TFENV_CONFIG_DIR=&#34;$TFENV_ROOT&#34;;
else
  TFENV_CONFIG_DIR=&#34;${TFENV_CONFIG_DIR%/}&#34;;
fi
export TFENV_CONFIG_DIR;</pre>


<h2>デフォルト設定</h2>

<p>以下で設定する。</p>

<pre class="code" data-lang="" data-unlink>$ tfenv use 1.1.5
Switching default version to v1.1.5
Switching completed</pre>


<p>これは結構複雑だった。ので、Debugレベルを上げて実行してみる。
結構いろんなファイルが呼び出されつつ、最終的には <code>libexec/tfenv-exec</code>で <code>$PATH</code> <a class="keyword" href="http://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>に該当バージョンの Terraform コマンドのパスを追加している。</p>

<pre class="code" data-lang="" data-unlink>% TFENV_DEBUG=2 tfenv use 1.1.5
[DEBUG] Sourcing helpers from /usr/local/Cellar/tfenv/2.2.0/lib/helpers.sh
/usr/local/bin/tfenv: DEBUG trap set
/usr/local/bin/tfenv: Helpers sourced successfully
/usr/local/bin/tfenv: $PATH does not contain &#39;/usr/local/Cellar/tfenv/2.2.0/libexec&#39;, prepending and exporting it now
/usr/local/bin/tfenv: $PATH does not contain &#39;/usr/local/Cellar/tfenv/2.2.0/bin&#39;, prepending and exporting it now
/usr/local/bin/tfenv: Setting TFENV_DIR to /Users/daisukeshimizu
/usr/local/bin/tfenv: tfenv argument is: use
/usr/local/bin/tfenv: Long argument provided: use
/usr/local/bin/tfenv: Resulting command-path: /usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-use
/usr/local/bin/tfenv: Exec: &#34;/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-use&#34; &#34;1.1.5&#34;
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-use: TFENV_HELPERS is set, not sourcing helpers again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-use: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/libexec&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-use: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/bin&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-use: Resolving version with: tfenv-resolve-version 1.1.5
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-resolve-version: TFENV_HELPERS is set, not sourcing helpers again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-resolve-version: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/libexec&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-resolve-version: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/bin&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-resolve-version: Version Requested: 1.1.5
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-resolve-version: Version is explicit: 1.1.5. Regex enforces the version: ^1.1.5$
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-use: Resolved to: 1.1.5:^1.1.5$
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-use: Searching /usr/local/Cellar/tfenv/2.2.0/versions/ for latest version matching ^1.1.5$
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-use: Found version: 1.1.5
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-use: Switching default version to v1.1.5
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-use: Writing &#34;1.1.5&#34; to &#34;/usr/local/Cellar/tfenv/2.2.0/version&#34;
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: TFENV_HELPERS is set, not sourcing helpers again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/libexec&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/bin&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Looking for a version file in /Users/daisukeshimizu
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Not found at /Users/daisukeshimizu/.terraform-version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Not found at /Users/.terraform-version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Not found at /.terraform-version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: No version file found in /Users/daisukeshimizu
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Looking for a version file in /Users/daisukeshimizu
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Not found at /Users/daisukeshimizu/.terraform-version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Not found at /Users/.terraform-version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Not found at /.terraform-version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: No version file found in /Users/daisukeshimizu
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: No version file found in search paths. Defaulting to TFENV_CONFIG_DIR: /usr/local/Cellar/tfenv/2.2.0/version
/usr/local/Cellar/tfenv/2.2.0/bin/terraform: TFENV_HELPERS is set, not sourcing helpers again
/usr/local/Cellar/tfenv/2.2.0/bin/terraform: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/libexec&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/bin/terraform: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/bin&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/bin/terraform: program=&#34;terraform&#34;
/usr/local/Cellar/tfenv/2.2.0/bin/terraform: Exec: &#34;/usr/local/Cellar/tfenv/2.2.0/bin/tfenv&#34; exec &#34;version&#34;
/usr/local/Cellar/tfenv/2.2.0/bin/tfenv: TFENV_HELPERS is set, not sourcing helpers again
/usr/local/Cellar/tfenv/2.2.0/bin/tfenv: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/libexec&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/bin/tfenv: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/bin&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/bin/tfenv: Setting TFENV_DIR to /Users/daisukeshimizu
/usr/local/Cellar/tfenv/2.2.0/bin/tfenv: tfenv argument is: exec
/usr/local/Cellar/tfenv/2.2.0/bin/tfenv: Long argument provided: exec
/usr/local/Cellar/tfenv/2.2.0/bin/tfenv: Resulting command-path: /usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-exec
/usr/local/Cellar/tfenv/2.2.0/bin/tfenv: Exec: &#34;/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-exec&#34; &#34;version&#34;
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-exec: TFENV_HELPERS is set, not sourcing helpers again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-exec: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/libexec&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-exec: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/bin&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-exec: Getting version from tfenv-version-name
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-name: TFENV_HELPERS is set, not sourcing helpers again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-name: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/libexec&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-name: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/bin&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: TFENV_HELPERS is set, not sourcing helpers again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/libexec&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: $PATH already contains &#39;/usr/local/Cellar/tfenv/2.2.0/bin&#39;, not adding it again
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Looking for a version file in /Users/daisukeshimizu
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Not found at /Users/daisukeshimizu/.terraform-version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Not found at /Users/.terraform-version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Not found at /.terraform-version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: No version file found in /Users/daisukeshimizu
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Looking for a version file in /Users/daisukeshimizu
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Not found at /Users/daisukeshimizu/.terraform-version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Not found at /Users/.terraform-version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: Not found at /.terraform-version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: No version file found in /Users/daisukeshimizu
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-file: No version file found in search paths. Defaulting to TFENV_CONFIG_DIR: /usr/local/Cellar/tfenv/2.2.0/version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-name: TFENV_VERSION_FILE retrieved from tfenv-version-file: /usr/local/Cellar/tfenv/2.2.0/version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-name: TFENV_VERSION specified in TFENV_VERSION_FILE: 1.1.5
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-version-name: TFENV_VERSION does not use &#34;latest&#34; keyword
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-exec: TFENV_VERSION is 1.1.5
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-exec: TF_BIN_PATH added to PATH: /usr/local/Cellar/tfenv/2.2.0/versions/1.1.5/terraform
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-exec: Executing: /usr/local/Cellar/tfenv/2.2.0/versions/1.1.5/terraform version
/usr/local/Cellar/tfenv/2.2.0/libexec/tfenv-use: Switching completed</pre>


<h2>まとめ</h2>

<p>tfenv を <a class="keyword" href="http://d.hatena.ne.jp/keyword/macOS">macOS</a> にインストールしつつ、内部の動きを見てみた。
細かいところはまだ理解できてないけど、なんとなく全体感は把握できた。</p>

<h2>参考</h2>

<p><a href="https://github.com/tfutils/tfenv">GitHub - tfutils/tfenv: Terraform version manager</a></p>

