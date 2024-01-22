---
title: "Cloud SDK をインストールして gcloud コマンドを使って config の設定をする"
date: 2022-03-19
slug: "Cloud SDK をインストールして gcloud コマンドを使って config の設定をする"
category: blog
tags: [Google Cloud]
---
<h2>はじめに</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud を使い始めているため、色々環境整備をしている。</p>

<p>ローカルで <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud を扱うにあたって、 Cloud <a class="keyword" href="http://d.hatena.ne.jp/keyword/SDK">SDK</a> というのがあり、これを使って <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a> で操作できるので、これをインストールして触ってみる。</p>

<ul>
<li><a href="https://cloud.google.com/sdk/docs?hl=ja">Cloud SDK &#x306E;&#x30C9;&#x30AD;&#x30E5;&#x30E1;&#x30F3;&#x30C8; &nbsp;|&nbsp; Google Cloud</a></li>
</ul>


<h2>インストール</h2>

<p>ドキュメントだと<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AB%A5%A4%A5%D6">アーカイブ</a>ファイルを取得するやり方だけど、パッケージマネージャー経由でもインストールできる。 <a class="keyword" href="http://d.hatena.ne.jp/keyword/macOS">macOS</a> だと Homebrew でもインストールできる。</p>

<ul>
<li><a href="https://cloud.google.com/sdk/docs/install-sdk?hl=ja">https://cloud.google.com/sdk/docs/install-sdk?hl=ja</a></li>
</ul>


<h2>使ってみる</h2>

<p>バージョン。</p>

<pre class="code" data-lang="" data-unlink>% gcloud -v
Google Cloud SDK 377.0.0
bq 2.0.74
core 2022.03.10
gsutil 5.8
Updates are available for some Google Cloud CLI components.  To install them,
please run:
  $ gcloud components update</pre>


<p>ヘルプを見てみる。</p>

<pre class="code" data-lang="" data-unlink>% gcloud -h
Usage: gcloud [optional flags] &lt;group | command&gt;
  group may be           access-approval | access-context-manager |
                         active-directory | ai | ai-platform | anthos |
                         api-gateway | apigee | app | artifacts | asset |
                         assured | auth | bigtable | billing | bms | builds |
                         cloud-shell | components | composer | compute |
                         config | container | data-catalog |
                         database-migration | dataflow | dataplex | dataproc |
                         datastore | datastream | debug | deploy |
                         deployment-manager | dns | domains | emulators |
                         endpoints | essential-contacts | eventarc | filestore |
                         firebase | firestore | functions | game | healthcare |
                         iam | iap | identity | ids | iot | kms | logging |
                         memcache | metastore | ml | ml-engine | monitoring |
                         network-connectivity | network-management |
                         network-security | network-services | notebooks |
                         org-policies | organizations | policy-intelligence |
                         policy-troubleshoot | privateca | projects | pubsub |
                         recaptcha | recommender | redis | resource-manager |
                         resource-settings | run | scc | scheduler | secrets |
                         service-directory | services | source | spanner | sql |
                         tasks | topic | transcoder | transfer | workflows |
                         workspace-add-ons
  command may be         cheat-sheet | docker | feedback | help | info | init |
                         survey | version

For detailed information on this command and its flags, run:
  gcloud --help</pre>


<h3>初期設定</h3>

<p><code>gcloud init</code> を実行する。</p>

<pre class="code" data-lang="" data-unlink>$ gcloud init
Welcome! This command will take you through the configuration of gcloud.

Your current configuration has been set to: [default]

You can skip diagnostics next time by using the following flag:
  gcloud init --skip-diagnostics

Network diagnostic detects and fixes local network connection issues.
Checking network connection...done.
Reachability Check passed.
Network diagnostic passed (1/1 checks passed).

You must log in to continue. Would you like to log in (Y/n)?  y

You are authorizing gcloud CLI without access to a web browser. Please run the following command on a machine with a web browser and copy its output back here. Make sure the installed gcloud version is 372.0.0 or newer.

gcloud auth login --remote-bootstrap=&#34;https://accounts.google.com/o/oauth2/auth?response_type=code&amp;client_id=****&#34;


Enter the output of the above command:</pre>


<p>Web ブラウザを搭載した端末で <code>gcloud auth login --remote-bootstrap="****"</code> を実行せよ、というメッセージが出る。
ブラウザ経由で <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> アカウントにログインし、認証する必要があるらしい。なかなかに面倒。</p>

<p>別ターミナルを起動して、実行する。</p>

