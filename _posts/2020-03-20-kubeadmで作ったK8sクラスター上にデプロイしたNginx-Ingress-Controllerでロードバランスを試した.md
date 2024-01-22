---
layout: post
Categories:  ["tech"]
Description:  " kubeadmで作ったK8sクラスター上にデプロイしたNginx Ingress Controllerでロードバランスを試した。 "
Tags: ["Kubernetes", "tech"]
date: "2020-03-20T00:27:00+09:00"
title: "kubeadmで作ったK8sクラスター上にデプロイしたNginx Ingress Controllerでロードバランスを試した"
author: "dshimizu"
archive: ["2020"]
draft: "true"
---

<body>
<p>kubeadmで作った<a class="keyword" href="http://d.hatena.ne.jp/keyword/K8s">K8s</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー上にデプロイしたNginx <a class="keyword" href="http://d.hatena.ne.jp/keyword/Ingress">Ingress</a> Controllerでロードバランスを試した。</p>
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


<p><a target="_brank" rel="nooperer noopener noreferrer" href="https://blog.d-shimizu.io/article/1812">こちら</a>と<a target="_brank" rel="noopener noreferrer" href="https://blog.d-shimizu.io/article/1847">こちら</a>の手順で構築した環境で動作を確認する。</p>

<p>以下のようなL7ロードバランサを利用する<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A5%CB%A5%D5%A5%A7%A5%B9%A5%C8">マニフェスト</a>を作成する。
下記3つを1つのファイルとしても良いし別々のファイルにしてapplyするようにしても良い。</p>

<pre class="terminal"> # namespace
apiVersion: v1
kind: Namespace
metadata:
  name: test
---
# ingress
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: lb
  namespace: test
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
    - host: hogehoge.vs.sakura.ne.jp    # httpへアクセスする際に利用するホスト名
      http:
        paths:
          - path: /apache               # /apache への振り分け設定
            backend:
              serviceName: apache-svc
              servicePort: 80
          - path: /nginx                # /nginx への振り分け設定
            backend:
              serviceName: nginx-svc
              servicePort: 80
          - path: /
            backend:
              serviceName: blackhole
              servicePort: 80
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Apache">Apache</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A5%CB%A5%D5%A5%A7%A5%B9%A5%C8">マニフェスト</a>ファイルを下記としておく。</p>

<pre class="terminal"> # apache
apiVersion: v1
kind: Service
metadata:
  name: apache-svc
  namespace: testest
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: httpd
  type: NodePort
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd
  namespace: test
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpd
  template:
    metadata:
      labels:
        app: httpd
    spec:
      containers:
      - image: httpd:alpine
        name: httpd
        ports:
        - containerPort: 80
 </pre>


<p>Nginxの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A5%CB%A5%D5%A5%A7%A5%B9%A5%C8">マニフェスト</a>ファイルを下記としておく。</p>

<pre class="terminal"> # nginx
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
  namespace: test
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
  type: NodePort
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  namespace: test
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:alpine
        name: nginx
        ports:
        - containerPort: 80
 </pre>


<p>Apply後は以下のようになる。</p>

<pre class="terminal"> $ kubectl get pods --all-namespaces
NAMESPACE        NAME                                                    READY   STATUS    RESTARTS   AGE
ingress-nginx    nginx-ingress-controller-7ff5fb7dd-bm65r                1/1     Running   0          43d
kube-system      coredns-6955765f44-5z4ph                                1/1     Running   1          91d
kube-system      coredns-6955765f44-fcfxs                                1/1     Running   1          91d
kube-system      etcd-ik1-319-19580.vs.sakura.ne.jp                      1/1     Running   1          91d
kube-system      kube-apiserver-hogehoge.vs.sakura.ne.jp                 1/1     Running   1          91d
kube-system      kube-controller-manager-hogehoge.vs.sakura.ne.jp        1/1     Running   1          91d
kube-system      kube-flannel-ds-amd64-9s4f9                             1/1     Running   1          90d
kube-system      kube-flannel-ds-amd64-ddzql                             1/1     Running   3          90d
kube-system      kube-flannel-ds-amd64-jmhwb                             1/1     Running   8          90d
kube-system      kube-proxy-fpppn                                        1/1     Running   1          91d
kube-system      kube-proxy-rszww                                        1/1     Running   1          91d
kube-system      kube-proxy-sl4vt                                        1/1     Running   3          91d
kube-system      kube-scheduler-hogehoge.vs.akura.ne.jp                  1/1     Running   1          91d
metallb-system   controller-5df7dc969b-rrgwb                             1/1     Running   0          43d
metallb-system   speaker-9mppl                                           1/1     Running   2          44d
metallb-system   speaker-fk8wq                                           1/1     Running   0          44d
test             httpd-75cb4864cc-xhqj2                                  1/1     Running   0          3d12h
test             nginx-5c559d5697-58qz9                                  1/1     Running   0          3d11h
 </pre>


<p>こうなったら hogehoge.vs.sakura.ne.jp/nginx, hogehoge.vs.sakura.ne.jp/<a class="keyword" href="http://d.hatena.ne.jp/keyword/apache">apache</a> へリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>トして、それぞれのWelocomeページが表示されればOK。</p>

<h3>まとめ</h3>


<p>kubeadmで作成した<a class="keyword" href="http://d.hatena.ne.jp/keyword/k8s">k8s</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%AF%A5%E9%A5%B9%A5%BF">クラスタ</a>ー上に構築した<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ingress">Ingress</a> Controllerを利用して、L7のリク<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A8%A5%B9">エス</a>ト負荷分散の動作を確認するための手順を書いた。</p>

<h3>参考</h3>


<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://qiita.com/murata-tomohide/items/801c25492e55f672c23c">オンプレでもIngressする - Qiita</a></li>
</ul>

</body>
