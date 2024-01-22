---
title: "macOS へ Lima をインストールして Docker を動かしてみたときのメモ"
date: 2023-10-31
slug: "macOS へ Lima をインストールして Docker を動かしてみたときのメモ"
category: blog
tags: [Lima,Docker,Container]
---
<h2 id="はじめに">はじめに</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/macOS">macOS</a> 上で Docker を実行するには Docker Desktop を使うのが一番楽かと思いますが、他のやり方として Lima を使ってやってみたのでメモ書きです。</p>

<h2 id="Lima">Lima</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/macOS">macOS</a> で containerd を広めることを目的としたプロジェクトであるらしいです。
containerd, nerdctl が入った<a class="keyword" href="https://d.hatena.ne.jp/keyword/Linux">Linux</a><a class="keyword" href="https://d.hatena.ne.jp/keyword/%B2%BE%C1%DB%A5%DE%A5%B7%A5%F3">仮想マシン</a>を起動できます。(Docker の機能の1つに containerd ランタイムを動かすというのもありますが先駆者的な位置付けであったのもあってか独自の仕様が利用されているらしいのに対して、共通インターフェースとなる CRI が登場し、また containerd の機能が充実してきたことで、CRI に則った nerdctl という containerd ランタイムを動かすためのソフトウェアが登場した、といった感じのようです)</p>

<ul>
<li><a href="https://github.com/lima-vm/lima">GitHub - lima-vm/lima: Linux virtual machines, with a focus on running containers</a></li>
</ul>


<p>ただ、Lima でも Docker も利用可能であるようです。</p>

<h2 id="macOS-へのセットアップ"><a class="keyword" href="https://d.hatena.ne.jp/keyword/macOS">macOS</a> へのセットアップ</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/macOS">macOS</a> へセットアップしてみます。</p>

<h3 id="Lima-インストール">Lima インストール</h3>

<p>Lima をインストールします。</p>

<pre class="code shell" data-lang="shell" data-unlink>% brew install lima</pre>


<p>2023/10/30 時点ではバージョンは下記でした。</p>

<pre class="code shell" data-lang="shell" data-unlink>% lima -v
limactl version 0.18.0</pre>


<h3 id="limactl-コマンドを触ってみる">limactl コマンドを触ってみる</h3>

<p>limactl というコマンドが提供されています。
ヘルプを見てみます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% limactl help
Lima: Linux virtual machines

Usage:
  limactl [command]

Examples:
  Start the default instance:
  $ limactl start

  Open a shell:
  $ lima

  Run a container:
  $ lima nerdctl run -d --name nginx -p 8080:80 nginx:alpine

  Stop the default instance:
  $ limactl stop

  See also template YAMLs: /usr/local/share/lima/templates

Available Commands:
  completion    Generate the autocompletion script for the specified shell
  copy          Copy files between host and guest
  create        Create an instance of Lima
  delete        Delete an instance of Lima.
  disk          Lima disk management
  edit          Edit an instance of Lima
  factory-reset Factory reset an instance of Lima
  help          Help about any command
  info          Show diagnostic information
  list          List instances of Lima.
  protect       Protect an instance to prohibit accidental removal
  prune         Prune garbage objects
  shell         Execute shell in Lima
  show-ssh      Show the ssh command line (DEPRECATED; use `ssh -F` instead)
  snapshot      Manage instance snapshots
  start         Start an instance of Lima
  stop          Stop an instance
  sudoers       Generate the content of the /etc/sudoers.d/lima file
  unprotect     Unprotect an instance
  validate      Validate YAML files

Flags:
      --debug              debug mode
  -h, --help               help for limactl
      --log-level string   Set the logging level [trace, debug, info, warn, error]
      --tty                Enable TUI interactions such as opening an editor. Defaults to true when stdout is a terminal. Set to false for automation. (default true)
  -v, --version            version for limactl

Use &#34;limactl [command] --help&#34; for more information about a command.</pre>


<h2 id="Lima-VM-の操作">Lima <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a> の操作</h2>

<p><code>limactl start</code> を実行すると、そのまま default という名前の <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a> が起動します。</p>

<pre class="code shell" data-lang="shell" data-unlink>% limactl start</pre>


<p>特定の名前の <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a> を作成するには、<code>start</code> の後に名前を指定します。</p>

<pre class="code shell" data-lang="shell" data-unlink>% limactl start testvm</pre>


<p>いくつか対話的なやり取りが発生しますが、とりあえずデフォルトでやります。</p>

