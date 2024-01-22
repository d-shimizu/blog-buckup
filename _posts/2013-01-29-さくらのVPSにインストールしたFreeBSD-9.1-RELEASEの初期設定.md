---
layout: post
Categories:  ["tech"]
Description:  " はじめに  以前本ブログで記載した\"こちら\"の記事の内容を元に、さくらのVPSのISOイメージインストールを利用して、FreeBSD 9.1-RELEASEのISOイメージからFreeBSD 9.1でのZFS FileSystem環境を構"
Tags: ["FreeBSD", "さくらのVPS"]
date: "2013-01-29T02:24:00+09:00"
title: "さくらのVPSにインストールしたFreeBSD 9.1-RELEASEの初期設定"
author: "dshimizu"
archive: ["2013"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p>以前本ブログで記載した"<a href="http://kanjuku-tomato.blogspot.jp/2013/01/freebsd-91-release-zfs-root-filesystem.html">こちら</a>"の記事の内容を元に、さくらの<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPS">VPS</a>のISOイメージインストールを利用して、<a class="keyword" href="http://d.hatena.ne.jp/keyword/FreeBSD">FreeBSD</a> 9.1-RELEASEのISOイメージから<a class="keyword" href="http://d.hatena.ne.jp/keyword/FreeBSD">FreeBSD</a> 9.1での<a class="keyword" href="http://d.hatena.ne.jp/keyword/ZFS">ZFS</a> FileSystem環境を構築したので、その際に行ったOSの初期設定についてまとめました。 </p>
<a name="more"></a> <h4>設定項目</h4>
<p>初期設定として、以下を実施しています。 </p>
<ol>  <li>日本語キーボードの設定</li>  <li>rootパスワードの設定</li>  <li>ホスト名、ネットワークの設定</li>  <li>ユーザ/グループの作成</li>  <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSH">SSH</a>の設定</li>  <li>FW(<a class="keyword" href="http://d.hatena.ne.jp/keyword/ipfw">ipfw</a>)の設定</li>  <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/DNS">DNS</a>リ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BE%A5%EB">ゾル</a>バの設定</li>  <li>NTPの設定</li>  <li>メール(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Sendmail">Sendmail</a>)の設定</li>  <li>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ports">Ports</a> Collectionの設定</li>
</ol> <h4>環境</h4>
<p>構築環境は以下のとおりです。 </p>
<table>  <tr>    <td bgcolor="#cccccc">プラットフォーム</td>    <td>さくらの<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPS">VPS</a> 2G</td>  </tr>  <tr>    <td bgcolor="#cccccc">OS</td>    <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/FreeBSD">FreeBSD</a> 9.1-RELEASE <a class="keyword" href="http://d.hatena.ne.jp/keyword/amd64">amd64</a>
</td>  </tr>
</table> <h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/FreeBSD">FreeBSD</a> 9.1-RELEASEのOS初期設定</h4>
<p>最初は<a class="keyword" href="http://d.hatena.ne.jp/keyword/IP%A5%A2%A5%C9%A5%EC%A5%B9">IPアドレス</a>も設定されていないので、さくらの<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPS">VPS</a>コン<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%ED%A1%BC%A5%EB">トロール</a>パネルから<a class="keyword" href="http://d.hatena.ne.jp/keyword/VNC">VNC</a>コンソールを開き、作業を行います。 </p> <h5>日本語キーボードの設定</h5>
<p>keymapがデフォルト(american phonetic layout)になっているようなので、利用しているキーボードに合わせて設定します。<br>ここでは日本語キーボードレイアウトのものに変更します。 </p>
<pre class="terminal">  # kbdcontrol -l jp.106.kbd &lt; /dev/console  
</pre>
<p>システム起動時にも有効となるように/etc/rc.confへ記載します。 </p>
<pre class="terminal">  # vi /etc/rc.conf ### KeyBoard Setting ### keymap="jp.106"  </pre> <h5>rootパスワード設定</h5>
<p>初期状態ではrootのパスワードが設定されていないので、パスワードを設定します。 </p>
<pre class="terminal">  # passwd Changing local password for root New Password: Retype New Password:  </pre> <h5>ホスト名、ネットワークの設定</h5>
<p>/etc/rc.confにOSの基本情報を追記します。 環境に合わせてホスト名、ネットワークといった各種情報を入力します。 </p>
<pre class="terminal">  # vi /etc/rc.conf ### Hostname Setting ### hostname="xxxxxxxxx.sakura.ne.jp"  ### Network Setting ### ifconfig_em0="inet xxx.xxx.xxx.xxx netmask xxx.xxx.xxx.xxx" defaultrouter="xxx.xxx.xxx.xxx"  
</pre>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/NIC">NIC</a>デ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>に<a class="keyword" href="http://d.hatena.ne.jp/keyword/IP%A5%A2%A5%C9%A5%EC%A5%B9">IPアドレス</a>を割り当てたので、ネットワークを起動します。 <pre class="terminal">  # /etc/netstart  </pre> <h5>ユーザ/グループ作成</h5>
<p>まず、"pw groupadd"で必要なグループを作成します。 ここでは例として<a class="keyword" href="http://d.hatena.ne.jp/keyword/hoge">hoge</a>というグループをグループID：10001として作成しています。 </p>
<pre class="terminal">  # pw groupadd hoge -g 10001  
</pre>
<p>続いてadduserコマンドで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%BF%A5%E9%A5%AF%A5%C6%A5%A3%A5%D6">インタラクティブ</a>(対話的)に新規ユーザを作成します。<br>ここでは例として<a class="keyword" href="http://d.hatena.ne.jp/keyword/hoge">hoge</a>というユーザを作成し、<a class="keyword" href="http://d.hatena.ne.jp/keyword/hoge">hoge</a>グループとwheelグループに所属させます。<br>特に記載しない場合はデフォルト値([]内の値)が設定されます。 </p>
<pre class="terminal">  # adduser ### 作成ユーザの設定 ### ### ユーザ名入力 Username: hoge ### フルネーム入力 Full name: hoge ### ユーザIDを指定 Uid (Leave empty for default): 10001 ### ログイングループを指定 Login group [hoge]:  ### 他のログイングループを指定(ここでwheelグループを指定) Login group is users. Invite j into other groups? []: wheel ### ログインクラスを指定(デフォルトのままでよいのでそのままEnterキー押下) Login class [default]:  ### 利用したいシェルを指定 Shell (sh csh tcsh bash rbash nologin) [sh]: tcsh ### ホームディレクトリの指定 Home directory [/home/hoge]:  ### パスワード認証の可否の設定 Use password-based authentication? [yes]: ### 空パスワードの設定の可否の設定 Use an empty password? (yes/no) [no]: ### ランダムパスワードでの設定の可否の設定 Use a random password? (yes/no) [no]: ### パスワードの設定 Enter password:  Enter password again:  ### アカウントをロックさせるかどうかの設定 Lock out the account after creation? [no]: ### 設定内容の確認 Username : hoge Password : ***** Full Name : hoge Uid : 10001 Class : Groups : hoge wheel Home : /home/hoge Shell : /bin/tcsh Locked : no ### 上記内容で作成してよいかの確認(問題なければyes) OK? (yes/no): yes adduser: INFO: Successfully added (hoge) to the user database. ### 別のユーザを作成するかの確認 Add another user? (yes/no): no Goodbye!  </pre> <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSH">SSH</a>の設定</h5>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/SSH">SSH</a>の設定を行います。<br>※セキュリティ的な問題は多少ありますが、キー認証のみ許可する設定にするので<a class="keyword" href="http://d.hatena.ne.jp/keyword/teraterm">teraterm</a>などでログインして作業するなどが良いかも知れません。 <br>/etc/<a class="keyword" href="http://d.hatena.ne.jp/keyword/ssh">ssh</a>/<a class="keyword" href="http://d.hatena.ne.jp/keyword/sshd">sshd</a>_configの設定を編集します。 <a class="keyword" href="http://d.hatena.ne.jp/keyword/ssh">ssh</a>のkey認証のみの接続を許可するように設定しています。 </p>
<pre class="terminal">  # vi /etc/ssh/sshd_config ### sshd_configの設定 ### ### SSHのポート番号変更(ここでは例として10022と設定) Port 10022  ### Rootログインの禁止 PermitRootLogin No  ### 公開鍵認証を許可 RSAAuthentication yes PubkeyAuthentication yes ### 鍵ファイルのパス指定 AuthorizedKeysFile     .ssh/authorized_keys  ### パスワードログインを禁止 PasswordAuthentication no ### 空のパスワード利用を禁止 PermitEmptyPasswords no ### チャレンジレスポンス認証を禁止 ChallengeResponseAuthentication no  ### sshでのログインを許可するユーザを指定 AllowUsers hoge  
</pre>
<p>先ほど作成したユーザにスイッチし、<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSH">SSH</a>公開鍵を作成します。 </p>
<pre class="terminal">  # su - hoge  
</pre>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/RSA%B0%C5%B9%E6">RSA暗号</a>方式での鍵ペアを作成します。 </p>
<pre class="terminal">  # ssh-keygen -t rsa ### RSA暗号鍵の生成 ### Generating public/private rsa key pair. ### 必要であれば、キーファイルの設置先とファイル名を指定します。 Enter file in which to save the key (/home/hoge/.ssh/id_rsa):  ### パスフレーズを2度入力します。 Enter passphrase (empty for no passphrase): Enter same passphrase again: Your identification has been saved in /home/hoge/.ssh/id_rsa. Your public key has been saved in /home/hoge/.ssh/id_rsa. The key fingerprint is: **:**:**:**:**:**:**:**:**:**:**:**:**:**:**:** hoge@xxxxxxxx.sakura.ne.jp The key's randomart image is: +--[ RSA 2048]----+ |                 | |                 | |                 | |                 | |                 | |                 | |                 | |                 | |                 | +-----------------+  
</pre>
<p>$HOME/.<a class="keyword" href="http://d.hatena.ne.jp/keyword/ssh">ssh</a>/<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リに<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C8%EB%CC%A9%B8%B0">秘密鍵</a>(id_<a class="keyword" href="http://d.hatena.ne.jp/keyword/rsa">rsa</a>)と公開鍵(id_<a class="keyword" href="http://d.hatena.ne.jp/keyword/rsa">rsa</a>.pub)が作成されます。 /etc/<a class="keyword" href="http://d.hatena.ne.jp/keyword/ssh">ssh</a>/<a class="keyword" href="http://d.hatena.ne.jp/keyword/sshd">sshd</a>_configの設定により、公開鍵はユーザのホーム<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ配下の「.<a class="keyword" href="http://d.hatena.ne.jp/keyword/ssh">ssh</a>/authorized_keys」が参照されますので、公開鍵のファイル名を変更します。 </p>
<pre class="terminal">  $ cd .ssh $ cat id_rsa.pub &gt;&gt; $HOME/.ssh/authorized_keys $ chmod 600 authorized_keys  
</pre>
<p>また、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D1%A1%BC%A5%DF%A5%C3%A5%B7%A5%E7%A5%F3">パーミッション</a>もそのユーザのみしか読み書きできないようにしておく必要があります。 </p>
<pre class="terminal">  $ chmod 600 authorized_keys  
</pre>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%C8%EB%CC%A9%B8%B0">秘密鍵</a>については、<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSH">SSH</a>クライアントを利用する端末にコピーするなどして取得しておきます。 <br>rc.confに<a class="keyword" href="http://d.hatena.ne.jp/keyword/SSH">SSH</a>起動の設定を追記します。 </p>
<pre class="terminal">  # vi /etc/rc.conf ### SSH Setting ### sshd_enable="YES"  
</pre>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/sshd">sshd</a>を起動します。 </p>
<pre class="terminal">  # service sshd start Starting sshd  </pre> <h5>FW(<a class="keyword" href="http://d.hatena.ne.jp/keyword/ipfw">ipfw</a>)の設定</h5>
<p>/etc/rc.<a class="keyword" href="http://d.hatena.ne.jp/keyword/firewall">firewall</a>という名前のファイルがデフォルトで存在しますが、新規に<a class="keyword" href="http://d.hatena.ne.jp/keyword/ipfw">ipfw</a>用のコンフィグファイルを作成します。ここでは<a class="keyword" href="http://d.hatena.ne.jp/keyword/ipfw">ipfw</a>.confというファイル名で作成していますが、名前は何でも構いません。<br>一番最後のルール999だと/var/log/secureに多量のログが出力されますので、それが困る場合は削除しても構いません。ルール65535に"deny ip from any to any"が自動で設定されるためです。 </p>
<pre class="terminal">  # vi /etc/ipfw.conf ### ipfw.confの設定 ### #! /bin/sh  FW="ipfw -q" RULE="ipfw -q add"   ### Delete Rule $FW -f flush  # Permit loopback address $RULE 10 allow all from any to any via lo0  # Drop Needless Packet $RULE 20 deny all from any to 127.0.0.0/8 $RULE 21 deny all from 127.0.0.0/8 to any $RULE 22 deny all from any to 172.16.0.0/12 $RULE 23 deny all from 172.16.0.0/12 to any $RULE 24 deny all from any to 192.168.0.0/16 $RULE 25 deny all from 192.168.0.0/16 to any  $RULE 30 deny tcp from any to any frag  # statefull $RULE 50 check-state $RULE 60 allow tcp from any to any established $RULE 70 allow all from any to any out keep-state $RULE 80 deny icmp from any to any  # Permit DNS $RULE 201 allow udp from me to any 53 out keep-state  # Permit NTP $RULE 202 allow udp from me to 210.188.224.14 123 in keep-state $RULE 203 allow udp from me to any 123 out keep-state  # Permit SSH(prot 10022) $RULE 301 allow tcp from any to me 10022 in $RULE 302 allow tcp from me to any 22 out  # Permit Mail $RULE 303 allow tcp from me to any 25 in $RULE 304 allow tcp from me to any 25 out  # Permit http/https $RULE 305 allow tcp from any to me 80 in $RULE 306 allow tcp from me to any 80 out $RULE 307 allow tcp from any to me 443 in $RULE 308 allow tcp from me to any 443 out  # deny and log everything $RULE 999 deny log all from any to any  
</pre>
<p>rc.confへ<a class="keyword" href="http://d.hatena.ne.jp/keyword/ipfw">ipfw</a>起動の設定を追記します。 </p>
<pre class="terminal">  # vi /etc/rc.conf ### Firewall Setting ### firewall_enable="YES" firewall_logging="YES" firewall_script="/etc/ipfw.conf"  
</pre>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/ipfw">ipfw</a>を起動します。 </p>
<pre class="terminal">  # /etc/rc.d/ipfw start Starting ipfw.  </pre> <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/DNS">DNS</a>リ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BE%A5%EB">ゾル</a>バの設定</h5>
<p>リ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BE%A5%EB">ゾル</a>バの設定を行います。 ※利用しているリージョン等によって内容が異なると思いますので、環境に合わせて設定します。 </p>
<pre class="terminal">  # vi /etc/resolv.conf ### resolv.confの設定 ### nameserver xxx.xxx.xxx.xxx nameserver xxx.xxx.xxx.xxx serach sakura.ne.jp  </pre> <h5>NTPの設定</h5>
<p>まず、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BF%A5%A4%A5%E0%A5%BE%A1%BC%A5%F3">タイムゾーン</a>の設定を行います。 </p>
<pre class="terminal">  # tzsetup  
</pre>
<p>NTPの設定を行います。<br>参照先NTPサーバは<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A4%B5%A4%AF%A4%E9%A5%A4%A5%F3%A5%BF%A1%BC%A5%CD%A5%C3%A5%C8">さくらインターネット</a>社のNTPサーバとし、外部からこの<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPS">VPS</a>サーバのntpdサービスの参照を無効化します。 </p>
<pre class="terminal">  # vi /etc/ntp.conf ### ntp.confの設定 ### restrict default ignore restrict -6 default ignore restrict 127.0.0.1 restrict ntp1.sakura.ad.jp kod nomodify notrap nopeer noquery server ntp1.sakura.ad.jp  
</pre>
<p>rc.confへNTP起動の設定を導入します。 </p>
<pre class="terminal">  # vi /etc/rc.conf ### ntpd Setting ### ntpd_enable="YES" ntpd_config="/etc/ntp.conf"  
</pre>
<p>最初は狂いが大きいと思いますので、ntpd起動前に、まず、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AB%A1%BC%A5%CD%A5%EB">カーネル</a>クロックを強制的に修正します。 </p>
<pre class="terminal">  # ntpdate -b -v ntp1.sakura.ad.jp  
</pre>
<p>続いて、ハードウェアクロックを<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AB%A1%BC%A5%CD%A5%EB">カーネル</a>クロックに合わせます。 </p>
<pre class="terminal">  # hwclock --systohc  
</pre>
<p>そして、ntpdを起動します。 </p>
<pre class="terminal">  # /etc/rc.d/ntpd start Starting ntpd.  
</pre>
<p>しばらくすると時刻同期が開始されます。 </p>
<pre class="terminal">  # ntpq -p      remote           refid      st t when poll reach   delay   offset  jitter ============================================================================== *ntp1.sakura.ad. 133.243.238.163  2 u   12   64  377    8.565    0.058   0.114  
</pre>
<h5>メール(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Sendmail">Sendmail</a>)の設定</h5>
<p>最近では<a class="keyword" href="http://d.hatena.ne.jp/keyword/Postfix">Postfix</a>が人気なのでそちらを使っても良いですが、デフォルトでインストールされている<a class="keyword" href="http://d.hatena.ne.jp/keyword/Sendmail">Sendmail</a>を利用しました。 細かいオプションはいくつかありますが、ここでは外部からの<a class="keyword" href="http://d.hatena.ne.jp/keyword/SMTP">SMTP</a>接続は全て拒否し、内部からの<a class="keyword" href="http://d.hatena.ne.jp/keyword/SMTP">SMTP</a>接続、メール送信を可能となるよう、下記を/etc/rc.confへ記載します。 </p>
<pre class="terminal">  # vi /etc/rc.conf ### Sendmail Setting ### sendmail_enable="NO" sendmail_submit_enable="YES" sendmail_outbound_enable="YES" sendmail_msp_queue_enable="YES"  
</pre>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Sendmail">Sendmail</a>を起動させます。 </p>
<pre class="terminal">  # /etc/rc.d/sendmail onestart Starting sendmail.  </pre> <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ports">Ports</a> Collectionの取得</h5>
<p>最新の<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ports">Ports</a> Collectionを取得します。 </p>
<pre class="terminal">  # cd /tmp # fetch ftp://ftp.jp.freebsd.org/pub/FreeBSD/releases/amd64/9.1-RELEASE/ports.txz  
</pre>
<p>/usr配下へ展開します。 </p>
<pre class="terminal">  # tar xzpf ./ports.txz -C /usr/  
</pre>
<p>これで<a class="keyword" href="http://d.hatena.ne.jp/keyword/ports">ports</a>が利用可能になりましたので、必要なアプリケーションをインストールしてカスタマイズしてきます。<br>なお、portsnapによる<a class="keyword" href="http://d.hatena.ne.jp/keyword/ports">ports</a>を自動アップデートするような設定は特に必要なかったのでやっていません。 </p> <h4>おわりに</h4>
<p>以上で、さくらの<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPS">VPS</a>上での<a class="keyword" href="http://d.hatena.ne.jp/keyword/FreeBSD">FreeBSD</a> 9.1-RELEASEの初期設定は完了です。<br><a class="keyword" href="http://d.hatena.ne.jp/keyword/ipfw">ipfw</a>などもう少し見直すべき点もありそうですが、これからいろいろ調整していければと思います。 </p>
</body>

<!-- more -->


