---
layout: post
Categories:  ["tech"]
Description:  " kubeadmで作ったk8sクラスタのWorkerノードの1つが障害停止してNotReady状態となったので復旧時の手順をメモしておく。 "
Tags: ["Kubernetes", "tech"]
date: "2020-02-29T23:16:00+09:00"
title: "kubeadmで作ったK8sのWorkerノードがNotReadyになったのでReadyに復旧させた時のメモ"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p>kubeadmで作った<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>のWorkerノードの1つが障害停止してNotReady状態となったので復旧時の手順をメモしておく。</p>
</body>

<!-- more -->

<body>
<p>状態としては以下のようになっている。</p>

<pre class="terminal"> $ kubectl get nodes
NAME          STATUS     ROLES     AGE   VERSION
host-01       Ready      master    67d   v1.17.1
host-02       Ready      worker1   67d   v1.17.1
host-03       NotReady   worker2   67d   v1.17.1
 </pre>




<h3>復旧</h3>


<p>MasterノードでWorkerノードを削除して再登録(join)するとかのやり方が必要になるケースもあるみたいだが、今回の自分のケースでは、kubeletが停止していたので起動したら直った。</p>

<pre class="terminal"> $ sudo systemctl start kubelet
 </pre>


<p>しばらくしたらReadyになった。AGEも特に変わらず。</p>

<pre class="terminal"> $ kubectl get nodes
NAME          STATUS     ROLES     AGE   VERSION
host-01       Ready      master    67d   v1.17.1
host-02       Ready      worker1   67d   v1.17.1
host-03       Ready      worker2   67d   v1.17.1
 </pre>




<h3>まとめ</h3>


<p>kubeadmで作成したWorkerノードがNotReadyになったので、Readyに復旧させるための手順を書いた。
場合によってはWorkerノードの情報をMasterノードから削除して再度登録する、Workerノードのkubeletを再インストールする、などのやり方が必要になるケースもあるかもしれないので、状況(バージョンや環境？)に応じて確認する必要があるかもしれない。</p>

<h3>参考</h3>




<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://www.n-novice.com/entry/2018/03/18/030512">Kubernetesのノードステータス"NotReady"を"Ready"へ - 備忘録／にわかエンジニアが好きなように書く</a></li>
  <li><a target="_brank" rel="noopener noreferrer" href="https://yunkt.hatenablog.com/entry/2018/08/13/200123">kubeadmをセットアップしてみたが、いつも通りハマる - yu nkt’s blog</a></li>
</ul>

</body>
