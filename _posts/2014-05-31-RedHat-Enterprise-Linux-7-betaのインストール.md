---
layout: post
Categories:  ["tech"]
Description:  " はじめに  昨年(2013/12/11)公開されたRedHat Enterprise Linux 7betaを今更ながら触ってみようと思い、VirtualBoxを使ってインストールしてみました。  Red Hat | Red Hat An"
Tags: ["RHEL 7"]
date: "2014-05-31T23:05:00+09:00"
title: "RedHat Enterprise Linux 7 betaのインストール"
author: "dshimizu"
archive: ["2014"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p>昨年(2013/12/11)公開された<a class="keyword" href="http://d.hatena.ne.jp/keyword/RedHat%20Enterprise%20Linux">RedHat Enterprise Linux</a> 7betaを今更ながら触ってみようと思い、<a class="keyword" href="http://d.hatena.ne.jp/keyword/VirtualBox">VirtualBox</a>を使ってインストールしてみました。 </p>
<a href="http://www.redhat.com/about/news/archive/2013/12/red-hat-announces-availability-of-red-hat-enterprise-linux-7-beta">Red Hat | Red Hat Announces Availability of Red Hat Enterprise Linux 7 Beta</a><a name="more"></a><h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/RedHat%20Enterprise%20Linux">RedHat Enterprise Linux</a> 7 beta</h4>
<p>「<a class="keyword" href="http://d.hatena.ne.jp/keyword/Red%20Hat%20Enterprise%20Linux">Red Hat Enterprise Linux</a>」は、米<a class="keyword" href="http://d.hatena.ne.jp/keyword/Red%20Hat">Red Hat</a>社が開発および提供している<a class="keyword" href="http://d.hatena.ne.jp/keyword/Linux%A5%C7%A5%A3%A5%B9%A5%C8%A5%EA%A5%D3%A5%E5%A1%BC%A5%B7%A5%E7%A5%F3">Linuxディストリビューション</a>で、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Fedora%20Project">Fedora Project</a>が開発している<a class="keyword" href="http://d.hatena.ne.jp/keyword/Linux%A5%C7%A5%A3%A5%B9%A5%C8%A5%EA%A5%D3%A5%E5%A1%BC%A5%B7%A5%E7%A5%F3">Linuxディストリビューション</a>「<a class="keyword" href="http://d.hatena.ne.jp/keyword/Fedora">Fedora</a>」の開発の結果を取り込んでいます。<br>今回の<a class="keyword" href="http://d.hatena.ne.jp/keyword/RedHat%20Enterprise%20Linux">RedHat Enterprise Linux</a> 7 betaは<a class="keyword" href="http://d.hatena.ne.jp/keyword/Fedora">Fedora</a> 19がベースとされており、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Linux">Linux</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AB%A1%BC%A5%CD%A5%EB">カーネル</a>3.10が使われています。<br>また、<a class="keyword" href="http://d.hatena.ne.jp/keyword/RedHat%20Enterprise%20Linux">RedHat Enterprise Linux</a> 7から<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AD%A5%C6%A5%AF%A5%C1%A5%E3">アーキテクチャ</a>が64bitのみになったようです。 </p> <h5>インストールメディアの取得</h5>
<p>ISOイメージは以下からダウンロードできます。 </p>
<ul>  <li><a href="http://ftp.redhat.com/redhat/rhel/beta/7/x86_64/iso/">Index of /redhat/rhel/beta/7/x86_64/iso</a></li>
</ul> <h5>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%C8%A1%BC%A5%E9">インストーラ</a>の起動</h5>
<p>VirualBox上に作成した<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B2%BE%C1%DB%A5%DE%A5%B7%A5%F3">仮想マシン</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/IDE">IDE</a>にISOイメージファイルをマウントさせ、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B2%BE%C1%DB%A5%DE%A5%B7%A5%F3">仮想マシン</a>を起動させます。以下のような画面が表示されます。<br>Anacondaの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%C8%A1%BC%A5%E9">インストーラ</a>画面がずいぶん変わっています。 </p>
<p>分かりにくいですが、デフォルトでは<code>Test this media &amp; install Red Hat Enterprise Linux 7.0</code>が選択されていますので、そのまま起動するとメディアのテストが実行されます。<br>メディアテストは実行した方が良いですが、不要な場合は<code>↑</code><code>↓</code>キーで<code>Install Red Hat Enterprise Linux 7.0</code>を選択します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://4.bp.blogspot.com/-pn2VPtXDFqY/U4nGWqYwZaI/AAAAAAAAAiw/4GQcOvc9l1k/s1600/rhel7beta01.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://4.bp.blogspot.com/-pn2VPtXDFqY/U4nGWqYwZaI/AAAAAAAAAiw/4GQcOvc9l1k/s640/rhel7beta01.png"></a></div> <h5>メディアのテスト</h5>
<p>メディアのテストを実行すると以下のような画面が表示されます。手元の環境では数分で終わりました。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://2.bp.blogspot.com/-R9DixQ5j1bw/U4nJJGLUH7I/AAAAAAAAAi4/7jGkAWb9iOI/s1600/rhel7beta02.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220322/20220322113736.png"></a></div> <h5>言語の選択</h5>
<p>インストール作業時に使用する言語を選択します。最初は英語<code>Engilish</code>が選択されていますが、日本語の方がやりやすいので<code>Japanese</code>を選択して日本語表記にします。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://1.bp.blogspot.com/-m2eQXZsTq58/U4nJ7Qez92I/AAAAAAAAAjA/o83X9D6LNFQ/s1600/screenshot-0000.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://1.bp.blogspot.com/-m2eQXZsTq58/U4nJ7Qez92I/AAAAAAAAAjA/o83X9D6LNFQ/s640/screenshot-0000.png"></a></div>
<p>日本語表記にしたところで<code>続行</code>ボタンを押下します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://1.bp.blogspot.com/-KlJJ_IqaJ9c/U4nKr3yLJRI/AAAAAAAAAjI/LjdK2-hMG9M/s1600/screenshot-0002.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://1.bp.blogspot.com/-KlJJ_IqaJ9c/U4nKr3yLJRI/AAAAAAAAAjI/LjdK2-hMG9M/s640/screenshot-0002.png"></a></div> <h5>インストールの概要 画面</h5>
<p>以下のような画面になります。<a class="keyword" href="http://d.hatena.ne.jp/keyword/Red%20Hat%20Enterprise%20Linux">Red Hat Enterprise Linux</a> 5や6のように各設定画面が順番に表示されるのではなく、設定したい項目を選択して設定を行う形になっています。<br>順に設定を行っていきます。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://3.bp.blogspot.com/-f8pYIVsWWzI/U4nMJWmg8vI/AAAAAAAAAjc/HTRv4Shfnwk/s1600/screenshot-0004.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://3.bp.blogspot.com/-f8pYIVsWWzI/U4nMJWmg8vI/AAAAAAAAAjc/HTRv4Shfnwk/s640/screenshot-0004.png"></a></div> <h5>地域設定</h5>
<h6>日付と時刻の設定</h6>
<p>日付と時刻の設定を行います。<br>ここでは特にデフォルトのまま変更したい点はなかったので、このまま画面左上の<code>完了</code>ボタンを押下します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://2.bp.blogspot.com/-uCnEiHaSQcc/U4nMGGXhPXI/AAAAAAAAAjU/xidmTqpk3Ao/s1600/screenshot-0005.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220322/20220322113819.png"></a></div> <h6>キーボードの設定</h6>
<p>キーボードの設定を行います。<br><code>日本語</code>しか選択項目がありませんので、<code>日本語</code>を選択します。 キーボードレイアウトを選択したら、画面左上の<code>完了</code>ボタンを押下します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://4.bp.blogspot.com/-rs0gQIRwQZg/U4nOLukWPWI/AAAAAAAAAjo/ERygGXml-RM/s1600/screenshot-0006.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://4.bp.blogspot.com/-rs0gQIRwQZg/U4nOLukWPWI/AAAAAAAAAjo/ERygGXml-RM/s640/screenshot-0006.png"></a></div> <h6>言語サポートの設定</h6>
<p>インストール完了後の<a class="keyword" href="http://d.hatena.ne.jp/keyword/Red%20Hat%20Enterprise%20Linux">Red Hat Enterprise Linux</a> 7 beta 環境で使う言語を選択します。 ここでは<code>日本語</code>のみ選択します。<br>使用言語を選択したら、画面左上の<code>完了</code>ボタンを押下します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://3.bp.blogspot.com/-2U-_YXO34Og/U4nPOwzNGJI/AAAAAAAAAj0/IuN7KLpO7dA/s1600/screenshot-0007.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://3.bp.blogspot.com/-2U-_YXO34Og/U4nPOwzNGJI/AAAAAAAAAj0/IuN7KLpO7dA/s640/screenshot-0007.png"></a></div> <h5>ソフトウェアの設定</h5>
<h6>インストールソースの設定</h6>
<p>外部<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EA%A5%DD%A5%B8%A5%C8%A5%EA">リポジトリ</a>の指定等を行います。特に変更点はないので、このまま画面左上の<code>完了</code>ボタンを押下します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://3.bp.blogspot.com/-MAruquD4fAE/U4nRGZI6eoI/AAAAAAAAAkA/u_D7xZ0yfXE/s1600/screenshot-0008.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://3.bp.blogspot.com/-MAruquD4fAE/U4nRGZI6eoI/AAAAAAAAAkA/u_D7xZ0yfXE/s640/screenshot-0008.png"></a></div> <h6>ソフトウェアの選択の設定</h6>
<p>インストールするソフトウェアを選択します。<br><a class="keyword" href="http://d.hatena.ne.jp/keyword/Red%20Hat%20Enterprise%20Linux">Red Hat Enterprise Linux</a> 5や6のように細かいソフトウェア選択はできなくなったようです。<br>ここでは<code>最小限のインストール</code>を選択します。<br>インストールするソフトウェアの選択が完了したら、画面左上の<code>完了</code>ボタンを押下します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://4.bp.blogspot.com/-vShWqy6Dvvg/U4nR7xtpPYI/AAAAAAAAAkI/d_TnGBA6JhA/s1600/screenshot-0009.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://4.bp.blogspot.com/-vShWqy6Dvvg/U4nR7xtpPYI/AAAAAAAAAkI/d_TnGBA6JhA/s640/screenshot-0009.png"></a></div> <h5>システム</h5>
<h6>インストール先の設定</h6>
<p>インストール先の設定と<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D1%A1%BC%A5%C6%A5%A3%A5%B7%A5%E7%A5%F3">パーティション</a>の設定を行います。 </p>
<p>インストール先デ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A5%A4%A5%B9">バイス</a>にローカルの標準ディスクを選択します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://3.bp.blogspot.com/-OYExHXTRphk/U4nTahXs1DI/AAAAAAAAAkU/W9fAD9B-Zmc/s1600/screenshot-0010.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://3.bp.blogspot.com/-OYExHXTRphk/U4nTahXs1DI/AAAAAAAAAkU/W9fAD9B-Zmc/s640/screenshot-0010.png"></a></div>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D1%A1%BC%A5%C6%A5%A3%A5%B7%A5%E7%A5%F3">パーティション</a>の設定を行います。<br><code>パーティション構成を行いたい</code>を選択し、画面左下の<code>すべてのディスクの要約とブートローダー...(F)</code>を押下します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://4.bp.blogspot.com/-9EB7UpPndxo/U4nUKnQ1e1I/AAAAAAAAAks/fLD1c2AdkAg/s1600/screenshot-0011.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://4.bp.blogspot.com/-9EB7UpPndxo/U4nUKnQ1e1I/AAAAAAAAAks/fLD1c2AdkAg/s640/screenshot-0011.png"></a></div>
<p>手動<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D1%A1%BC%A5%C6%A5%A3%A5%B7%A5%E7%A5%F3">パーティション</a>設定画面になります。<br>ディスクサイズが小さいのでここでは細かい指定をせず、<code>ここをクリックして自動的に作成します(C)</code>を押下し、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D1%A1%BC%A5%C6%A5%A3%A5%B7%A5%E7%A5%F3">パーティション</a>を自動で作成します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://3.bp.blogspot.com/-cvaVRsGjNpw/U4nUKl5mrFI/AAAAAAAAAkc/sJIlhrbgaDY/s1600/screenshot-0012.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://3.bp.blogspot.com/-cvaVRsGjNpw/U4nUKl5mrFI/AAAAAAAAAkc/sJIlhrbgaDY/s640/screenshot-0012.png"></a></div>
<p>以下のような<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D1%A1%BC%A5%C6%A5%A3%A5%B7%A5%E7%A5%F3">パーティション</a>構成になります。<br>デフォルトの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%A1%A5%A4%A5%EB%A5%B7%A5%B9%A5%C6%A5%E0">ファイルシステム</a>にxfsが選択されています。 </p>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D1%A1%BC%A5%C6%A5%A3%A5%B7%A5%E7%A5%F3">パーティション</a>構成に問題がなければ、画面左上の<code>完了</code>ボタンを押下します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://4.bp.blogspot.com/-LeVKzDAHlU8/U4nUKtjVLmI/AAAAAAAAAko/7cxiC_nSX08/s1600/screenshot-0013.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://4.bp.blogspot.com/-LeVKzDAHlU8/U4nUKtjVLmI/AAAAAAAAAko/7cxiC_nSX08/s640/screenshot-0013.png"></a></div>
<p>以下のような画面が表示されますので、問題なければ<code>変更を適用する</code>ボタンを押下し、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D1%A1%BC%A5%C6%A5%A3%A5%B7%A5%E7%A5%F3">パーティション</a>設定をディスクに反映させます。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://4.bp.blogspot.com/-wTrK6m_3J6w/U4nUMmVjBeI/AAAAAAAAAk0/LJHKfhep7ZQ/s1600/screenshot-0015.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://4.bp.blogspot.com/-wTrK6m_3J6w/U4nUMmVjBeI/AAAAAAAAAk0/LJHKfhep7ZQ/s640/screenshot-0015.png"></a></div> <h6>ネットワークとホスト名の設定</h6>
<p>ネットワーク環境に応じてネットワーク設定を行い、必要ならホスト名の設定を行います。<br>インストール後に設定を行うことにして、ここでは特に何もせず、画面左上の<code>完了</code>ボタンを押下します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://2.bp.blogspot.com/-R3ovQznC7RI/U4nUMgTT-EI/AAAAAAAAAk4/_bAxIPLpsIE/s1600/screenshot-0016.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220322/20220322113732.png"></a></div> <p>全ての設定が完了したら、インストールの概要画面右下の<code>インストールの開始</code>ボタンを押下して、インストールを開始します。 </p> <h5>インストール</h5>
<p>インストールが開始されると以下の画面になりますが、rootパスワードの設定とユーザの作成を行わなければインストールが完了しないので設定を行います。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://3.bp.blogspot.com/-ps_QAaahQ88/U4nbBR0ulAI/AAAAAAAAAlc/3Ewdc7BbIUM/s1600/screenshot-0017.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://3.bp.blogspot.com/-ps_QAaahQ88/U4nbBR0ulAI/AAAAAAAAAlc/3Ewdc7BbIUM/s640/screenshot-0017.png"></a></div> <h6>rootパスワードの設定</h6>
<p>rootパスワードの設定を行い、画面左上の<code>完了</code>ボタンを押下します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://2.bp.blogspot.com/-LBxRhH4ik1Y/U4nbBfJSB4I/AAAAAAAAAlY/d8GZl0axapg/s1600/screenshot-0018.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20220322/20220322113711.png"></a></div> <h6>ユーザの作成</h6>
<p>OSユーザ名とパスワードの設定をし、画面左上の<code>完了</code>ボタンを押下します。 </p>
<div class="separator" style="clear: both; text-align: center;"><a href="http://3.bp.blogspot.com/-nkgQ5cEb2xk/U4ndTymgaZI/AAAAAAAAAlw/3oyt03L-kGg/s1600/screenshot-0020.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://3.bp.blogspot.com/-nkgQ5cEb2xk/U4ndTymgaZI/AAAAAAAAAlw/3oyt03L-kGg/s640/screenshot-0020.png"></a></div> <p>全ての設定が完了したらインストールが完了するので、その後システムを再起動し、その後OSが起動すれば完了です。 </p>
<h4>おわりに</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Red%20Hat%20Enterprise%20Linux">Red Hat Enterprise Linux</a> 7からXFS<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%A1%A5%A4%A5%EB%A5%B7%A5%B9%A5%C6%A5%E0">ファイルシステム</a>の追加やsystemdによるシステム管理(init.d<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>の廃止)、ネットワーク設定の変更など大幅な変更がされていますが、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%B9%A5%C8%A1%BC%A5%E9">インストーラ</a>自体も大幅に変わったようです。<br>利用していくにはいろいろと変更内容を把握しておく方が良いかも知れません。 </p>
</body>

<!-- more -->


