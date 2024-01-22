---
title: "OSRM Frontend, Backend を使って、ローカルでマップを表示されてルート検索をやってみた"
date: 2022-11-23
slug: "OSRM Frontend, Backend を使って、ローカルでマップを表示されてルート検索をやってみた"
category: blog
tags: [OSRM,Tech,OSM]
---
<h2 id="はじめに">はじめに</h2>

<p>オープンデータの地理情報を作るプロジェクトである <a class="keyword" href="https://d.hatena.ne.jp/keyword/OSM">OSM</a>(<a class="keyword" href="https://d.hatena.ne.jp/keyword/OpenStreetMap">OpenStreetMap</a>) のデータをもとに、ルートの検索を行うためのエンジンである OSRM (Open Source Routing Machine) がどんなものかを浅く理解するために、ちょっと動かしてみた。</p>

<h2 id="OSRMOpen-Source-Routing-Machine">OSRM(Open Source Routing Machine)</h2>

<p>OSRM は <a class="keyword" href="https://d.hatena.ne.jp/keyword/OpenStreetMap">OpenStreetMap</a> (<a class="keyword" href="https://d.hatena.ne.jp/keyword/OSM">OSM</a>) プロジェクトのマップデータをもとにルート検索を行える <a class="keyword" href="https://d.hatena.ne.jp/keyword/C%2B%2B">C++</a>のルーティングエンジンらしい。</p>

<ul>
<li><a href="https://project-osrm.org/">Project OSRM</a></li>
<li><a href="http://project-osrm.org/docs/v5.24.0/api/#intersection-object">OSRM API Documentation</a></li>
<li><a href="https://en.wikipedia.org/wiki/Open_Source_Routing_Machine">Open Source Routing Machine - Wikipedia</a></li>
</ul>


<p>OSRMのプロジェクトとしていくつかアプリケーションが公開されていて、そのうちの以下の 2 つを利用する。</p>

<ul>
<li><a href="https://github.com/Project-OSRM/osrm-backend">GitHub - Project-OSRM/osrm-backend: Open Source Routing Machine - C++ backend</a></li>
<li><a href="https://github.com/Project-OSRM/osrm-frontend">GitHub - Project-OSRM/osrm-frontend: Modular rewrite of the OSRM frontend using LRM</a></li>
</ul>


<p>OSRM Backend は <a class="keyword" href="https://d.hatena.ne.jp/keyword/C%2B%2B">C++</a> のルーティングエンジンで、HTTP <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> も組み込まれているので、<a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> 経由で結果を取得できる。OSRM Frontendは、Web View で Backend と組み合わせて結果をブラウザ上に表示されたマップから確認できる。</p>

<p>どちらも Docker で利用可能になっているので、簡単に起動できる。</p>

<h2 id="試す環境">試す環境</h2>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/macOS">macOS</a> で試す。</p>

<pre class="code" data-lang="" data-unlink>% sw_vers
ProductName:    macOS
ProductVersion: 12.6.2</pre>




<pre class="code" data-lang="" data-unlink>% docker version
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

Server: Docker Desktop 4.14.0 (91374)
 Engine:
  Version:          20.10.21
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.18.7
  Git commit:       3056208
  Built:            Tue Oct 25 18:00:19 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.9
  GitCommit:        1c90a442489720eec95342e1789ee8a5e1b9536f
 runc:
  Version:          1.1.4
  GitCommit:        v1.1.4-0-g5fd4c4d
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0</pre>


<h2 id="構築">構築</h2>

<p>前述の通り Docker イメージが利用可能なので、それを利用する。</p>

<p>Backend の方は「QuickStart」の項目をベースに実施する。</p>

<ul>
<li><a href="https://github.com/Project-OSRM/osrm-backend#quick-start">GitHub - Project-OSRM/osrm-backend: Open Source Routing Machine - C++ backend</a></li>
</ul>


<p>Frontend の方は「Using Docker」の項目をベースに実施する。</p>

<ul>
<li><a href="https://github.com/Project-OSRM/osrm-frontend#using-docker">GitHub - Project-OSRM/osrm-frontend: Modular rewrite of the OSRM frontend using LRM</a></li>
</ul>


<p>実施内容についてのもう少し細かい記載が下記にある。</p>

<ul>
<li><a href="https://github.com/Project-OSRM/osrm-backend/wiki/Running-OSRM">Running OSRM &middot; Project-OSRM/osrm-backend Wiki &middot; GitHub</a></li>
</ul>


<h3 id="作業ディレクトリの作成">作業<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リの作成</h3>

<p>適当に作業<a class="keyword" href="https://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成して移動する。</p>

<pre class="code" data-lang="" data-unlink>% mkdir osrm-work

% cd osrm-work</pre>


<h3 id="OSM-マップデータの取得"><a class="keyword" href="https://d.hatena.ne.jp/keyword/OSM">OSM</a> マップデータの取得</h3>

<p><a class="keyword" href="https://d.hatena.ne.jp/keyword/OSM">OSM</a> のマップデータを取得する。いろんな地域のマップデータがあって、範囲が広いとサイズも大きい。ここでは関東エリアのマップを取得する。</p>

