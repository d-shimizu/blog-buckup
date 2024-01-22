---
layout: post
Categories:  ["tech"]
Description:  " はじめに  普段はあまりないかも知れませんが、HW構成などに関する詳細な情報を確認しなければならないことがあります。   例えば、サーバで物理的な故障が発生したとき、メーカー製のサーバであればサポートへ問い合わせて保守対応をしていただくこ"
Tags: ["システム運用"]
date: "2013-08-31T18:29:00+09:00"
title: "HWの情報を取得できるdmidecodeコマンドが結構便利だと思った"
author: "dshimizu"
archive: ["2013"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p>普段はあまりないかも知れませんが、HW構成などに関する詳細な情報を確認しなければならないことがあります。 </p>
<p>例えば、サーバで物理的な故障が発生したとき、メーカー製のサーバであればサポートへ問い合わせて保守対応をしていただくことになりますが、問い合わせを行ったとき、故障した箇所や内容によってそのHWに関する情報を求めらることがあります。<br>HWシリアル番号、<a class="keyword" href="http://d.hatena.ne.jp/keyword/BIOS">BIOS</a>のバージョン情報、搭載されているメモリモジュールのメモリサイズ・数…等です。 </p>
<p>そういった情報が分からないときにわざわざ電源を落として<a class="keyword" href="http://d.hatena.ne.jp/keyword/BIOS">BIOS</a>の画面等から調べる…といったことはなかなか出来ないことが多いですが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Linux">Linux</a>系OSであれば、そういった情報を知りたいときにdmidecodeというコマンドを利用できます。 </p>
<a name="more"></a><p><a class="keyword" href="http://d.hatena.ne.jp/keyword/RHEL">RHEL</a>系5系,6系であればdmidecodeパッケージをインストールする必要があります。 <br>※CentOS6.4 <a class="keyword" href="http://d.hatena.ne.jp/keyword/x86">x86</a>_64の例 </p>
<pre class="terminal">  % rpm -qf /usr/sbin/dmidecode dmidecode-2.11-2.el6.x86_64  
</pre>
<p>以下はdmidecodeコマンドについてのメモです。 </p>
<h4>dmidecodeコマンド</h4>
<p>dmidecodeはハードウェア情報をDMI(SMBIOS)に格納されている内容を解析して人間に読めるようなフォーマットとして報告します。<br>SMBIOS(System <a class="keyword" href="http://d.hatena.ne.jp/keyword/Management%20BIOS">Management BIOS</a>)とは簡単に言うとハードウェアの情報を<a class="keyword" href="http://d.hatena.ne.jp/keyword/BIOS">BIOS</a>に格納するための規格です。 </p>
<ul>  <li><a href="http://www.nongnu.org/dmidecode/">dmidecode</a></li>
</ul>
<p>オプションなしでコマンドを実行すると、取得可能な全ての情報が表示されます。<br>-qオプションで不明な情報や無効な情報、メーカー固有の情報を省いたり、-s オプションでキーワード文字を指定したり、-t でタイプを指定してできます。<br>※詳しくはmanを参照してください。 </p>
<h5>-sオプションで指定可能なキーワード</h5>
<pre class="terminal">  # dmidecode -s dmidecode: option requires an argument -- 's' Valid string keywords are:   bios-vendor   bios-version   bios-release-date   system-manufacturer   system-product-name   system-version   system-serial-number   system-uuid   baseboard-manufacturer   baseboard-product-name   baseboard-version   baseboard-serial-number   baseboard-asset-tag   chassis-manufacturer   chassis-type   chassis-version   chassis-serial-number   chassis-asset-tag   processor-family   processor-manufacturer   processor-version   processor-frequency  
</pre>
<h5>-tオプションで指定可能なタイプ</h5>
<pre class="terminal">   # dmidecode -t dmidecode: option requires an argument -- 't' Type number or keyword expected Valid type keywords are:   bios   system   baseboard   chassis   processor   memory   cache   connector   slot  
</pre>
<h4>動作確認</h4>
<h5>環境</h5>
<p>実行環境は以下です。 </p>
<table>
<tr>  <td bgcolor="#cccccc">プラットフォーム</td>  <td>さくらの<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPS">VPS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/SSD">SSD</a> 1Gモデル</td>
</tr>
<tr>  <td bgcolor="#cccccc">OS</td>  <td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/CentOS">CentOS</a> 6.4 <a class="keyword" href="http://d.hatena.ne.jp/keyword/x86">x86</a>_64 (64bit)</td>
</tr>
</table> <h5>実行</h5>
<p>dmidecodeコマンドを実行していくつか情報を取得してみます。 </p>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/BIOS">BIOS</a>の情報一覧を取得してみます。<a class="keyword" href="http://d.hatena.ne.jp/keyword/KVM">KVM</a>ゲストから実行したものですが、実際の<a class="keyword" href="http://d.hatena.ne.jp/keyword/BIOS">BIOS</a>から取得できた情報なのか分かりません... </p>
<pre class="terminal">  # dmidecode -t bios # dmidecode 2.11 SMBIOS 2.4 present.  Handle 0x0000, DMI type 0, 24 bytes BIOS Information  Vendor: Seabios  Version: 0.5.1  Release Date: 01/01/2007  Address: 0xE8000  Runtime Size: 96 kB  ROM Size: 64 kB  Characteristics:   BIOS characteristics not supported   Targeted content distribution is supported  BIOS Revision: 1.0  
</pre>
<p>サーバのモデル名を取得してみます。<a class="keyword" href="http://d.hatena.ne.jp/keyword/KVM">KVM</a>環境だと以下のような情報が取得できました。 </p>
<pre class="terminal">  # dmidecode -s system-product-name KVM  
</pre>
<p>HWのシリアルナンバーを取得してみます。<a class="keyword" href="http://d.hatena.ne.jp/keyword/KVM">KVM</a>の環境だと取得できないようです。 </p>
<pre class="terminal">  # dmidecode -s system-serial-number Not Specified  
</pre>
<h4>おわりに</h4>
<p>HWに関する情報を取得するdmidecodeコマンドについて簡単に記載しました。 </p>
<p>システムを停止したり、メーカーの管理ツールを使ったり、構成情報の資料を見たりしないと確認できないと思っていた情報でもコマンドで簡単に確認できるものもあります。<br>上記では<a class="keyword" href="http://d.hatena.ne.jp/keyword/KVM">KVM</a>環境で実行してみました結果ですが、物理マシン上で実行すると、システム製造元、モデル名、シリアル番号、<a class="keyword" href="http://d.hatena.ne.jp/keyword/BIOS">BIOS</a> バージョンやメーカー固有の情報(<a class="keyword" href="http://d.hatena.ne.jp/keyword/DELL">DELL</a>製HWだとサービスタグ等)やメモリスロットに何GBのメモリモジュールが挿さっているかといった情報まで、細かい情報が確認できます。<br>管理がされていない詳細不明なHWでも情報を整理していけるかもしれません。 </p>
</body>

<!-- more -->