<pre class="code" data-lang="" data-unlink>% gcloud auth login --remote-bootstrap=&#34;https://accounts.google.com/o/oauth2/auth?response_type=code&amp;client_id=****&#34;
DO NOT PROCEED UNLESS YOU ARE BOOTSTRAPPING GCLOUD ON A TRUSTED MACHINE WITHOUT A WEB BROWSER AND THE ABOVE COMMAND WAS THE OUTPUT OF `gcloud auth login --no-browser` FROM THE TRUSTED MACHINE.

Proceed (y/N)?  y</pre>


<p>ブラウザが起動して <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> アカウントへのログインが求められる</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220417/20220417003640.png" width="1190" height="1200" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<p>利用したい <a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> アカウントへログインして、<a class="keyword" href="http://d.hatena.ne.jp/keyword/SDK">SDK</a> のからの認証を許可する。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220417/20220417003656.png" width="1157" height="1200" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<p>認証すると、以下のような出力になる。この <code>https://localhost:8085/?state=***</code> をコピーする。</p>

<pre class="code" data-lang="" data-unlink>Proceed (y/N)?  y

Your browser has been opened to visit:

    https://accounts.google.com/o/oauth2/auth?response_type=code&amp;client_id=****

Copy the following line back to the gcloud CLI waiting to continue the login flow. WARNING: THE FOLLOWING LINE ENABLES ACCESS TO YOUR GCP RESOURCES. ONLY COPY IT TO A MACHINE YOU TRUST AND RAN `gcloud auth login --no-browser` ON EARLIER.

https://localhost:8085/?state=***</pre>


<p>元端末の <code>Enter the output of the above command:</code> の部分に <code>https://localhost:8085/?state=***</code> を入力して実行する。</p>

<pre class="code" data-lang="" data-unlink>You must log in to continue. Would you like to log in (Y/n)?  y

You are authorizing gcloud CLI without access to a web browser. Please run the following command on a machine with a web browser and copy its output back here. Make sure the installed gcloud version is 372.0.0 or newer.

gcloud auth login --remote-bootstrap=&#34;https://accounts.google.com/o/oauth2/auth?response_type=code&amp;client_id=****&#34;


Enter the output of the above command: `https://localhost:8085/?state=***`</pre>


<p>プロジェクトの選択 or 新規作成を行う。</p>

<pre class="code" data-lang="" data-unlink>
Enter the output of the above command: https://localhost:8085/?state=***

You are logged in as: [foobar@example.com].

Pick cloud project to use:
 [1] hogehoge-prj
 [2] Enter a project ID
 [3] Create a new project
Please enter numeric choice or text value (must exactly match list item):</pre>


<p>ここでは既存プロジェクトを選択した。
続いてリージョンとゾーンの設定を行うか聞かれる。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Google">Google</a> Cloud では、例えば <code>us-east1-b</code> だと <code>us-east1</code> がリージョン、<code>b</code> の部分がゾーンとなる。</p>

<pre class="code" data-lang="" data-unlink>Your current project has been set to: [hogehoge-prj].

Do you want to configure a default Compute Region and Zone? (Y/n)? 
</pre>


<p><code>Y</code> を選択するとリージョンのリストが出てくる。</p>

<pre class="code" data-lang="" data-unlink>Do you want to configure a default Compute Region and Zone? (Y/n)?  Y

Which Google Compute Engine zone would you like to use as project default?
If you do not specify a zone via a command line flag while working with Compute Engine resources, the default is assumed.
 [1] us-east1-b
 [2] us-east1-c
 [3] us-east1-d
 [4] us-east4-c
 [5] us-east4-b
 [6] us-east4-a
 [7] us-central1-c
 [8] us-central1-a
 [9] us-central1-f
 [10] us-central1-b
 [11] us-west1-b
 [12] us-west1-c
 [13] us-west1-a
 [14] europe-west4-a
 [15] europe-west4-b
 [16] europe-west4-c
 [17] europe-west1-b
 [18] europe-west1-d
 [19] europe-west1-c
 [20] europe-west3-c
 [21] europe-west3-a
 [22] europe-west3-b
 [23] europe-west2-c
 [24] europe-west2-b
 [25] europe-west2-a
 [26] asia-east1-b
 [27] asia-east1-a
 [28] asia-east1-c
 [29] asia-southeast1-b
 [30] asia-southeast1-a
 [31] asia-southeast1-c
 [32] asia-northeast1-b
 [33] asia-northeast1-c
 [34] asia-northeast1-a
 [35] asia-south1-c
 [36] asia-south1-b
 [37] asia-south1-a
 [38] australia-southeast1-b
 [39] australia-southeast1-c
 [40] australia-southeast1-a
 [41] southamerica-east1-b
 [42] southamerica-east1-c
 [43] southamerica-east1-a
 [44] asia-east2-a
 [45] asia-east2-b
 [46] asia-east2-c
 [47] asia-northeast2-a
 [48] asia-northeast2-b
 [49] asia-northeast2-c
 [50] asia-northeast3-a
