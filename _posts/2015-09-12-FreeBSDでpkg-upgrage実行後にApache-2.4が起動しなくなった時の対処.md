---
layout: post
Categories:  ["tech"]
Description:  " はじめに  FreeBSDでpkg upgrageを実行した後に、Apacheが以下のエラーを出力するようになって起動しなくなりました。    # apachectl configtest Performing sanity check "
Tags: ["FreeBSD", "Apache"]
date: "2015-09-12T17:53:00+09:00"
title: "FreeBSDでpkg upgrage実行後にApache 2.4が起動しなくなった時の対処"
author: "dshimizu"
archive: ["2015"]
draft: "true"
---

<body>
<h4>はじめに</h4>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/FreeBSD">FreeBSD</a>で<code>pkg upgrage</code>を実行した後に、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a>が以下のエラーを出力するようになって起動しなくなりました。 </p>
<pre class="terminal">  # apachectl configtest Performing sanity check on apache24 configuration: AH00534: httpd: Configuration error: No MPM loaded.  </pre> <pre class="terminal">  # service apache24 restart Performing sanity check on apache24 configuration: AH00534: httpd: Configuration error: No MPM loaded.  </pre> <a name="more"></a> <h4>MPMモジュールの読み込み</h4>
<p>出力されるエラーメッセージから調査すると、どうもMPMのモジュールが読み込まれていないようでした。 </p>
<ul>  <li><a href="https://httpd.apache.org/docs/2.4/en/mpm.html#dynamic">Multi-Processing Modules (MPMs)</a></li>
</ul>
<p>調べてみると、<code>httpd.conf</code>にMPMのモジュールが<code>LoadModule mpm_prefork_module libexec/apache24/mod_mpm_prefork.so</code>が読み込まれておらず(元のファイルがどうだったか覚えてませんが)、これが原因のようでした。 </p>
<p>MPMのモジュール読み込みの指定は以下３つのうちいずれかです。 </p>
<pre class="terminal">  LoadModule mpm_event_module libexec/apache24/mod_mpm_event.so LoadModule mpm_prefork_module libexec/apache24/mod_mpm_prefork.so LoadModule mpm_worker_module libexec/apache24/mod_mpm_worker.so  </pre> <pre class="terminal">  # cd /usr/local/etc/apache24 # grep mpm ./* ./httpd.conf:#Include etc/apache24/extra/httpd-mpm.conf ./httpd.conf.org:#Include etc/apache24/extra/httpd-mpm.conf ./httpd.conf.sample:#LoadModule mpm_event_module libexec/apache24/mod_mpm_event.so ./httpd.conf.sample:LoadModule mpm_prefork_module libexec/apache24/mod_mpm_prefork.so ./httpd.conf.sample:#LoadModule mpm_worker_module libexec/apache24/mod_mpm_worker.so ./httpd.conf.sample: ./httpd.conf.sample: ./httpd.conf.sample:#Include etc/apache24/extra/httpd-mpm.conf ./mime.types:application/vnd.blueice.multipass  mpm ./mime.types.sample:application/vnd.blueice.multipass  mpm  </pre> <p><a class="keyword" href="http://d.hatena.ne.jp/keyword/FreeBSD">FreeBSD</a>の<code>pkg</code>コマンドでApache24をインストールした場合、デフォルトでは<code>prefork</code>のオプションが有効になっているので、<code>LoadModule mpm_prefork_module libexec/apache24/mod_mpm_prefork.so</code>をコンフィグファイル内に記述し、起動するとうまくいきました。 </p> <pre class="terminal">  # apachectl configtest Performing sanity check on apache24 configuration: Syntax OK  </pre> <pre class="terminal">  # service apache24 restart Performing sanity check on apache24 configuration: Syntax OK Starting apache24.  </pre> <h4>おわりに</h4>
<p><code>pkg upgrade</code>実行前はバックアップを忘れずに。 </p>
</body>

<!-- more -->