<pre class="code shell" data-lang="shell" data-unlink>INFO[0000] Creating an instance &#34;testvm&#34; from template://default (Not from template://testvm)
WARN[0000] This form is deprecated. Use `limactl create --name=testvm template://default` instead
? Creating an instance &#34;testvm&#34;  [Use arrows to move, type to filter]
&gt; Proceed with the current configuration
  Open an editor to review or modify the current configuration
  Choose another template (docker, podman, archlinux, fedora, ...)
  Exit</pre>


<p>しばらく待つと <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a> が作成され、起動します。
<code>limactl ls</code> コマンドで作成された <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a> のリストを見ることができます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% limactl ls
NAME      STATUS     SSH                VMTYPE    ARCH      CPUS    MEMORY    DISK      DIR
default    Running    127.0.0.1:60022    qemu      x86_64    4       4GiB      100GiB    ~/.lima/default
testvm     Running    127.0.0.1:50751    qemu      x86_64    4       4GiB      100GiB    ~/.lima/testvm</pre>


<p><code>limactl shell</code> で Lima <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a> へ接続します。</p>

<pre class="code shell" data-lang="shell" data-unlink>% limactl shell testvm
lima@lima-testvm:/Users/dshimizu$ id
uid=502(lima) gid=1000(lima) groups=1000(lima)</pre>


<p>default の <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a> の場合は <code>lima</code> コマンドで <code>limactl shell default</code> 相当のことができます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% lima
lima@lima-default:/Users/dshimizu$</pre>


<p><code>/usr/local/bin/lima</code> では <code>limactl shell default</code> が実行されるような形でした。</p>

<pre class="code shell" data-lang="shell" data-unlink>#!/bin/sh
set -eu
: &#34;${LIMA_INSTANCE:=default}&#34;
: &#34;${LIMA_SHELL:=}&#34;
: &#34;${LIMA_WORKDIR:=}&#34;
: &#34;${LIMACTL:=limactl}&#34;

