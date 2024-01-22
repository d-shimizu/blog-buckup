---
layout: post
Categories:  ["tech"]
Description:  " コンテナの仕組みについての学習の一貫で、以下の記事を読みながらControl groups、Namespace、Capabilitiesを使ってコンテナを作って動かしてみる。       コンテナ技術入門 - 仮想化との違いを知り、要素技"
Tags: ["Container", "Docker", "tech"]
date: "2020-07-11T21:09:00+09:00"
title: "Control groups、Namespace、Capabilitiesを使ってコンテナを作って実行してみる"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p>コンテナの仕組みについての学習の一貫で、以下の記事を読みながらControl groups、Namespace、Capabilitiesを使ってコンテナを作って動かしてみる。</p>

<ul>
    <li><a href="https://employment.en-japan.com/engineerhub/entry/2019/02/05/103000" target="_brank" rel="noopener noreferrer">コンテナ技術入門 - 仮想化との違いを知り、要素技術を触って学ぼう - エンジニアHub｜若手Webエンジニアのキャリアを考える！</a></li>
</ul>

</body>

<!-- more -->

<body>
<h3>コンテナで使う<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%A1%A5%A4%A5%EB%A5%B7%A5%B9%A5%C6%A5%E0">ファイルシステム</a>の準備</h3>


<p><code>/tmp</code>以下に適当な<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成する。 次の手順でここにファイルを展開する。</p>

<pre class="terminal"> $ ROOTFS=$(mktemp -d)
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/CentOS">CentOS</a>７のイメージを取得する。Dockerコマンドを使っているが、コンテナを作るためでなく、このイメージから<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%A1%A5%A4%A5%EB%A5%B7%A5%B9%A5%C6%A5%E0">ファイルシステム</a>を抽出するために使用する。</p>

<pre class="terminal"> $ CID=$(sudo docker container create centos:centos7)
 </pre>


<p>取得したイメージから<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%A1%A5%A4%A5%EB%A5%B7%A5%B9%A5%C6%A5%E0">ファイルシステム</a>を抽出し、<code>/tmp</code>以下に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リへ展開する。</p>

<pre class="terminal"> $ sudo docker container export $CID | tar -x -C $ROOTFS
 </pre>


<p>以下のようにファイルが展開されている。</p>

<pre class="terminal"> $ ls -lht /tmp/tmp.D4ipCfPw3e/
合計 68K
drwxr-xr-x  4 hogehoge hogehoge 4.0K  7月 11 05:00 dev
drwxr-xr-x 47 hogehoge hogehoge 4.0K  7月 11 05:00 etc
-rw-r--r--  1 hogehoge hogehoge  12K  5月  4 15:37 anaconda-post.log
dr-xr-x---  2 hogehoge hogehoge 4.0K  5月  4 15:37 root
drwxr-xr-x 11 hogehoge hogehoge 4.0K  5月  4 15:37 run
drwxrwxr-x  7 hogehoge hogehoge 4.0K  5月  4 15:37 tmp
drwxr-xr-x 18 hogehoge hogehoge 4.0K  5月  4 15:36 var
lrwxrwxrwx  1 hogehoge hogehoge    7  5月  4 15:35 bin -&gt; usr/bin
lrwxrwxrwx  1 hogehoge hogehoge    7  5月  4 15:35 lib -&gt; usr/lib
lrwxrwxrwx  1 hogehoge hogehoge    9  5月  4 15:35 lib64 -&gt; usr/lib64
lrwxrwxrwx  1 hogehoge hogehoge    8  5月  4 15:35 sbin -&gt; usr/sbin
drwxr-xr-x 13 hogehoge hogehoge 4.0K  5月  4 15:35 usr
drwxr-xr-x  2 hogehoge hogehoge 4.0K  5月  4 15:35 proc
drwxr-xr-x  2 hogehoge hogehoge 4.0K  5月  4 15:35 sys
drwxr-xr-x  2 hogehoge hogehoge 4.0K  4月 11  2018 home
drwxr-xr-x  2 hogehoge hogehoge 4.0K  4月 11  2018 media
drwxr-xr-x  2 hogehoge hogehoge 4.0K  4月 11  2018 mnt
drwxr-xr-x  2 hogehoge hogehoge 4.0K  4月 11  2018 opt
drwxr-xr-x  2 hogehoge hogehoge 4.0K  4月 11  2018 srv
 </pre>


<p>Dockerコマンドで取得したコンテナイメージは不要なので消す。</p>

<pre class="terminal"> $ sudo docker container rm $CID
af28f3a824658aae61673120265b28fb63c6e60e62526bd47d3c9dcdaa1551fc
 </pre>


<h3>コン<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%ED%A1%BC%A5%EB">トロール</a>グループ作成</h3>


<p>適当な名前をつけるためにUUIDを使う。</p>

<pre class="terminal"> $ UUID=$(uuidgen)
 </pre>


<p>cpu,memoryのコン<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%ED%A1%BC%A5%EB">トロール</a>グループを作成する。名称にUUIDを利用する。</p>

<pre class="terminal"> $ sudo cgcreate -t $(id -un):$(id -gn) -a $(id -un):$(id -gn) -g cpu,memory:$UUID
 </pre>


<p>以下で確認できる。</p>

<pre class="terminal"> ＄ cat /proc/self/cgroup
 </pre>


<p>このグループ内の全てのプロセスのメモリ使用量を 10 MB に設定する。</p>

<pre class="terminal"> $ cgset -r memory.limit_in_bytes=10000000 $UUID
 </pre>


