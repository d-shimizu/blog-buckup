---
layout: post
Categories:  ["tech"]
Description:  " はじめに  Python 実行環境を作る機会があったので Virtaulenv を使ってみました。 "
Tags: ["Python", "tech", "Ubuntu"]
date: "2016-09-15T14:25:00+09:00"
title: "virturalenv で Python 仮想環境を作成"
author: "dshimizu"
archive: ["2016"]
draft: "true"
---

<body>
<h3>はじめに</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 実行環境を作る機会があったので Virtaulenv を使ってみました。</p>
</body>

<!-- more -->

<body>
<h3>Virtaulenv</h3>


<p>Virtaulenv はシステム環境とは独立して <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 仮想環境を作成するツールです。
仮想環境とは、特定のバージョンの <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> と幾つかの追加パッケージを含んだ <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 実行環境を構成する<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リです。
例えば <a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> であれば、 <a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> 標準の <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 2.7 (<code>/usr/bin/python -&gt; python2.7</code>)と <code>pip</code> を使ってインストールされたライブラリ類は <code>/usr/local/lib/python2.7</code> 以下にインストールされますが、　Virtualenv を使った場合、 <code>/usr/local/lib/python2.7</code> ではなく Virtualenv で作成した<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ配下に <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 2.7 と <code>pip</code> のライブラリがインストールされます。</p>

<h3>インストールと構築</h3>


<p>Virtaulenv をインストールしてみます。
インストールの方法は、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BD%A1%BC%A5%B9%A5%B3%A1%BC%A5%C9">ソースコード</a>からインストールするか、 <code>pip</code> を使うか、OSパッケージを使うか、のいずれかになると思います。
どの方法が一般的かわかりませんが、今回は Virtualenv を OS パッケージでインストールし、Virtualenv上に <code>pip</code> 含めライブラリをインストールすることにします。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> 16.04 LTSには <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 2.7 と <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 3.5 のOSパッケージが存在しますので、この2つの<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a>を利用して、2.7 と 3.5 の<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a>仮想環境を試すことにします。</p>

<h4>環境</h4>