<pre class="code" data-lang="" data-unlink>$ wget https://download.geofabrik.de/asia/japan/kanto-latest.osm.pbf</pre>


<h3 id="OSRM-Backend-の構築">OSRM Backend の構築</h3>

<p>OSRM Backend を構築する。Docker Desktop に割り当てるメモリリソースが 4 GB くらいは割り当ててないと、後続処理が失敗する。</p>

<p>まず、 <a class="keyword" href="https://d.hatena.ne.jp/keyword/OSM">OSM</a> のデータファイルはいろんな情報が含まれているため、ルート検索用の形式で抽出する。</p>

<pre class="code" data-lang="" data-unlink>% docker run -t -v &#34;${PWD}:/data&#34; ghcr.io/project-osrm/osrm-backend osrm-extract -p /opt/car.lua /data/kanto-latest.osm.pbf</pre>


<p>以下のようなファイルが生成される。</p>

<pre class="code" data-lang="" data-unlink>% ls                                                                                                                                                                                                                  [osrm]
kanto-latest.osm.pbf                kanto-latest.osrm.edges             kanto-latest.osrm.names             kanto-latest.osrm.tld
kanto-latest.osrm               kanto-latest.osrm.enw               kanto-latest.osrm.nbg_nodes         kanto-latest.osrm.tls
kanto-latest.osrm.cnbg              kanto-latest.osrm.fileIndex         kanto-latest.osrm.properties            kanto-latest.osrm.turn_duration_penalties
kanto-latest.osrm.cnbg_to_ebg           kanto-latest.osrm.geometry          kanto-latest.osrm.ramIndex          kanto-latest.osrm.turn_penalties_index
kanto-latest.osrm.ebg               kanto-latest.osrm.icd               kanto-latest.osrm.restrictions          kanto-latest.osrm.turn_weight_penalties
kanto-latest.osrm.ebg_nodes         kanto-latest.osrm.maneuver_overrides        kanto-latest.osrm.timestamp

% ls  | wc -l
      23</pre>


<p><code>.osrm</code> のファイルに対して、<a class="keyword" href="https://d.hatena.ne.jp/keyword/%BA%C6%B5%A2">再帰</a>的にセル分割する処理を実行する。(詳しくわかっていない)</p>

<pre class="code" data-lang="" data-unlink>% docker run -t -v &#34;${PWD}:/data&#34; ghcr.io/project-osrm/osrm-backend osrm-partition /data/kanto-latest.osrm</pre>


<p><code>kanto-latest.osrm.cells</code> と <code>kanto-latest.osrm.partition</code> の2つのファイルが追加で生成される。</p>

<pre class="code" data-lang="" data-unlink>% ls
kanto-latest.osm.pbf                kanto-latest.osrm.edges             kanto-latest.osrm.nbg_nodes         kanto-latest.osrm.tls
kanto-latest.osrm               kanto-latest.osrm.enw               kanto-latest.osrm.partition         kanto-latest.osrm.turn_duration_penalties
kanto-latest.osrm.cells             kanto-latest.osrm.fileIndex         kanto-latest.osrm.properties            kanto-latest.osrm.turn_penalties_index
kanto-latest.osrm.cnbg              kanto-latest.osrm.geometry          kanto-latest.osrm.ramIndex          kanto-latest.osrm.turn_weight_penalties
kanto-latest.osrm.cnbg_to_ebg           kanto-latest.osrm.icd               kanto-latest.osrm.restrictions
kanto-latest.osrm.ebg               kanto-latest.osrm.maneuver_overrides        kanto-latest.osrm.timestamp
kanto-latest.osrm.ebg_nodes         kanto-latest.osrm.names             kanto-latest.osrm.tld

% ls | wc -l
      25</pre>


<p>ルート毎のウェイトを計算してセルをカスタマイズする処理を実行する。(詳しくわかっていない)</p>

<pre class="code" data-lang="" data-unlink>% docker run -t -v &#34;${PWD}:/data&#34; ghcr.io/project-osrm/osrm-backend osrm-customize /data/kanto-latest.osrm</pre>


<p>さらに追加で <code>kanto-latest.osrm.mldgr</code>, <code>kanto-latest.osrm.cell_metrics</code>, <code>kanto-latest.osrm.datasource_names</code> の3つのファイルが生成される。</p>

<pre class="code" data-lang="" data-unlink>% ls
kanto-latest.osm.pbf                kanto-latest.osrm.ebg               kanto-latest.osrm.maneuver_overrides        kanto-latest.osrm.restrictions
kanto-latest.osrm               kanto-latest.osrm.ebg_nodes         kanto-latest.osrm.mldgr             kanto-latest.osrm.timestamp
kanto-latest.osrm.cell_metrics          kanto-latest.osrm.edges             kanto-latest.osrm.names             kanto-latest.osrm.tld
kanto-latest.osrm.cells             kanto-latest.osrm.enw               kanto-latest.osrm.nbg_nodes         kanto-latest.osrm.tls
kanto-latest.osrm.cnbg              kanto-latest.osrm.fileIndex         kanto-latest.osrm.partition         kanto-latest.osrm.turn_duration_penalties
kanto-latest.osrm.cnbg_to_ebg           kanto-latest.osrm.geometry          kanto-latest.osrm.properties            kanto-latest.osrm.turn_penalties_index
kanto-latest.osrm.datasource_names      kanto-latest.osrm.icd               kanto-latest.osrm.ramIndex          kanto-latest.osrm.turn_weight_penalties