if [ &#34;$#&#34; -eq 1 ]; then
  if [ &#34;$1&#34; = &#34;-h&#34; ] || [ &#34;$1&#34; = &#34;--help&#34; ]; then
    base=&#34;$(basename &#34;$0&#34;)&#34;
    echo &#34;Usage: ${base} [COMMAND...]&#34;
    echo
    echo &#34;${base} is an alias for \&#34;${LIMACTL} shell ${LIMA_INSTANCE}\&#34;.&#34;
    echo &#34;The instance name (\&#34;${LIMA_INSTANCE}\&#34;) can be changed by specifying \$LIMA_INSTANCE.&#34;
    echo
    echo &#34;The shell and initial workdir inside the instance can be specified via \$LIMA_SHELL&#34;
    echo &#34;and \$LIMA_WORKDIR.&#34;
    echo
    echo &#34;See \`${LIMACTL} shell --help\` for further information.&#34;
    exit 0
  elif [ &#34;$1&#34; = &#34;-v&#34; ] || [ &#34;$1&#34; = &#34;--version&#34; ]; then
    exec &#34;$LIMACTL&#34; &#34;$@&#34;
  fi
fi

set - &#34;$LIMA_INSTANCE&#34; &#34;$@&#34;
if [ -n &#34;${LIMA_SHELL}&#34; ]; then
  set - --shell &#34;$LIMA_SHELL&#34; &#34;$@&#34;
fi
if [ -n &#34;${LIMA_WORKDIR}&#34; ]; then
  set - --workdir &#34;$LIMA_WORKDIR&#34; &#34;$@&#34;
fi
# Avoid converting paths with MSYS2
MSYS2_ARG_CONV_EXCL=&#34;*&#34;
export MSYS2_ARG_CONV_EXCL
exec &#34;$LIMACTL&#34; shell &#34;$@&#34;</pre>


<p>設定ファイルは <code>~/.lima/</code> に配置されるようです。
<a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a>名の <code>lima.yaml</code> が <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a> の設定ファイルのようです。</p>

<pre class="code shell" data-lang="shell" data-unlink>% tree -L 2 ~/.lima/
/Users/daisukeshimizu/.lima/
├── _config
│   ├── networks.yaml
│   ├── user
│   └── user.pub
├── default
│   ├── basedisk
│   ├── cidata.iso
│   ├── diffdisk
│   ├── ga.sock
│   ├── ha.pid
│   ├── ha.sock
│   ├── ha.stderr.log
│   ├── ha.stdout.log
│   ├── lima.yaml
│   ├── qemu.pid
│   ├── qmp.sock
│   ├── serial.log
│   ├── serial.sock
│   ├── serialv.log
│   ├── serialv.sock
│   ├── ssh.config
│   └── ssh.sock
└── testvm
    ├── basedisk
    ├── cidata.iso
    ├── diffdisk
    ├── ga.sock
    ├── ha.pid
    ├── ha.sock
    ├── ha.stderr.log
    ├── ha.stdout.log
    ├── lima.yaml
    ├── qemu.pid
    ├── qmp.sock
    ├── serial.log
    ├── serial.sock
    ├── serialv.log
    ├── serialv.sock
    ├── ssh.config
    └── ssh.sock</pre>


<h2 id="Docker-を使ってみる">Docker を使ってみる</h2>

<p>Lima の <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a> に Docker Server をインストールし、<a class="keyword" href="https://d.hatena.ne.jp/keyword/macOS">macOS</a> を Docker クライアントをインストールして、少し触ってみます。</p>

<h3 id="Docker-クライアントのセットアップ">Docker クライアントのセットアップ</h3>

<h4 id="macOS-へ-Docker-CLI-インストール"><a class="keyword" href="https://d.hatena.ne.jp/keyword/macOS">macOS</a> へ Docker <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> インストール</h4>

<p> Docker <a class="keyword" href="https://d.hatena.ne.jp/keyword/CLI">CLI</a> (クライアント)をインストールします。</p>

<pre class="code shell" data-lang="shell" data-unlink>% curl -OL https://download.docker.com/mac/static/stable/x86_64/docker-24.0.7.tgz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 16.3M  100 16.3M    0     0  4157k      0  0:00:04  0:00:04 --:--:-- 4167k</pre>


<p>バージョンを見てみます。この時点では Server の情報は出てこず、 Client の情報のみ出てきます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% docker version
Client:
 Cloud integration: v1.0.29
 Version:           20.10.21
 API version:       1.41
 Go version:        go1.18.7
 Git commit:        baeda1f
 Built:             Tue Oct 25 18:01:18 2022
 OS/Arch:           darwin/amd64
 Context:           default
 Experimental:      true</pre>


<h4 id="macOS-へ-Docker-Compose-Plugin-インストール"><a class="keyword" href="https://d.hatena.ne.jp/keyword/macOS">macOS</a> へ Docker Compose Plugin インストール</h4>

<p>Docker Compose Plugin をインストールします。</p>

<pre class="code shell" data-lang="shell" data-unlink>% curl -SL https://github.com/docker/compose/releases/download/v2.2.2/docker-compose-darwin-x86_64 -o ~/.docker/cli-plugins/docker-compose
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 26.0M  100 26.0M    0     0  7913k      0  0:00:03  0:00:03 --:--:-- 9667k</pre>


<p>実行権限を付与します。</p>

<pre class="code shell" data-lang="shell" data-unlink>% chmod +x ~/.docker/cli-plugins/docker-compose</pre>




<pre class="code shell" data-lang="shell" data-unlink>% docker compose version
Docker Compose version v2.13.0</pre>


<h3 id="Docker-Server-のセットアップ">Docker Server のセットアップ</h3>

<h4 id="Lima-VM-への-Docker-インストール">Lima <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a> への Docker インストール</h4>

<p><code>get.docker.com</code> に<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>があるので、 Lima で起動した <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a> にパイプで渡して実行できるので実行します。</p>

<pre class="code shell" data-lang="shell" data-unlink>% curl -fsSL https://get.docker.com | limactl shell testvm</pre>


<p>Lima <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a> で Docker を設定して起動します。</p>

<pre class="code shell" data-lang="shell" data-unlink>% limactl shell testvm
lima@lima-testvm:/Users/dshimizu$</pre>


<p><code>/etc/docker/daemon.json</code>を作成します。</p>

<pre class="code shell" data-lang="shell" data-unlink>lima@lima-testvm:/Users/dshimizu$ sudo vim /etc/docker/daemon.json</pre>


<p>以下のように記述します。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>
    &quot;<span class="synStatement">hosts</span>&quot;: <span class="synSpecial">[</span>
        &quot;<span class="synConstant">tcp://127.0.0.1:2375</span>&quot;,
        &quot;<span class="synConstant">unix:///var/run/docker.sock</span>&quot;
    <span class="synSpecial">]</span>
<span class="synSpecial">}</span>
</pre>


<p><code>/etc/systemd/system/docker.service.d/override.conf</code> を作成します。</p>

<pre class="code shell" data-lang="shell" data-unlink>lima@lima-testvm:/Users/dshimizu$ sudo vim /etc/docker/daemon.json</pre>


<p>以下のように記述します。</p>

<pre class="code text" data-lang="text" data-unlink>[Service]
ExecStart=  # この記述も必要
ExecStart=/usr/bin/dockerd   # 上書きする設定</pre>




<pre class="code shell" data-lang="shell" data-unlink>lima@lima-testvm:/Users/dshimizu$ sudo systemctl daemon-reload
lima@lima-testvm:/Users/dshimizu$ sudo systemctl start docker
lima@lima-testvm:/Users/dshimizu$  sudo docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES</pre>


<p>Lima <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a>をDocker ホストとして指定するために <code>DOCKER_HOST</code> <a class="keyword" href="https://d.hatena.ne.jp/keyword/%B4%C4%B6%AD%CA%D1%BF%F4">環境変数</a>を指定します。</p>

<pre class="code shell" data-lang="shell" data-unlink>% echo  &#34;export DOCKER_HOST=&#39;tcp://127.0.0.1:2375&#39;&#34; &gt;&gt; $HOME/.profile
% source $HOME/.profile</pre>


<p>ここでは <a class="keyword" href="https://d.hatena.ne.jp/keyword/TCP">TCP</a> を使ってますが、下記のテンプレートに相当することができれば<a class="keyword" href="https://d.hatena.ne.jp/keyword/Unix">Unix</a><a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C9%A5%E1%A5%A4%A5%F3">ドメイン</a>ソケットでも大丈夫なようです。</p>

<ul>
<li><a href="https://github.com/lima-vm/lima/blob/master/examples/docker.yaml">lima/examples/docker.yaml at master &middot; lima-vm/lima &middot; GitHub</a></li>
</ul>


<p>これで <a class="keyword" href="https://d.hatena.ne.jp/keyword/macOS">macOS</a> から Lima <a class="keyword" href="https://d.hatena.ne.jp/keyword/VM">VM</a> の Docker に接続できるようになりました。</p>

<pre class="code shell" data-lang="shell" data-unlink>% docker version
Client:
 Cloud integration: v1.0.29
 Version:           20.10.21
 API version:       1.41
 Go version:        go1.18.7
 Git commit:        baeda1f
 Built:             Tue Oct 25 18:01:18 2022
 OS/Arch:           darwin/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          24.0.7
  API version:      1.43 (minimum version 1.12)
  Go version:       go1.20.10
  Git commit:       311b9ff
  Built:            Thu Oct 26 09:07:58 2023
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v1.7.7
  GitCommit:        8c087663b0233f6e6e2f4515cee61d49f14746a8
 runc:
  Version:          1.1.9
  GitCommit:        v1.1.9-0-gccaecfcb
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0</pre>


<h2 id="コンテナ起動">コンテナ起動</h2>

<p>コンテナを起動してみます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% docker run --name some-nginx -d -p 8080:80 nginx</pre>


<h2 id="まとめ">まとめ</h2>

<p>Lima を触って、Docker コンテナを実行してみました。</p>

<p>nerdctl はまだわかっていないので次はその辺りを触ってみようかと思います。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://github.com/lima-vm/lima">GitHub - lima-vm/lima: Linux virtual machines, with a focus on running containers</a></li>
<li><a href="https://medium.com/nttlabs/docker-to-containerd-4f3a56e6f2b6">Docker&#x304B;&#x3089;containerd&#x3078;&#x306E;&#x79FB;&#x884C; (NTT Tech Conference 2022&#x767A;&#x8868;&#x30EC;&#x30DD;&#x30FC;&#x30C8;) | by Akihiro Suda | nttlabs | Medium</a></li>
<li><a href="https://qiita.com/nvsofts/items/529e422bb8a326401c39">systemd&#x3067;&#x65E2;&#x5B58;&#x306E;unit&#x3092;&#x7DE8;&#x96C6;&#x3059;&#x308B;2&#x3064;&#x306E;&#x65B9;&#x6CD5; #Linux - Qiita</a></li>
<li><a href="https://qiita.com/yoichiwo7/items/44aff38674134ad87da3">Docker Desktop for Mac&#x306E;&#x5B9F;&#x7528;&#x7684;&#x306A;&#x4EE3;&#x66FF;&#x624B;&#x6BB5;: lima + Docker #Docker - Qiita</a></li>
</ul>


