---
title: "Docker コンテナ内で Docker デーモンを起動しようとするとどうなるかを見て、Docker outside of Docker を動かしてみた"
date: 2022-07-10
slug: "Docker コンテナ内で Docker デーモンを起動しようとするとどうなるかを見て、Docker outside of Docker を動かしてみた"
category: blog
tags: [Tech]
---
<h2 id="はじめに">はじめに</h2>

<p>CI を動かす時、CI 側を Docker コンテナで動かしている場合、自分のアプリケーションも Docker コンテナで動かしてビルドする、といった場合に一工夫必要になる。</p>

<p>この場合の対応方法として、Docker コンテナの中で Docker を利用できるようにする、といったことができる。
以下の2つの方法がある。</p>

<ul>
<li>Docker コンテナの中で Docker デーモンを動かす (Docker in Docker)</li>
<li>ホストの Docker デーモンのソケットファイルを、各 Docker コンテナで共有する (Docker outside of Docker)</li>
</ul>


<p>Docker in Docker は色々問題点があり<a href="#f-e7145a74" name="fn-e7145a74" title="https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/">*1</a>、やるにも結構手間がかかるので、 Docker outside of Docker をやってみる。</p>

<h2 id="環境">環境</h2>

<pre class="code" data-lang="" data-unlink>% docker -v
Docker version 20.10.17, build 100c701</pre>


<h2 id="何もせず-Docker-から-Docker-を使おうとする場合">何もせず Docker から Docker を使おうとする場合</h2>

<h3 id="ホスト側の操作">ホスト側の操作</h3>

<pre class="code" data-lang="" data-unlink>% docker container run -it --rm --name dood-test -v /var/run/docker.sock:/var/run/docker.sock ubuntu</pre>


<h3 id="コンテナ側の操作">コンテナ側の操作</h3>

<p>コンテナ側にも docker をインストールする。ちゃんとやる場合は Docker 公式のものをインストールするところだけど、とりあえず動作確認のため <a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> のパッケージのものをインストールする。</p>

<pre class="code" data-lang="" data-unlink>root@20b7df14b340:/ # apt update &amp;&amp; apt install -y docker.io</pre>


<p>Docker デーモンにアクセスできない、と言われる。</p>

<pre class="code" data-lang="" data-unlink>root@221eb7bbf979:/# docker ps
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?</pre>


<p>Docker コンテナ内で Docker デーモンを起動しようとすると、ストレージドライバやネットワーク周りの操作に失敗し、デーモンの起動に失敗する。この辺りの仕組みについてはまだよくわかっていないが、コンテナから<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AB%A1%BC%A5%CD%A5%EB">カーネル</a>に関係する部分の操作ができないためと思う。</p>