<table>
<tbody>
<tr>
<th>OS</th>
<td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> 16.04 LTS(4.4.0-21-generic)</td>
</tr>
<tr>
<th><a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a></th>
<td>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 2.7 (<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> 標準パッケージ), <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 3.5 (<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ubuntu">Ubuntu</a> 標準パッケージ)</td>
</tr>
</tbody>
</table>


<h4>インストール</h4>


<p>単純に apt コマンドでインストールすれば終わりです。</p>

<pre class="terminal"> $ sudo apt install virtualenv
 </pre>


<h4>Virtualenv 環境構築</h4>


<p>Virtualenv環境用<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成します。</p>

<pre class="terminal"> $ sudo mkdir ~/WORK/
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 2.7 用の Virtualenv　環境を作成します。
<code>-p</code> で <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> の <code>Path</code> を指定します。</p>

<pre class="terminal"> $ virtualenv -p /usr/bin/python2.7 ~/WORK/venv27
Running virtualenv with interpreter /usr/bin/python2.7
New python executable in /home/${USER}/WORK/venv27/bin/python2.7
Also creating executable in /home/${USER}/WORK/venv27/bin/python
Installing setuptools, pkg_resources, pip, wheel...done.
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 3.5 用の Virtualenv　環境を作成します。</p>

<pre class="terminal"> $ virtualenv -p /usr/bin/python3.5 ~/WORK/venv35
Running virtualenv with interpreter /usr/bin/python3.5
Using base prefix '/usr'
New python executable in /home/${USER}/WORK/venv35/bin/python3.5
Also creating executable in /home/${USER}/WORK/venv35/bin/python
Installing setuptools, pkg_resources, pip, wheel...done.
 </pre>


<h4>Virtualenvの利用</h4>


<p>virtualenvの環境に移行してみます。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 2.7 の環境に移行するには、先ほど <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 2.7 を指定して作成した Virtualenv <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ以下の <code>/bin/activate</code> コマンドを使います。</p>

<pre class="terminal"> $ . ~/WORK/venv27/bin/activate
(venv27) $
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> の <code>PATH</code> やバージョンを確認してみます。</p>

<pre class="terminal"> (venv27) $ which python
/home/${USER}/WORK/venv27/bin/python

(venv27) $ python -V
Python 2.7.12
 </pre>


<p>Virtualenv環境から抜けるには、先ほど <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 2.7 を指定して作成した Virtualenv <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ以下の <code>/bin/deactivate</code> コマンドを使います。virtaulenv環境に入っている場合は <code>deactivate</code> コマンドへの <code>PATH</code> は通っています。</p>

<pre class="terminal"> (venv27) $ deactivate
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 3.5 の環境に移行するには、先ほど <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 3.5 を指定して作成した Virtualenv <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ以下の /bin/activate コマンドを使います。</p>

<pre class="terminal"> $ . ~/WORK/venv35/bin/activate
(venv35) $
 </pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> の PATHやバージョンを確認してみます。</p>

<pre class="terminal"> (venv35) $ which python
/home/${USER}/WORK/venv27/bin/python

(venv35) $ python -V
Python 3.5.2
 </pre>


<p>virtualenv環境から抜けるには、先ほど <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> 3.5 を指定して作成した virtualenv <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ以下の <code>/bin/deactivate</code> コマンドを使います。Virtaulenv環境に入っている場合は <code>deactivate</code> コマンドへの <code>PATH</code> は通っています。</p>

<pre class="terminal"> (venv35) $ deactivate
 </pre>


<p>何かライブラリをインストールしてみます。
Virtualenv 環境作成時に <code>pip</code> も利用可能になっています。
<code>pip</code> を使用してライブラリ類をインストールできます。Virtualenv環境内でインストールしたものは 他のVirtaulenv環境やシステムのライブラリに影響を与えません。</p>

<p>以下は flask をインストールしてみたものです。</p>

<pre class="terminal"> (venv27) $ pip install flask
Collecting flask
  Downloading Flask-0.11.1-py2.py3-none-any.whl (80kB)
    100% |████████████████████████████████| 81kB 3.2MB/s
Collecting itsdangerous&gt;=0.21 (from flask)
  Downloading itsdangerous-0.24.tar.gz (46kB)
    100% |████████████████████████████████| 51kB 7.0MB/s
Collecting click&gt;=2.0 (from flask)
  Downloading click-6.6.tar.gz (283kB)
    100% |████████████████████████████████| 286kB 2.8MB/s
Collecting Werkzeug&gt;=0.7 (from flask)
  Downloading Werkzeug-0.11.11-py2.py3-none-any.whl (306kB)
    100% |████████████████████████████████| 307kB 3.0MB/s
Collecting Jinja2&gt;=2.4 (from flask)
  Downloading Jinja2-2.8-py2.py3-none-any.whl (263kB)
    100% |████████████████████████████████| 266kB 4.4MB/s
Collecting MarkupSafe (from Jinja2&gt;=2.4-&gt;flask)
  Downloading MarkupSafe-0.23.tar.gz
Building wheels for collected packages: itsdangerous, click, MarkupSafe
  Running setup.py bdist_wheel for itsdangerous ... done
  Stored in directory: /home/${USER}/.cache/pip/wheels/fc/a8/66/24d655233c757e178d45dea2de22a04c6d92766abfb741129a
  Running setup.py bdist_wheel for click ... done
  Stored in directory: /home/${USER}/.cache/pip/wheels/b0/6d/8c/cf5ca1146e48bc7914748bfb1dbf3a40a440b8b4f4f0d952dd
  Running setup.py bdist_wheel for MarkupSafe ... done
  Stored in directory: /home/${USER}/.cache/pip/wheels/a3/fa/dc/0198eed9ad95489b8a4f45d14dd5d2aee3f8984e46862c5748
Successfully built itsdangerous click MarkupSafe
Installing collected packages: itsdangerous, click, Werkzeug, MarkupSafe, Jinja2, flask
Successfully installed Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.11 click-6.6 flask-0.11.1 itsdangerous-0.24
 </pre>


<p>PATHを確認してみます。</p>

<pre class="terminal"> (venv27) $ which flask
/home/${USER}/work/venv27/bin/flask
 </pre>


<p>ライブラリのインストール場所を確認します。</p>

<pre class="terminal"> (venv27) $ ls work/venv27/lib/python2.7/site-packages/flask
__init__.py   _compat.py   blueprints.py   config.py   debughelpers.py   exthook.pyc  helpers.pyc  logging.pyc   signals.pyc     testing.pyc  wrappers.pyc
__init__.pyc  _compat.pyc  blueprints.pyc  config.pyc  debughelpers.pyc  globals.py   json.py      sessions.py   templating.py   views.py
__main__.py   app.py       cli.py          ctx.py      ext               globals.pyc  json.pyc     sessions.pyc  templating.pyc  views.pyc
__main__.pyc  app.pyc      cli.pyc         ctx.pyc     exthook.py        helpers.py   logging.py   signals.py    testing.py      wrappers.py
 </pre>


<p>virtualenv環境から抜けて、 flask を利用できるか確認してみます。</p>

<pre class="terminal"> (venv27) $ deactivate

$ which flask
(出力なし)
 </pre>


<h3>終わりに</h3>


<p>異なるバージョンの <a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a> やライブラリの動作を試したい場合に、 Virtualenv を使用することでシステム内のライブラリを汚さずに管理して使い分けることができます。</p>

<h3>参考</h3>


<ul>
    <li><a href="http://docs.python.jp/3/tutorial/venv.html" target="_blank" rel="noopener noreferrer">12. 仮想環境とパッケージ — Python 3.5.2 ドキュメント</a></li>
    <li><a href="https://virtualenv.pypa.io/en/stable/" target="_blank" rel="noopener noreferrer">Virtualenv — virtualenv 15.0.2 documentation</a></li>
    <li><a href="http://dev.classmethod.jp/server-side/language/python-virtualenv-tutorial/" target="_blank" rel="noopener noreferrer">virtualenvを使っていろいろなライブラリを手軽にためそう ｜ Developers.IO</a></li>
</ul>

</body>