% ls | wc -l
      28</pre>


<p>OSRM <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> 用の HTTP server を起動する。</p>

<pre class="code" data-lang="" data-unlink>% % docker run -t -i -p 5000:5000 -v &#34;${PWD}:/data&#34; ghcr.io/project-osrm/osrm-backend osrm-routed --algorithm mld /data/kanto-latest.osrm</pre>


<p>正常に起動すれば以下のような出力がなされる。</p>

<pre class="code" data-lang="" data-unlink>[info] starting up engines, v5.26.0
[info] Threads: 2
[info] IP address: 0.0.0.0
[info] IP port: 5000
[info] http 1.1 compression handled by zlib version 1.2.8
[info] Listening on: 0.0.0.0:5000
[info] running and waiting for requests</pre>


<p>OSRM Backend の構築はこれで完了。</p>

<h3 id="OSRM-Frontend-の構築">OSRM Frontend の構築</h3>

<p>続いて OSRM の Frontend を起動する。ポートはドキュメント通り <code>9966</code> としている。</p>

<pre class="code" data-lang="" data-unlink>% docker run -p 9966:9966 ghcr.io/project-osrm/osrm-frontend </pre>


<p>以下のような出力がなされて起動する。</p>

<pre class="code" data-lang="" data-unlink>&gt; osrm-frontend@0.4.0 start /src
&gt; npm run build &amp;&amp; npm run start-index


&gt; osrm-frontend@0.4.0 build /src
&gt; npm run replace &amp;&amp; npm run compile &amp;&amp; cp node_modules/leaflet/dist/leaflet.css css/leaflet.css


&gt; osrm-frontend@0.4.0 replace /src
&gt; node ./scripts/replace.js


&gt; osrm-frontend@0.4.0 compile /src
&gt; browserify -d src/index.js -s osrm &gt; bundle.raw.js &amp;&amp; uglifyjs bundle.raw.js -c -m --source-map=bundle.js.map -o bundle.js</pre>


<p>この状態でマップを表示させた場合、初期表示が ワシントンD.C とかになっているので、変更する。
デフォルトのイメージをそのまま使っているので、とりあえずDocker コンテナに入ってファイルを変更する。</p>

<pre class="code" data-lang="" data-unlink>% docker exec -it 8be8fe263398 /bin/sh</pre>


<p>ファイル編集用に <a class="keyword" href="https://d.hatena.ne.jp/keyword/vim">vim</a> をインストールする。</p>

<pre class="code" data-lang="" data-unlink># apk update

# apk add vim</pre>


<p><code>/src/src/leaflet_options.js</code> というファイルがあるので、下記の箇所を修正する。
下記の内容だと東京駅付近がデフォルト表示となる。</p>

<p>Frontend 側から呼び出される Backend の <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> はデフォルトではルート検索用の <code>http://localhost:5000/route/v1</code> が利用されるようになっている。</p>

<pre class="code" data-lang="" data-unlink>--- src/leaflet_options.js.bak
+++ src/leaflet_options.js
@@ -21,7 +21,7 @@

 module.exports = {
   defaultState: {
-    center: L.latLng(38.8995,-77.0269),
+    center: L.latLng(35.679767,139.768429),
     zoom: 13,
     waypoints: [],
     language: &#39;en&#39;,</pre>


<p>これでとりあえず Front の方も構築完了となる。</p>

<h2 id="表示">表示</h2>

<p><code>http://localhost:9966</code> へアクセスする。
画面左下の設定項目で、「<a class="keyword" href="https://d.hatena.ne.jp/keyword/openstreetmap">openstreetmap</a>.org」を選択すると以下のような画面が表示される。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20230116/20230116200314.png" width="1200" height="657" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<p>適当にマップ上をクリックするとピンが落ち、任意の地点を2つ選択すると、ルートが表示される。
デフォルトだと車道のルートになっていると思われる。</p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/d/dshimizu/20230116/20230116200319.png" width="1200" height="656" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

<h2 id="まとめ">まとめ</h2>

<p>OSRM Frontend, Backend を使って、ローカルでルート検索マップを表示させてみた。
Frontend と組み合わせることで手軽にマップを表示させてルート検索を試せて面白い。<a class="keyword" href="https://d.hatena.ne.jp/keyword/Google">Google</a> Map とかはこういった仕組みを使って一瞬で経路を返しているんだろうなと思った。</p>

<p>まだあんまり触っていないけど Backend は色々 <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> が用意されているので、それらの <a class="keyword" href="https://d.hatena.ne.jp/keyword/API">API</a> を使うことで色々なことがやれそうだった。</p>