<pre class="code" data-lang="" data-unlink>root@221eb7bbf979:/# dockerd
INFO[2022-07-10T13:19:08.959606477+09:00] Starting up
INFO[2022-07-10T13:19:08.960336841+09:00] libcontainerd: started new containerd process  pid=4080
INFO[2022-07-10T13:19:08.960358071+09:00] parsed scheme: &#34;unix&#34;                         module=grpc
INFO[2022-07-10T13:19:08.960364727+09:00] scheme &#34;unix&#34; not registered, fallback to default scheme  module=grpc
INFO[2022-07-10T13:19:08.960379332+09:00] ccResolverWrapper: sending update to cc: {[{unix:///var/run/docker/containerd/containerd.sock  &lt;nil&gt; 0 &lt;nil&gt;}] &lt;nil&gt; &lt;nil&gt;}  module=grpc
INFO[2022-07-10T13:19:08.960385097+09:00] ClientConn switching balancer to &#34;pick_first&#34;  module=grpc
WARN[0000] deprecated version : `1`, please switch to version `2`
INFO[2022-07-10T13:19:08.973892503+09:00] starting containerd                           revision= version=&#34;1.5.9-0ubuntu1~20.04.4&#34;
INFO[2022-07-10T13:19:08.989213285+09:00] loading plugin &#34;io.containerd.content.v1.content&#34;...  type=io.containerd.content.v1
INFO[2022-07-10T13:19:08.989271418+09:00] loading plugin &#34;io.containerd.snapshotter.v1.aufs&#34;...  type=io.containerd.snapshotter.v1
INFO[2022-07-10T13:19:08.989360114+09:00] loading plugin &#34;io.containerd.snapshotter.v1.btrfs&#34;...  type=io.containerd.snapshotter.v1
INFO[2022-07-10T13:19:08.989539433+09:00] skip loading plugin &#34;io.containerd.snapshotter.v1.btrfs&#34;...  error=&#34;path /var/lib/docker/containerd/daemon/io.containerd.snapshotter.v1.btrfs (overlay) must be a btrfs filesystem to be used with the btrfs snapshotter: skip plugin&#34; type=io.containerd.snapshotter.v1
INFO[2022-07-10T13:19:08.989553182+09:00] loading plugin &#34;io.containerd.snapshotter.v1.devmapper&#34;...  type=io.containerd.snapshotter.v1
WARN[2022-07-10T13:19:08.989563685+09:00] failed to load plugin io.containerd.snapshotter.v1.devmapper  error=&#34;devmapper not configured&#34;
INFO[2022-07-10T13:19:08.989570146+09:00] loading plugin &#34;io.containerd.snapshotter.v1.native&#34;...  type=io.containerd.snapshotter.v1
INFO[2022-07-10T13:19:08.989616642+09:00] loading plugin &#34;io.containerd.snapshotter.v1.overlayfs&#34;...  type=io.containerd.snapshotter.v1
INFO[2022-07-10T13:19:08.989727550+09:00] loading plugin &#34;io.containerd.snapshotter.v1.zfs&#34;...  type=io.containerd.snapshotter.v1
INFO[2022-07-10T13:19:08.989808917+09:00] skip loading plugin &#34;io.containerd.snapshotter.v1.zfs&#34;...  error=&#34;path /var/lib/docker/containerd/daemon/io.containerd.snapshotter.v1.zfs must be a zfs filesystem to be used with the zfs snapshotter: skip plugin&#34; type=io.containerd.snapshotter.v1
INFO[2022-07-10T13:19:08.989819978+09:00] loading plugin &#34;io.containerd.metadata.v1.bolt&#34;...  type=io.containerd.metadata.v1
WARN[2022-07-10T13:19:08.989846668+09:00] could not use snapshotter devmapper in metadata plugin  error=&#34;devmapper not configured&#34;
INFO[2022-07-10T13:19:08.989854445+09:00] metadata content store policy set             policy=shared
INFO[2022-07-10T13:19:09.005693666+09:00] loading plugin &#34;io.containerd.differ.v1.walking&#34;...  type=io.containerd.differ.v1
INFO[2022-07-10T13:19:09.005709185+09:00] loading plugin &#34;io.containerd.gc.v1.scheduler&#34;...  type=io.containerd.gc.v1
INFO[2022-07-10T13:19:09.005750892+09:00] loading plugin &#34;io.containerd.service.v1.introspection-service&#34;...  type=io.containerd.service.v1
INFO[2022-07-10T13:19:09.005776234+09:00] loading plugin &#34;io.containerd.service.v1.containers-service&#34;...  type=io.containerd.service.v1
INFO[2022-07-10T13:19:09.005785569+09:00] loading plugin &#34;io.containerd.service.v1.content-service&#34;...  type=io.containerd.service.v1
INFO[2022-07-10T13:19:09.005792849+09:00] loading plugin &#34;io.containerd.service.v1.diff-service&#34;...  type=io.containerd.service.v1
INFO[2022-07-10T13:19:09.005801751+09:00] loading plugin &#34;io.containerd.service.v1.images-service&#34;...  type=io.containerd.service.v1
INFO[2022-07-10T13:19:09.005809527+09:00] loading plugin &#34;io.containerd.service.v1.leases-service&#34;...  type=io.containerd.service.v1
INFO[2022-07-10T13:19:09.005817730+09:00] loading plugin &#34;io.containerd.service.v1.namespaces-service&#34;...  type=io.containerd.service.v1
INFO[2022-07-10T13:19:09.005826105+09:00] loading plugin &#34;io.containerd.service.v1.snapshots-service&#34;...  type=io.containerd.service.v1
INFO[2022-07-10T13:19:09.005834033+09:00] loading plugin &#34;io.containerd.runtime.v1.linux&#34;...  type=io.containerd.runtime.v1
INFO[2022-07-10T13:19:09.005910278+09:00] loading plugin &#34;io.containerd.runtime.v2.task&#34;...  type=io.containerd.runtime.v2
INFO[2022-07-10T13:19:09.005983203+09:00] loading plugin &#34;io.containerd.monitor.v1.cgroups&#34;...  type=io.containerd.monitor.v1
INFO[2022-07-10T13:19:09.006182150+09:00] loading plugin &#34;io.containerd.service.v1.tasks-service&#34;...  type=io.containerd.service.v1
INFO[2022-07-10T13:19:09.006202426+09:00] loading plugin &#34;io.containerd.internal.v1.restart&#34;...  type=io.containerd.internal.v1
INFO[2022-07-10T13:19:09.006230819+09:00] loading plugin &#34;io.containerd.grpc.v1.containers&#34;...  type=io.containerd.grpc.v1
INFO[2022-07-10T13:19:09.006240320+09:00] loading plugin &#34;io.containerd.grpc.v1.content&#34;...  type=io.containerd.grpc.v1
INFO[2022-07-10T13:19:09.006250691+09:00] loading plugin &#34;io.containerd.grpc.v1.diff&#34;...  type=io.containerd.grpc.v1
INFO[2022-07-10T13:19:09.006258760+09:00] loading plugin &#34;io.containerd.grpc.v1.events&#34;...  type=io.containerd.grpc.v1
INFO[2022-07-10T13:19:09.006266514+09:00] loading plugin &#34;io.containerd.grpc.v1.healthcheck&#34;...  type=io.containerd.grpc.v1
INFO[2022-07-10T13:19:09.006274823+09:00] loading plugin &#34;io.containerd.grpc.v1.images&#34;...  type=io.containerd.grpc.v1
INFO[2022-07-10T13:19:09.006282813+09:00] loading plugin &#34;io.containerd.grpc.v1.leases&#34;...  type=io.containerd.grpc.v1
INFO[2022-07-10T13:19:09.006290262+09:00] loading plugin &#34;io.containerd.grpc.v1.namespaces&#34;...  type=io.containerd.grpc.v1
INFO[2022-07-10T13:19:09.006298818+09:00] loading plugin &#34;io.containerd.internal.v1.opt&#34;...  type=io.containerd.internal.v1
INFO[2022-07-10T13:19:09.006397506+09:00] loading plugin &#34;io.containerd.grpc.v1.snapshots&#34;...  type=io.containerd.grpc.v1
INFO[2022-07-10T13:19:09.006410048+09:00] loading plugin &#34;io.containerd.grpc.v1.tasks&#34;...  type=io.containerd.grpc.v1
INFO[2022-07-10T13:19:09.006417912+09:00] loading plugin &#34;io.containerd.grpc.v1.version&#34;...  type=io.containerd.grpc.v1
INFO[2022-07-10T13:19:09.006426572+09:00] loading plugin &#34;io.containerd.grpc.v1.introspection&#34;...  type=io.containerd.grpc.v1
INFO[2022-07-10T13:19:09.006547669+09:00] serving...                                    address=/var/run/docker/containerd/containerd-debug.sock
INFO[2022-07-10T13:19:09.006587473+09:00] serving...                                    address=/var/run/docker/containerd/containerd.sock.ttrpc
INFO[2022-07-10T13:19:09.006623044+09:00] serving...                                    address=/var/run/docker/containerd/containerd.sock
INFO[2022-07-10T13:19:09.006640633+09:00] containerd successfully booted in 0.033593s
INFO[2022-07-10T13:19:09.012581779+09:00] parsed scheme: &#34;unix&#34;                         module=grpc
INFO[2022-07-10T13:19:09.012596336+09:00] scheme &#34;unix&#34; not registered, fallback to default scheme  module=grpc
INFO[2022-07-10T13:19:09.012609483+09:00] ccResolverWrapper: sending update to cc: {[{unix:///var/run/docker/containerd/containerd.sock  &lt;nil&gt; 0 &lt;nil&gt;}] &lt;nil&gt; &lt;nil&gt;}  module=grpc
INFO[2022-07-10T13:19:09.012616202+09:00] ClientConn switching balancer to &#34;pick_first&#34;  module=grpc
INFO[2022-07-10T13:19:09.012981373+09:00] parsed scheme: &#34;unix&#34;                         module=grpc
INFO[2022-07-10T13:19:09.012990671+09:00] scheme &#34;unix&#34; not registered, fallback to default scheme  module=grpc
INFO[2022-07-10T13:19:09.012998299+09:00] ccResolverWrapper: sending update to cc: {[{unix:///var/run/docker/containerd/containerd.sock  &lt;nil&gt; 0 &lt;nil&gt;}] &lt;nil&gt; &lt;nil&gt;}  module=grpc
INFO[2022-07-10T13:19:09.013003905+09:00] ClientConn switching balancer to &#34;pick_first&#34;  module=grpc
ERRO[2022-07-10T13:19:09.013650576+09:00] failed to mount overlay: operation not permitted  storage-driver=overlay2
ERRO[2022-07-10T13:19:09.013689926+09:00] exec: &#34;fuse-overlayfs&#34;: executable file not found in $PATH  storage-driver=fuse-overlayfs
WARN[2022-07-10T13:19:09.013871253+09:00] [graphdriver] WARNING: the aufs storage-driver is deprecated, and will be removed in a future release
WARN[2022-07-10T13:19:09.039645655+09:00] Your kernel does not support swap memory limit
WARN[2022-07-10T13:19:09.039658477+09:00] Your kernel does not support CPU realtime scheduler
WARN[2022-07-10T13:19:09.039663435+09:00] Your kernel does not support cgroup blkio weight
WARN[2022-07-10T13:19:09.039667506+09:00] Your kernel does not support cgroup blkio weight_device
INFO[2022-07-10T13:19:09.039793600+09:00] Loading containers: start.
WARN[2022-07-10T13:19:09.040965617+09:00] Running iptables --wait -t nat -L -n failed with message: `iptables v1.8.4 (legacy): can&#39;t initialize iptables table `nat&#39;: Permission denied (you must be root)
Perhaps iptables or your kernel needs to be upgraded.`, error: exit status 3
INFO[2022-07-10TT13:19:09.058222674+09:00] stopping event stream following graceful shutdown  error=&#34;&lt;nil&gt;&#34; module=libcontainerd namespace=moby
INFO[2022-07-10TT13:19:09.058478516+09:00] stopping healthcheck following graceful shutdown  module=libcontainerd
INFO[2022-07-10TT13:19:09.058517532+09:00] stopping event stream following graceful shutdown  error=&#34;context canceled&#34; module=libcontainerd namespace=plugins.moby
WARN[2022-07-10TT13:19:10.059763358+09:00] grpc: addrConn.createTransport failed to connect to {unix:///var/run/docker/containerd/containerd.sock  &lt;nil&gt; 0 &lt;nil&gt;}. Err :connection error: desc = &#34;transport: Error while dialing dial unix:///var/run/docker/containerd/containerd.sock: timeout&#34;. Reconnecting...  module=grpc
failed to start daemon: Error initializing network controller: error obtaining controller instance: failed to create NAT chain DOCKER: iptables failed: iptables -t nat -N DOCKER: iptables v1.8.4 (legacy): can&#39;t initialize iptables table `nat&#39;: Permission denied (you must be root)
Perhaps iptables or your kernel needs to be upgraded.
 (exit status 3)</pre>


<h2 id="Docker-outside-of-Docker-を試す">Docker outside of Docker を試す</h2>

<h3 id="ホスト側の操作-1">ホスト側の操作</h3>

<p>普通に利用したいコンテナを起動する際に、ホスト側のDockerデーモンのソケットファイルを、コンテナ側にもマウントすれば良い。</p>

<pre class="code" data-lang="" data-unlink>% docker container run -it --rm --name dood-test -v /var/run/docker.sock:/var/run/docker.sock ubuntu</pre>


<h3 id="コンテナ側の操作-1">コンテナ側の操作</h3>

<p>コンテナ側にも docker をインストールする。ちゃんとやる場合は Docker 公式のものをインストールするところだけど、とりあえず動作確認のため <a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> のパッケージのものをインストールする。</p>

<pre class="code" data-lang="" data-unlink>root@20b7df14b340:/ # apt update &amp;&amp; apt install -y docker.io</pre>


<p>コンテナからも <code>docker</code> コマンドが使えるようになった。
結果はホスト側から実行した場合と同じになる。</p>

<p>この場合はコンテナ内に Docker デーモンが起動している訳ではなく、ホスト側の Docker デーモンに対して <code>/var/run/docker.sock</code> を介して実行された Docker <a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a>で取得された情報が返されているものと思う。Docker <a class="keyword" href="http://d.hatena.ne.jp/keyword/API">API</a>はデフォルトだと認証がないため、この場合だとホスト内からだと自由に実行できてしまうといった点ではセキュリティ上の懸念はある。</p>

<pre class="code" data-lang="" data-unlink>root@20b7df14b340:/ # docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED         STATUS         PORTS     NAMES
20b7df14b340   ubuntu    &#34;bash&#34;    4 minutes ago   Up 4 minutes             dood-test</pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> コンテナ内で、docker イメージをビルドして起動してみると、うまく起動もできた。</p>

<pre class="code" data-lang="" data-unlink>root@20b7df14b340:/#  docker container run -it --rm --name dood-docker  docker
/ #</pre>


<h3 id="ホスト側の操作-2">ホスト側の操作</h3>

<p>ホスト側から見た場合は以下のような感じになる。</p>

<pre class="code" data-lang="" data-unlink>~ % docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
715a67e73c21   docker    &#34;docker-entrypoint.s…&#34;   2 minutes ago   Up 2 minutes             dood-docker
20b7df14b340   ubuntu    &#34;bash&#34;                   9 minutes ago   Up 9 minutes             dood-test</pre>


<h2 id="まとめ">まとめ</h2>

<p>Docker outside of Docker の使い方を簡単に記載した。</p>
<div class="footnote">
<p class="footnote"><a href="#fn-e7145a74" name="f-e7145a74" class="footnote-number">*1</a><span class="footnote-delimiter">:</span><span class="footnote-text"><a href="https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/">https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/</a></span></p>
</div>
