---
layout: post
Categories:  ["tech"]
Description:  " kubeadmで作ったKubernatesクラスターにMetalLBとNginx Ingress Controllerを導入したのでその時にやった手順をメモしておく。  MetalLBとNginx Ingress ControllerはK"
Tags: ["Kubernetes", "tech"]
date: "2020-03-11T14:18:00+09:00"
title: "kubeadmで作ったK8sクラスターにMetalLBとNginx Ingress Controllerを導入した手順メモ"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p>kubeadmで作ったKubernates<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーにMetalLBとNginx <a class="keyword" href="http://d.hatena.ne.jp/keyword/Ingress">Ingress</a> Controllerを導入したのでその時にやった手順をメモしておく。</p>

<p>MetalLBとNginx <a class="keyword" href="http://d.hatena.ne.jp/keyword/Ingress">Ingress</a> ControllerはKubernates内部でのロードバランシングに利用する。前者はL4、後者はL7レベルのロードバランシングを行う。</p>
</body>

<!-- more -->

<body>
<h3>環境</h3>




<ul>
  <li>プラットフォーム さくらの<a class="keyword" href="http://d.hatena.ne.jp/keyword/VPS">VPS</a> 3台 (<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a> master * 1, <a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a> worker * 2)</li>
  <li>OS: <a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> 18.04</li>
  <li>kubernatis: 1.17.1</li>
  <li>Docker: 19.03.5</li>
</ul>




<h3>事前準備</h3>


<p>上記の環境でkubeadmでの<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ーをセットアップできている状態で、MetalLBやNginx <a class="keyword" href="http://d.hatena.ne.jp/keyword/Ingress">Ingress</a> Controllerを導入する。</p>

<h3>MetalLBのセットアップ</h3>


<p>MetalLBの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A5%CB%A5%D5%A5%A7%A5%B9%A5%C8">マニフェスト</a>ファイルを取得する。</p>

<pre class="terminal"> $ wget https://raw.githubusercontent.com/google/metallb/master/manifests/metallb.yaml
 </pre>


<p>このままでも使えるが、Podを配置するノードだけ指定しておく。
ここでは1台目のWorkerノードに配置することとする。</p>

<p>まず、Workerノードにラベルをつけていない場合はラベルをつける。
そのために以下のコマンドをMasterノードで実行する。host-01,host-02の部分は環境に合わせて置き換える。</p>

<pre class="terminal"> $ kubectl label node host-01 node-role.kubernetes.io/worker1=
 </pre>




<pre class="terminal"> $ kubectl label node host-02 node-role.kubernetes.io/worker2=
 </pre>


<p>続いて、上記のラベルを設定したノードにPodがデプロイされるように<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A5%CB%A5%D5%A5%A7%A5%B9%A5%C8">マニフェスト</a>ファイルを編集する。
DaemonSetとDeploymentのspecフィールドの中のspecオブジェクト(Podの状態を定義するオブジェクト)の最後に以下を記載する。+の部分が追記部分となる。</p>

<pre class="terminal"> :
  ---
  apiVersion: apps/v1
  kind: DaemonSet
  metadata:
    labels:
      app: metallb
      component: speaker
    name: speaker
    namespace: metallb-system
  spec:
    :
      spec:
        :
+       tolerations:
+       - effect: NoSchedule
+         key: node-role.kubernetes.io/worker1
  ---
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    labels:
      app: metallb
      component: controller
    name: controller
    namespace: metallb-system
  spec:
    :
      spec:
        :
+       tolerations:
+       - effect: NoSchedule
+         key: node-role.kubernetes.io/worker1
 </pre>


<p>設定できたらapplyする。</p>

<pre class="terminal"> $ kubectl apply -f metallb.yaml
 </pre>


<p>masterノード以外にPodがデプロイされていることを確認する。</p>