<p>CPUを利用可能な時間の上限値を1秒に設定する。</p>

<pre class="terminal"> $ cgset -r cpu.cfs_period_us=1000000 $UUID
 </pre>


<pre class="terminal"> $ cgset -r cpu.cfs_quota_us=300000 $UUID
 </pre>


<p>cgexec で指定したcgroupの設定に対して、 unshare で新しいシェルを呼び出し、その中で、現在使っているttyとptmxをコンテナ側の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%A1%A5%A4%A5%EB%A5%B7%A5%B9%A5%C6%A5%E0">ファイルシステム</a>にマウント&amp;リンクを張ったり（これによってコンテナを現在の疑似端末を使って制御できる）、/dev/null を作成したりホスト名を設定する。
unshare は親プロセスのNamespace（<a class="keyword" href="http://d.hatena.ne.jp/keyword/%CC%BE%C1%B0%B6%F5%B4%D6">名前空間</a>）を共有せずにプログラムを実行するためのコマンド。</p>

<pre class="terminal"> $ cgexec -g cpu,memory:$UUID \
   unshare -muinpfr /bin/sh -c "
    mount -t proc proc $ROOTFS/proc &amp;&amp;
    touch $ROOTFS$(tty); mount --bind $(tty) $ROOTFS$(tty) &amp;&amp;
    touch $ROOTFS/dev/pts/ptmx; mount --bind /dev/pts/ptmx $ROOTFS/dev/pts/ptmx &amp;&amp;
    ln -sf /dev/pts/ptmx $ROOTFS/dev/ptmx &amp;&amp;
    touch $ROOTFS/dev/null &amp;&amp; mount --bind /dev/null $ROOTFS/dev/null &amp;&amp;
    /bin/hostname $UUID &amp;&amp;
    exec capsh --chroot=$ROOTFS --drop=cap_sys_chroot -- -c 'exec /bin/sh'
  "
 </pre>


<p>これで以下のようなプロンプトが表示される。</p>

<pre class="terminal"> sh-4.2#
 </pre>


<h3>削除</h3>


<p>コンテナを停止させて cgroup や<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%A1%A5%A4%A5%EB%A5%B7%A5%B9%A5%C6%A5%E0">ファイルシステム</a>を削除する。</p>

<pre class="terminal"> sh-4.2# exit

$ sudo cgdelete -r -g cpu,memory:$UUID
$ rm -rf $ROOTFS
 </pre>


<h3>まとめ</h3>


<p>コンテナの仕組みの勉強のため、以下の記事や参考リンクを見ながらcgroupとNamespaceでコンテナを動かしてみた。
仕組みの理解が進んだのと同時にdockerの便利さや凄さも実感できた。</p>

<h3>参考</h3>


<ul>
    <li><a href="https://employment.en-japan.com/engineerhub/entry/2019/02/05/103000" target="_brank" rel="noopener noreferrer">コンテナ技術入門 - 仮想化との違いを知り、要素技術を触って学ぼう - エンジニアHub｜若手Webエンジニアのキャリアを考える！</a></li>
    <li><a href="https://tech-lab.sios.jp/archives/18811" target="_brank" rel="noopener noreferrer">【連載】世界一わかりみが深いコンテナ &amp; Docker入門 〜 その1:コンテナってなに？ 〜 | SIOS Tech. Lab</a></li>
    <li><a href="https://gihyo.jp/admin/serial/01/linux_containers/0042?page=2" target="_brank" rel="noopener noreferrer">第42回　Linuxカーネルのケーパビリティ［1］：LXCで学ぶコンテナ入門 －軽量仮想化環境を実現する技術｜gihyo.jp … 技術評論社</a></li>
    <li><a href="https://gihyo.jp/admin/serial/01/linux_containers/0016" target="_brank" rel="noopener noreferrer">第16回　Linuxカーネルのコンテナ機能 [6] ─ユーザ名前空間：LXCで学ぶコンテナ入門 －軽量仮想化環境を実現する技術｜gihyo.jp … 技術評論社</a></li>
    <li><a href="https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/7/html/resource_management_guide/chap-introduction_to_control_groups" target="_brank" rel="noopener noreferrer">第1章 コントロールグループの概要 (cgroup) Red Hat Enterprise Linux 7 | Red Hat Customer Portal</a></li>
    <li><a href="https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/6/html/resource_management_guide/sec-cpu#sect-cfs" target="_brank" rel="noopener noreferrer">3.2. cpu Red Hat Enterprise Linux 6 | Red Hat Customer Portal</a></li>
    <li><a href="https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/6/html/resource_management_guide/starting_a_process" target="_brank" rel="noopener noreferrer">2.9. コントロールグループ内のプロセスの開始 Red Hat Enterprise Linux 6 | Red Hat Customer Portal</a></li>
    <li><a href="https://blog.framinal.life/entry/2020/04/08/163138" target="_brank" rel="noopener noreferrer">要素技術を触って学ぶ「コンテナ技術入門」を実際にやってみた - フラミナル</a></li>
    <li><a href="https://manual.atmark-techno.com/armadillo-guide/armadillo-guide-2_ja-2.1.0/ch03.html" target="_brank" rel="noopener noreferrer">第3章 Linuxシステムの仕組みと運用、管理</a></li>
    <li><a href="https://kaito-two.hatenablog.com/entry/2019/07/06/010602" target="_brank" rel="noopener noreferrer">dockerコマンド無しでコンテナを動かしてみる - Data Eng (なりたい)</a></li>
</ul>

</body>