Did not print [39] options.
Too many options [89]. Enter &#34;list&#34; at prompt to print choices fully.
Please enter numeric choice or text value (must exactly match list item):  </pre>


<p>ここで 89 を入力すると、リージョンのみを選択できる。</p>

<pre class="code" data-lang="" data-unlink>Please enter numeric choice or text value (must exactly match list item):  89

Which Google Compute Engine region would you like to use as project default?
If you do not specify a region via a command line flag while working with Compute Engine resources, the default is assumed.
 [1] asia-east1
 [2] asia-east2
 [3] asia-northeast1
 [4] asia-northeast2
 [5] asia-northeast3
 [6] asia-south1
 [7] asia-south2
 [8] asia-southeast1
 [9] asia-southeast2
 [10] australia-southeast1
 [11] australia-southeast2
 [12] europe-central2
 [13] europe-north1
 [14] europe-west1
 [15] europe-west2
 [16] europe-west3
 [17] europe-west4
 [18] europe-west6
 [19] northamerica-northeast1
 [20] northamerica-northeast2
 [21] southamerica-east1
 [22] southamerica-west1
 [23] us-central1
 [24] us-east1
 [25] us-east4
 [26] us-west1
 [27] us-west2
 [28] us-west3
 [29] us-west4
 [30] Do not set default region
Please enter numeric choice or text value (must exactly match list item):  asia-northeast1

Your project default Compute Engine region has been set to [asia-northeast1].
You can change it by running [gcloud config set compute/region NAME].

