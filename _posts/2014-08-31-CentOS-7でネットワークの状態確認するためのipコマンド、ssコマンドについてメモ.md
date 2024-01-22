---
layout: post
Categories:  ["tech"]
Description:  " はじめに  CentOS 7ではnet-toolsパッケージ(ifconfigコマンド、netstatコマンド等)が非推奨になりデフォルトではインストールされておらず、iprouteパッケージ(ipコマンド、ssコマンド)が使われることに"
Tags: ["CentOS 7"]
date: "2014-08-31T14:04:00+09:00"
title: "CentOS 7でネットワークの状態確認するためのipコマンド、ssコマンドについてメモ"
author: "dshimizu"
archive: ["2014"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/CentOS">CentOS</a> 7では<code>net-tools</code>パッケージ(<code>ifconfig</code>コマンド、<code>netstat</code>コマンド等)が非推奨になりデフォルトではインストールされておらず、<code>iproute</code>パッケージ(<code>ip</code>コマンド、<code>ss</code>コマンド)が使われることになっています。 </p>
<p>慣れていないコマンドはなかなかオプション等を覚えられず、最初は苦労しましたので、すでにいろいろなブログ記事等で紹介されていますが、自分用に簡単な使い方をいくつかメモしておきます。 </p>
<a name="more"></a><h4>ipコマンド, ssコマンドによるネットワーク確認</h4>
<p>基本的な使い方は<code>help</code>で確認できます。必要に応じて<code>man</code>で確認します。 </p>
<ul>  <li>ipコマンドの使い方</li>
</ul>
<pre class="file">  Usage: ip [ OPTIONS ] OBJECT { COMMAND | help }        ip [ -force ] -batch filename where  OBJECT := { link | addr | addrlabel | route | rule | neigh | ntable |                    tunnel | tuntap | maddr | mroute | mrule | monitor | xfrm |                    netns | l2tp | tcp_metrics | token }        OPTIONS := { -V[ersion] | -s[tatistics] | -d[etails] | -r[esolve] |                     -f[amily] { inet | inet6 | ipx | dnet | bridge | link } |                     -4 | -6 | -I | -D | -B | -0 |                     -l[oops] { maximum-addr-flush-attempts } |                     -o[neline] | -t[imestamp] | -b[atch] [filename] |                     -rc[vbuf] [size]}  </pre> <ul>  <li>ssコマンドの使い方</li>
</ul>
<pre class="file">  Usage: ss [ OPTIONS ]        ss [ OPTIONS ] [ FILTER ]    -h, --help  this message    -V, --version output version information    -n, --numeric don't resolve service names    -r, --resolve       resolve host names    -a, --all  display all sockets    -l, --listening display listening sockets    -o, --options       show timer information    -e, --extended      show detailed socket information    -m, --memory        show socket memory usage    -p, --processes show process using socket    -i, --info  show internal TCP information    -s, --summary show socket usage summary    -b, --bpf           show bpf filter socket information     -4, --ipv4          display only IP version 4 sockets    -6, --ipv6          display only IP version 6 sockets    -0, --packet display PACKET sockets    -t, --tcp  display only TCP sockets    -u, --udp  display only UDP sockets    -d, --dccp  display only DCCP sockets    -w, --raw  display only RAW sockets    -x, --unix  display only Unix domain sockets    -f, --family=FAMILY display sockets of type FAMILY     -A, --query=QUERY, --socket=QUERY        QUERY := {all|inet|tcp|udp|raw|unix|packet|netlink}[,QUERY]     -D, --diag=FILE     Dump raw information about TCP sockets to FILE    -F, --filter=FILE   read filter information from FILE        FILTER := [ state TCP-STATE ] [ EXPRESSION ]  </pre> <h5>ネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>の状態確認</h5>
<p><code>ip</code>コマンドの<code>address</code>オブジェクトを用いてネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/IP%A5%A2%A5%C9%A5%EC%A5%B9">IPアドレス</a>や状態を確認できます。 </p>
<h6>構文</h6>
<p></p>
<ul>  <li>すべてのデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/IP%A5%A2%A5%C9%A5%EC%A5%B9">IPアドレス</a>と状態を確認</li>
</ul>
<pre class="file">  ip address show  
</pre>
<p>　※<code>address</code>は<code>a</code>と短縮可能、<code>show</code>は省略可能 </p>
<p></p>
<ul>  <li>特定のデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/IP%A5%A2%A5%C9%A5%EC%A5%B9">IPアドレス</a>と状態を確認</li>
</ul>
<pre class="file">  ip address show dev デバイス名  
</pre>
<p>　※<code>address</code>は<code>a</code>と短縮可能 </p> <h6>実行例</h6>
<p>以下が実行例です。 </p>
<ul>  <li>すべてのデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/IP%A5%A2%A5%C9%A5%EC%A5%B9">IPアドレス</a>と状態を確認</li>
</ul>
<pre class="terminal">  % ip a 1: lo:  mtu 65536 qdisc noqueue state UNKNOWN     link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00     inet 127.0.0.1/8 scope host lo        valid_lft forever preferred_lft forever     inet6 ::1/128 scope host        valid_lft forever preferred_lft forever 2: eth0:  mtu 1500 qdisc pfifo_fast state UP qlen 1000     link/ether  brd ff:ff:ff:ff:ff:ff     inet  brd  scope global eth0        valid_lft forever preferred_lft forever     inet6  scope link        valid_lft forever preferred_lft forever 3: eth1:  mtu 1500 qdisc pfifo_fast state UP qlen 1000     link/ether  brd ff:ff:ff:ff:ff:ff 4: eth2:  mtu 1500 qdisc pfifo_fast state UP qlen 1000     link/ether  brd ff:ff:ff:ff:ff:ff  </pre> <ul>  <li>eth0デ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/IP%A5%A2%A5%C9%A5%EC%A5%B9">IPアドレス</a>と状態を確認</li>
</ul>
<pre class="terminal">  % ip a show dev eth0 2: eth0:  mtu 1500 qdisc pfifo_fast state UP qlen 1000     link/ether 52:54:11:00:42:42 brd ff:ff:ff:ff:ff:ff     inet  brd  scope global eth0        valid_lft forever preferred_lft forever     inet6  scope link        valid_lft forever preferred_lft forever  </pre> <h5>ネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>のリンクのダウン/アップ、状態確認</h5>
<p><code>ip</code>コマンドの<code>link</code>オブジェクトを用いてネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>のリンク状態を確認できます。 </p> <h6>構文</h6>
<ul>  <li>ネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>をリンクダウン</li>
</ul>
<pre class="file">  ip link set デバイス名 down  
</pre>
<p>　※<code>link</code>は<code>l</code>と短縮可能 </p>
<ul>  <li>ネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>をリンクアップ</li>
</ul>
<pre class="file">  ip link set デバイス名 up  
</pre>
<p>　※<code>link</code>は<code>l</code>と短縮可能 </p>
<ul>  <li>全てのネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>のリンク確認</li>
</ul>
<pre class="file">  ip link show  
</pre>
<p>　※<code>link</code>は<code>l</code>と短縮可能、<code>show</code>は省略可能 </p> <ul>  <li>特定のネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>のリンク確認</li>
</ul>
<pre class="file">  ip link show dev デバイス名  
</pre>
<p>　※<code>link</code>は<code>l</code>と短縮可能 </p> <h6>実行例</h6>
<p>以下が実行例です。 </p>
<p></p>
<ul>  <li>ネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>のリンクダウン</li>
</ul>
<pre class="terminal">  # ip l set eth1 down  
</pre>
<p></p>
<ul>  <li>ネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>のリンク状態確認</li>
</ul>
<pre class="terminal">  # ip l show eth1 3: eth1:  mtu 1500 qdisc pfifo_fast state DOWN mode DEFAULT qlen 1000      link/ether  brd ff:ff:ff:ff:ff:ff  </pre> <p></p>
<ul>  <li>ネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>のリンクアップ</li>
</ul>
<pre class="terminal">  # ip link set eth1 up  
</pre>
<p></p>
<ul>  <li>ネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>のリンク状態確認</li>
</ul>
<pre class="terminal">  # ip link show dev eth1 3: eth1:  mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000     link/ether  brd ff:ff:ff:ff:ff:ff  </pre> <h5>パケットの処理情報確認</h5>
<p><code>ip</code>コマンドの<code>-s</code>オプションでデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>のより詳細な情報を表示できます。<code>link</code>オブジェクトと組み合わせることで、パケットの処理状態を表示できます。 </p>
<h6>構文</h6>
<ul>  <li>全てのネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>のパケットの処理情報を表示</li>
</ul>
<pre class="file">  ip -s link show  
</pre>
<p>　※<code>link</code>は<code>l</code>と短縮可能、<code>show</code>は省略可能 </p> <ul>  <li>全てのネットワークデ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>のパケットの処理情報を表示</li>
</ul>
<pre class="file">  ip -s link show dev デバイス名  
</pre>
<p>　※<code>link</code>は<code>l</code>と短縮可能 </p> <h6>実行例</h6>
<pre class="terminal">  # ip -s l 1: lo:  mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT     link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00     RX: bytes  packets  errors  dropped overrun mcast     2060       19       0       0       0       0     TX: bytes  packets  errors  dropped carrier collsns     2060       19       0       0       0       0 2: eth0:  mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000     link/ether  brd ff:ff:ff:ff:ff:ff     RX: bytes  packets  errors  dropped overrun mcast     53242888   780447   0       0       0       0     TX: bytes  packets  errors  dropped carrier collsns     4936364    25919    0       0       0       0 3: eth1:  mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000     link/ether  brd ff:ff:ff:ff:ff:ff     RX: bytes  packets  errors  dropped overrun mcast     468        6        0       0       0       0     TX: bytes  packets  errors  dropped carrier collsns     0          0        0       0       0       0 4: eth2:  mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000     link/ether  brd ff:ff:ff:ff:ff:ff     RX: bytes  packets  errors  dropped overrun mcast     468        6        0       0       0       0     TX: bytes  packets  errors  dropped carrier collsns     0          0        0       0       0       0  </pre> <pre class="terminal">  # ip -s l show dev eth0 2: eth0:  mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000     link/ether  brd ff:ff:ff:ff:ff:ff     RX: bytes  packets  errors  dropped overrun mcast     53909617   790762   0       0       0       0     TX: bytes  packets  errors  dropped carrier collsns     5012886    26334    0       0       0       0  </pre> <h5>ルーティングテーブル確認</h5>
<p><code>ip</code>コマンドの<code>route</code>オブジェクトを用いてルーティングテーブルのエントリを確認できます。 </p>
<h6>構文</h6>
<pre class="file">  ip route show  
</pre>
<p>　※<code>route</code>は<code>r</code>と短縮可能、<code>show</code>は省略可能 </p>
<h6>実行例</h6>
<pre class="terminal">  # ip r default via &lt;デフォルトゲートウェイアドレス&gt; dev eth0  proto static  metric 1024 IPアドレス&gt; dev eth0  proto kernel  scope link  src   </pre>  <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/arp">arp</a>キャッシュ確認</h5>
<p><code>ip</code>コマンドの<code>neighbour</code>オブジェクトを用いて<a class="keyword" href="http://d.hatena.ne.jp/keyword/ARP">ARP</a>やNDISCのキャッシュ状態を確認できます。 </p>
<h6>構文</h6>
<pre class="file">  ip neighbour show  
</pre>
<p>　※<code>neighbour</code>は<code>n</code>と短縮可能、<code>show</code>は省略可能 </p>
<h6>実行例</h6>
<pre class="terminal">  # ip n  dev eth0 lladdr  STALE  dev eth0 lladdr  DELAY  </pre> <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/TCP">TCP</a>通信状態確認</h5>
<h6>構文</h6>
<p><code>ss</code>コマンドの<code>-a</code>オプションを指定すると全ての通信ソケットが表示され、<code>-t</code>オプションを指定すると<a class="keyword" href="http://d.hatena.ne.jp/keyword/tcp">tcp</a>通信のコネクション状況を確認できます。<code>-n</code>オプションを指定すると、通信しているサービスの名前ではなくポート番号が表示されます。 </p>
<pre class="file">  ss -a -t (-n)  </pre> <h6>実行例</h6>
<pre class="terminal">  #  ss -ant State       Recv-Q Send-Q                                                               Local Address:Port                                                                 Peer Address:Port LISTEN      0      100                                                                      127.0.0.1:25                                                                              *:* LISTEN      0      128                                                                              *:22                                                                              *:* ESTAB       0      52                                                                  xxx.xxx.xxx.xxx:22                                                                  xxx.xxx.xxx.xxx:59702 ESTAB       0      76                                                                  xxx.xxx.xxx.xxx:22                                                                  xxx.xxx.xxx.xxx:55329 TIME-WAIT   0      0                                                                   xxx.xxx.xxx.xxx:22                                                                  xxx.xxx.xxx.xxx:59080 LISTEN      0      100                                                                            ::1:25                                                                             :::* LISTEN      0      128                                                                             :::22                                                                             :::*  </pre> <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/UDP">UDP</a>通信状態確認</h5>
<h6>構文</h6>
<p><code>ss</code>コマンドの<code>-a</code>オプションを指定すると全ての通信ソケットが表示され、<code>-u</code>オプションを指定すると<a class="keyword" href="http://d.hatena.ne.jp/keyword/udp">udp</a>通信のコネクション状況を確認できます。<code>-n</code>オプションを指定すると、通信しているサービスの名前ではなくポート番号が表示されます。 </p>
<pre class="file">  ss -a -u (-n)  </pre> <h6>実行例</h6>
<pre class="terminal">  # ss -a -n -u State       Recv-Q Send-Q                                                               Local Address:Port                                                                 Peer Address:Port UNCONN      0      0                                                                                *:123                                                                             *:* UNCONN      0      0                                                                                *:5353                                                                            *:* UNCONN      0      0                                                                                *:51496                                                                           *:* UNCONN      0      0                                                                        127.0.0.1:323                                                                             *:* UNCONN      0      0                                                                               :::123                                                                            :::* UNCONN      0      0                                                                              ::1:323                                                                            :::*  </pre> <h4>おわりに</h4>
<p>よく使いそうなものをメモがてら書き連ねました。ただ、ネットワークはデフォルトで<code>NetworkManager</code>で管理されるようになっているのでそのやり方も確認しておく必要がありそうです。 </p>
</body>

<!-- more -->