<pre class="terminal"> $ kubectl get pod -n metallb-system -o wide
NAME                          READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
controller-5df7dc969b-rrgwb   1/1     Running   0          20d   10.244.2.20   Node名   　           
speaker-9mppl                 1/1     Running   2          20d   10.1.0.130    Node名   　           
speaker-fk8wq                 1/1     Running   0          20d   10.1.0.131    Node名   　           
 </pre>


<p>続いてMetalLBのモードの設定をする。
BGPモードとL2モードがあるが、BGPはBGPルータなどの専用機器が必要なのと、そもそもL2モードで十分なのでその設定をする。</p>

<pre class="terminal"> $ wget https://raw.githubusercontent.com/metallb/metallb/main/manifests/example-layer2-config.yaml -O metallb-layer2-config.yaml
 </pre>


<p>基本的にこのままで使えるが、addressesの部分だけ、利用したいExternal IPのレンジを設定する。</p>

<pre class="terminal"> apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - ***.***.***.***/32
 </pre>


<p>これもapplyする。</p>

<pre class="terminal"> $ kubectl apply -f metallb-layer2-config.yaml
 </pre>




<h3>Nginx <a class="keyword" href="http://d.hatena.ne.jp/keyword/Ingress">Ingress</a> Controllerのセットアップ</h3>


<p>L7ロードバランサも設定する。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A5%CB%A5%D5%A5%A7%A5%B9%A5%C8">マニフェスト</a>ファイルを取得する。</p>

<p>まず<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ingress">Ingress</a> Contollerのデプロイメントを作成する。</p>

<pre class="terminal"> $ wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/mandatory.yaml
 </pre>


<p>これもMetalLBと同様に1台目のWorkerノードに配置することとする。</p>

<pre class="terminal">   ---

  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: nginx-ingress-controller
    namespace: ingress-nginx
    labels:
      app.kubernetes.io/name: ingress-nginx
      app.kubernetes.io/part-of: ingress-nginx
  spec:
    :
      spec:
        :
+       tolerations:
+       - key: "node-role.kubernetes.io/worker1"
+         effect: "NoSchedule"
 </pre>


<p>デプロイする。</p>

<pre class="terminal"> $ kubectl apply -f mandatory.yaml
 </pre>


<p>続いて、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ingress">Ingress</a> Contollerのサービスを作成する。</p>

<pre class="terminal"> $ wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/cloud-generic.yaml
 </pre>


<p>これはそのままapplyすれば良い。</p>

<pre class="terminal"> $ kubectl apply -f cloud-generic.yaml
 </pre>




<h3>確認</h3>


<p>これでExternal-IPにアクセスしてNginxの404ページが表示されればとりあえず完了。</p>

<h3>まとめ</h3>


<p>kubeadmで作成した<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー上にMetalLBとNginx <a class="keyword" href="http://d.hatena.ne.jp/keyword/Ingress">Ingress</a> Controllerをデプロイしてみた時の手順を書いた。</p>

<h3>参考</h3>




<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://kubernetes.github.io/ingress-nginx/deploy/baremetal/">Bare-metal considerations - NGINX Ingress Controller</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://kubernetes.io/ja/docs/concepts/overview/working-with-objects/kubernetes-objects/">Kubernetesオブジェクトを理解する - Kubernetes</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#podspec-v1-core">Kubernetes API Reference Docs</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/metallb/metallb">GitHub - metallb/metallb: A network load-balancer implementation for Kubernetes using standard routing protocols</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/kubernetes/ingress-nginx">GitHub - kubernetes/ingress-nginx: NGINX Ingress Controller for Kubernetes</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://docs.cloud.oracle.com/ja-jp/iaas/Content/ContEng/Tasks/contengsettingupingresscontroller.htm">例:クラスタ上のイングレス・コントローラの設定</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://qiita.com/nagase/items/15726e37057e7cc3b8cd">Kubernetes on CentOS7 - Qiita</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://qiita.com/murata-tomohide/items/801c25492e55f672c23c">オンプレでもIngressする - Qiita</a></li>
</ul>

</body>