Created a default .boto configuration file at [/home/dshimizu/.boto]. See this file and
[https://cloud.google.com/storage/docs/gsutil/commands/config] for more
information about configuring Google Cloud Storage.
Your Google Cloud SDK is configured and ready to use!

* Commands that require authentication will use foobar@example.com by default
* Commands will reference project `hogehoge-prj` by default
* Compute Engine commands will use region `asia-northeast1` by default
Run `gcloud help config` to learn how to change individual settings

This gcloud configuration is called [default]. You can create additional configurations if you work with multiple accounts and/or projects.
Run `gcloud topic configurations` to learn more.

Some things to try next:

* Run `gcloud --help` to see the Cloud Platform services you can interact with. And run `gcloud help COMMAND` to get help on any gcloud command.
* Run `gcloud topic --help` to learn about advanced features of the SDK like arg files and output formatting
* Run `gcloud cheat-sheet` to see a roster of go-to `gcloud` commands.</pre>


<h3>設定確認</h3>

<p><code>gcloud config list</code> で設定を出力できる。</p>

<pre class="code" data-lang="" data-unlink>% gcloud config list
[compute]
region = asia-northeast1
[core]
account = foorbar@example.com
disable_usage_reporting = True
project = hogehoge-prj

Your active configuration is: [default]</pre>


<p><code>gcloud projects list</code> で、認証しているアカウントで、自分が参加しているプロジェクトを出力できる。</p>

<pre class="code" data-lang="" data-unlink>% gcloud projects list
PROJECT_ID      NAME            PROJECT_NUMBER
hogehoge-prj  dhogehoge-prj  5**********3
test-prj     test-prj     5**********6</pre>


<h3>プロジェクト切り替え</h3>

<p><code>gcloud config set project {Project ID}</code> で、プロジェクトを切り替えられる。このとき、指定するのはプロジェクト名ではなく、プロジェクトIDである。</p>

<pre class="code" data-lang="" data-unlink>% gcloud config set project test-prj
Updated property [core/project].</pre>




<pre class="code" data-lang="" data-unlink>% gcloud config list
[compute]
region = asia-northeast1
[core]
account = foorbar@example.com
disable_usage_reporting = True
project = test-prj

Your active configuration is: [default]</pre>


<p><code>gcloud config set account アカウント名</code> でアカウントを切り替える。</p>

<pre class="code" data-lang="" data-unlink>% gcloud config set account test@example.com
Updated property [core/account].</pre>


<p>変更後の出力。</p>

<pre class="code" data-lang="" data-unlink>% gcloud config list
[compute]
region = asia-northeast1
[core]
account = test@example.com
disable_usage_reporting = True
project = test-prj

Your active configuration is: [default]</pre>


<h3>再認証</h3>

<p>この状態だと一部オペレーションをしようとすると、以下のようになる。</p>

<pre class="code" data-lang="" data-unlink>$  gcloud config set compute/zone us-central1-a
ERROR: (gcloud.config.set) Your current active account [test@example.com] does not have any valid credentials
Please run:

  $ gcloud auth login

to obtain new credentials.

For service account, please activate it first:

  $ gcloud auth activate-service-account ACCOUNT</pre>


<p>こうなると再認証が必要となるので、 <code>gcloud init</code> 時と同様に <code>gcloud auth</code> を実行する。</p>

<pre class="code" data-lang="" data-unlink>% gcloud auth login
You are authorizing gcloud CLI without access to a web browser. Please run the following command on a machine with a web browser and copy its output back here. Make sure the installed gcloud version is 372.0.0 or newer.

gcloud auth login --remote-bootstrap=&#34;https://accounts.google.com/o/oauth2/auth?response_type=code&amp;client_id=********&#34;


Enter the output of the above command:</pre>


<p>また別途 <code>gcloud auth login --remote-bootstrap=</code> を実行しろ、と言われるので、別ターミナルで実行する。</p>

<pre class="code" data-lang="" data-unlink>% gcloud auth login --remote-bootstrap=&#34;https://accounts.google.com/o/oauth2/auth?response_type=code&amp;client_id=********&#34;
DO NOT PROCEED UNLESS YOU ARE BOOTSTRAPPING GCLOUD ON A TRUSTED MACHINE WITHOUT A WEB BROWSER AND THE ABOVE COMMAND WAS THE OUTPUT OF `gcloud auth login --no-browser` FROM THE TRUSTED MACHINE.

Proceed (y/N)?  y</pre>


<p><code>Proceed (y/N)?</code> で <code>y</code> を選択するとまたブラウザ経由で認証画面が出るので許可するとまた以下の出力を得られる。</p>

<pre class="code" data-lang="" data-unlink>Your browser has been opened to visit:

    https://accounts.google.com/o/oauth2/auth?response_type=code&amp;client_id=********

Copy the following line back to the gcloud CLI waiting to continue the login flow. WARNING: The following line enables access to your Google Cloud resources. Only copy it to the trusted machine that you ran the `gcloud auth login --no-browser` command on earlier.

https://localhost:8085/?state=****</pre>


<p>これを先程の <code>Enter the output of the above command:</code> に貼り付ければまた認証できる。</p>

<pre class="code" data-lang="" data-unlink>Enter the output of the above command: https://localhost:8085/?state=********

You are now logged in as [test@example.com].
Your current project is [test-prj].  You can change this setting by running:
  $ gcloud config set project PROJECT_ID</pre>


<h3>ゾーンの設定</h3>

<pre class="code" data-lang="" data-unlink>% gcloud config set compute/zone us-central1-a
Updated property [compute/zone].</pre>




<pre class="code" data-lang="" data-unlink>% gcloud config list
[compute]
region = asia-northeast1
zone = us-central1-a
[core]
account = test@example.com
disable_usage_reporting = True
project = test-prj

Your active configuration is: [default]</pre>


<p>設定を削除する場合は <code>gcloud config unset</code> を使う、</p>

<pre class="code" data-lang="" data-unlink>$ gcloud config unset compute/zone
Unset property [compute/zone].</pre>


<p>この場合 zone の指定が消える。</p>

<pre class="code" data-lang="" data-unlink>% gcloud config list
[compute]
region = asia-northeast1
[core]
account = test@example.com
disable_usage_reporting = True
project = test-prj

Your active configuration is: [default]</pre>


<h3>リージョンの設定</h3>

<p><code>gcloud config set compute/region</code> でリージョンの設定を変える</p>

<pre class="code" data-lang="" data-unlink>% gcloud config set compute/region us-central1
Updated property [compute/region].</pre>


<p>変わった。</p>

<pre class="code" data-lang="" data-unlink>% gcloud config list
[compute]
region = asia-northeast1
[core]
account = test@example.com
disable_usage_reporting = True
project = test-prj

Your active configuration is: [default]</pre>


<h3>configurations の新規作成</h3>

<p>今までのはconfigurationsの新規作成と、作成したconfigurationsの更新だった。
これは default 設定として保存されている。</p>

<pre class="code" data-lang="" data-unlink>% gcloud config configurations list
NAME     IS_ACTIVE  ACCOUNT             PROJECT         COMPUTE_DEFAULT_ZONE  COMPUTE_DEFAULT_REGION
default  True       test@example.com   test-prj                        asia-northeast1</pre>


<p>別なconfigurationsを作成してみる。
<code>gcloud config configurations create "config-name"</code> で作成する。</p>

<pre class="code" data-lang="" data-unlink>$ gcloud config configurations create dev-conf
Created [dev-conf].
Activated [dev-conf].</pre>


<p>自動的に作成した configuration がActive になって切り替わる。</p>

<pre class="code" data-lang="" data-unlink>$ gcloud config configurations list
NAME         IS_ACTIVE  ACCOUNT             PROJECT         COMPUTE_DEFAULT_ZONE  COMPUTE_DEFAULT_REGION
default      False      test@example.com   test-prj                        asia-northeast1
dev-conf  True</pre>


<p>新しい configuration にプロジェクト、 アカウント、ゾーン、リージョンを設定する。</p>

<pre class="code" data-lang="" data-unlink>% gcloud config set project hogehoge-prj
Updated property [core/project].

% gcloud config set account foorbar@example.com
Updated property [core/account].

% gcloud config set compute/zone asia-northeast1-a
Updated property [compute/zone].

% gcloud config set compute/region asia-northeast1
Updated property [compute/region].</pre>


<p>確認してみる。</p>

<pre class="code" data-lang="" data-unlink>% gcloud config list
[compute]
region = asia-northeast1
zone = asia-northeast1-a
[core]
account = foorbar@example.com
disable_usage_reporting = True
project = hogehoge-prj

Your active configuration is: [dev-conf]</pre>


<p>configuration はこうなる。</p>

<pre class="code" data-lang="" data-unlink>% gcloud config configurations list
NAME     IS_ACTIVE  ACCOUNT              PROJECT         COMPUTE_DEFAULT_ZONE  COMPUTE_DEFAULT_REGION
default  False      test@example.com   test-prj                        asia-northeast1
dev-conf  True       foorbar@example.com  hogehoge-prj  asia-northeast1-a     asia-northeast1</pre>


<h3>configuration の切り替え</h3>

<p><code>gcloud config configurations activate "config-name"</code> で切り替える。</p>

<pre class="code" data-lang="" data-unlink>% gcloud config configurations activate default
Activated [default].</pre>


<p>切り替わった。</p>

<pre class="code" data-lang="" data-unlink>% gcloud config configurations list
NAME     IS_ACTIVE  ACCOUNT              PROJECT         COMPUTE_DEFAULT_ZONE  COMPUTE_DEFAULT_REGION
default  True      test@example.com   test-prj                        asia-northeast1
dev-conf  False       foorbar@example.com  hogehoge-prj  asia-northeast1-a     asia-northeast1</pre>


<h3>その他</h3>

<p><code>gcloud config configurations rename</code> や <code>gcloud config configurations delete</code> で configuration の名称を変えたり、削除したりできる。</p>

<h2>設定ファイル</h2>

<p>ちなみに設定ファイルは <code>$HOME/.config/gcloud/</code> にできている。</p>

<pre class="code" data-lang="" data-unlink>% ls -lth .config/gcloud/
total 12K
-rw-r--r-- 1 dshimizu sysadmin   0 Feb 17 08:28 config_sentinel
-rw------- 1 dshimizu sysadmin 12K Feb 17 08:28 access_tokens.db
-rw------- 1 dshimizu sysadmin   5 Feb 17 08:22 gce
-rw-r--r-- 1 dshimizu sysadmin   7 Feb 17 08:22 active_config
drwxr-xr-x 2 dshimizu sysadmin   4 Feb 17 08:22 configurations
-rw------- 1 dshimizu sysadmin 12K Feb 17 06:37 credentials.db
drwxr-xr-x 4 dshimizu sysadmin   4 Feb 17 06:37 legacy_credentials
drwxr-xr-x 4 dshimizu sysadmin   4 Feb 17 04:27 logs</pre>


<p>作成した configuration は以下にある。</p>

<pre class="code" data-lang="" data-unlink>% ls -lth .config/gcloud/configurations
total 9.0K
-rw-r--r-- 1 dshimizu sysadmin 124 Feb 17 08:28 config_dev-conf
-rw-r--r-- 1 dshimizu sysadmin  98 Feb 17 07:24 config_default</pre>


<h2>まとめ</h2>

<p>gcloud コマンドを使って config の設定をしてみた。
慣れると良いが、少し使わないでいるとすぐ忘れる。</p>

<h2>参考</h2>

<ul>
<li><a href="https://cloud.google.com/sdk/docs?hl=ja">Cloud SDK &#x306E;&#x30C9;&#x30AD;&#x30E5;&#x30E1;&#x30F3;&#x30C8; &nbsp;|&nbsp; Google Cloud</a></li>
</ul>


