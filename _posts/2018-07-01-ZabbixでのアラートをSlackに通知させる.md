---
layout: post
Categories:  ["tech"]
Description:  " Zabbixからの発生されるアラートをslackに通知させる設定についてのメモ書き。同様の記事はたくさんあるので不要な気もするし今更感もあるが...  やり方としては、Zabbix側にslackへの通知用のシェルスクリプトを設置し、アラー"
Tags: ["Slack", "tech", "Zabbix"]
date: "2018-07-01T00:15:00+09:00"
title: "ZabbixでのアラートをSlackに通知させる"
author: "dshimizu"
archive: ["2018"]
draft: "true"
---

<body>
<p>Zabbixからの発生されるアラートをslackに通知させる設定についてのメモ書き。同様の記事はたくさんあるので不要な気もするし今更感もあるが...</p>

<p>やり方としては、Zabbix側にslackへの通知用の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%A7%A5%EB%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">シェルスクリプト</a>を設置し、アラートが発生した際にはその<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%A7%A5%EB%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">シェルスクリプト</a>を呼び出されるようにアクションを設定する。<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%A7%A5%EB%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">シェルスクリプト</a>内部ではSlackのIncoming Webhooksで生成したURLに対して、Zabbixのアクション実行時に通知される内容を<a class="keyword" href="http://d.hatena.ne.jp/keyword/JSON">JSON</a>形式のデータとしてPOSTで送ることで、該当のチャンネルへ内容が投稿される、という形。</p>
</body>

<!-- more -->

<body>
<h3>実装概要</h3>


<h4>環境情報</h4>


<p>実行環境としては Zabbix Server はバージョン3.4 で、Slackへの通知は当然ながら Incoming Webhooks を使う。</p>

<ul>
    <li>Zabbix Server 3.4</li>
    <li><a href="https://api.slack.com/incoming-webhooks" target="brank" rel="noopener noreferrer">Incoming Webhooks | Slack</a></li>
</ul>


<h4>Incoming Webhooksの設定</h4>


<p>まず、Zabbixからの通知を投稿するためのチャンネルをあらかじめ作成しておく。
続いて<a target="brank" rel="noopener noreferrer">https://my.slack.com/services/new/incoming-webhook</a> からIncoming WebhooksのURLを生成する。
通知したいチャンネルを指定するだけで以下のようなURLが生成されるのでこれをメモしておく。</p>

<p><a href="https://hooks.slack.com/services/">https://hooks.slack.com/services/</a><strong><strong><strong><strong><strong>/</strong></strong></strong></strong></strong>/************************</p>

<p>その他細かい設定については下記のマニュアルを参照。</p>

<ul>
    <li><a href="https://api.slack.com/incoming-webhooks" target="brank" rel="noopener noreferrer">Incoming Webhooks | Slack</a></li>
</ul>


<p>いったんこれだけでOK。</p>

<h4>Zabbix側の設定</h4>


<h5>通知<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>の作成と設置</h5>


<p>以下のようなslackへの通知用<a class="keyword" href="http://d.hatena.ne.jp/keyword/bash">bash</a><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>(<code>zabbix_to_slack.sh</code>)を別途作成し、<code>/usr/lib/zabbix/alertscripts/</code>以下に設置する。
※<code>/usr/lib/zabbix/alertscripts/</code>は環境によって異なるかもしれないので、Zabbixサーバの設定ファイル(<code>zabbix_server.conf</code>)の<code>AlertScriptsPath</code>に設定されているPATH場所に合わせてカスタム<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>を設置する。</p>

<pre class="terminal"> #!/bin/bash

# * メディアタイプで設定した引数を受け取って、その内容をSlackに通知するスクリプト *

# Slack incoming web-hook URL
SLACK_WEBHOOKSURL='https://hooks.slack.com/services/*********/*********/************************'

# Slack UserName
SLACK_USERNAME='Zabbix(bot)'


# "Send to" for Zabbix User Media Setting 
NOTIFY_CHANNEL="$1"

# "Default subject" for Action Operations Setting
ALERT_SUBJECT="$2"

# "Default message" for Action Operations Setting
ALERT_MESSAGE="$3"

if [ "${ALERT_SUBJECT%%:*}" == 'OK' ]; then
        ICON=':smile:'
        COLOR="good"
elif [ "${ALERT_SUBJECT%%:*}" == 'PROBLEM' ]; then
        ICON=':skull:'
        COLOR="danger"
else
        ICON=':innocent:'
        ICON=':sushi:'
        COLOR="#439FE0"
fi

# Create JSON payload
PAYLOAD="payload={
    \"channel\": \"${NOTIFY_CHANNEL//\"/\\\"}\",
    \"username\": \"${SLACK_USERNAME//\"/\\\"}\",
    \"icon_emoji\": \"${ICON}\",
    \"attachments\": [
        {
            \"color\": \"${COLOR}\",
            \"text\": \"${ALERT_MESSAGE//\"/\\\"}\"
        }
    ]
}"

# Send it as a POST request to the Slack incoming webhooks URL
curl -m 5 --data-urlencode "${PAYLOAD}" $SLACK_WEBHOOKSURL
 </pre>


<h5>Zabbix管理画面へSlack通知用のメディアタイプ設定</h5>


<p>Zabbix上でアラート通知先として使用される設定。
公式ドキュメントの通りにメディアタイプを作成する。</p>

<ul>
    <li><a href="https://www.zabbix.com/documentation/3.4/manual/config/notifications/media" target="brank" rel="noopener noreferrer">5 Custom alertscripts [Zabbix Documentation 3.4]</a></li>
</ul>


<p>管理画面から以下のように遷移する。</p>

<p>画面上部のメニューから"管理(Administration)" → "メディアタイプ(Media types)" → "メディアタイプの作成(Create media type)"と遷移する</p>

<p>以下のような内容を入力する。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>名は、上記で作成した<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>名(本記事では <code>zabbix_to_slack.sh</code>)を入力する。</p>

<div class="table sectionedit4">
<table class="inline">
<thead>
<tr class="row0">
<th class="col0">Parameter</th>
<th class="col1">Description</th>
</tr>
</thead>
<tbody>
<tr class="row1">
<td class="col0 leftalign"><em>名前(Name)</em></td>
<td class="col1 leftalign">zabbix_to_slack</td>
</tr>
<tr class="row2">
<td class="col0 leftalign"><em>タイプ(Type)</em></td>
<td class="col1 leftalign"><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a></td>
</tr>
<tr class="row3">
<td class="col0 leftalign"><em><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>名(Script name)</em></td>
<td class="col1 leftalign">zabbix_to_slack.sh</td>
</tr>
<tr class="row4">
<td class="col0 leftalign"><em><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>パラメータ(Script parameters#1)</em></td>
<td class="col1 leftalign">{ALERT.SENDTO}</td>
</tr>
<tr class="row5">
<td class="col0 leftalign"><em><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>パラメータ(Script parameters#2)</em></td>
<td class="col1 leftalign">{ALERT.SUBJECT}</td>
</tr>
<tr class="row6">
<td class="col0 leftalign"><em><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>パラメータ(Script parameters#3)</em></td>
<td class="col1 leftalign">{ALERT.MESSAGE}</td>
</tr>
<tr class="row6">
<td class="col0 leftalign"><em>有効(Enabled)</em></td>
<td class="col1 leftalign">チェックを入れる</td>
</tr>
</tbody>
</table>
</div>


<h5>Zabbix上のユーザのメディア設定(メディアタイプを指定)</h5>


<p>Zabbix管理画面から新規または既存のユーザのメディア設定に、上記で作成したメディアタイプ(アラート通知先)を設定する。</p>

<p>画面上部のメニューから"管理(Administration)" → "ユーザー(Users)"と遷移する。
"ユーザの作成(Create User)"か既存ユーザを選択し、ユーザのプロパティの設定画面で"メディアタブ(Media tab)"を選択し、"追加(Add)"ボタンを押下する。</p>

<p>以下のような内容を入力する。
※通知先チャネル名はIncoming Webhooksで設定したものとは別なものを指定しても良い。その場合そちらに通知が行く。</p>

<div class="table sectionedit6">
<table class="inline">
<thead>
<tr class="row0">
<th class="col0">Parameter</th>
<th class="col1">Description</th>
</tr>
</thead>
<tbody>
<tr class="row1">
<td class="col0 leftalign"><em>タイプ(Type)</em></td>
<td class="col1 leftalign">zabbix_to_slack</td>
</tr>
<tr class="row2">
<td class="col0 leftalign"><em><a class="keyword" href="http://d.hatena.ne.jp/keyword/%C1%F7%BF%AE%C0%E8">送信先</a>(Send to)</em></td>
<td class="col1 leftalign">通知先チャネル名(シャープなし)</td>
</tr>
<tr class="row3">
<td class="col0 leftalign"><em>有効な時間帯(When active)</em></td>
<td class="col1 leftalign">1-7,00:00-24:00</td>
</tr>
<tr class="row4">
<td class="col0 leftalign"><em>指定した深刻度のときに使用(Use if severity)</em></td>
<td class="col1 leftalign">すべてにチェック</td>
</tr>
<tr class="row5">
<td class="col0 leftalign"><em>有効(Enabled)</em></td>
<td class="col1 leftalign">チェックを入れる</td>
</tr>
</tbody>
</table>
</div>


<h5>アクションの設定</h5>


<p>最後にアクションの設定を行う。
アクションは発生したイベントの結果に応じて実行する処理(アラート通知の送信など)の設定。</p>

<p>"設定(Configuration)" → "アクション(Actions)"と遷移する。
新規にアクションを作成するにしても既存のアクションの設定を変更するにしても、いずれにしても"実行内容(Operations)"タブを選択し、その中の"実行内容(Operations)"で、上記メディアを設定したユーザを追加し、忘れずに保存する。</p>

<h5>確認</h5>


<p>これでアラートが通知されるので適当にアラートを発生させて確認する。
以下のような感じでSlackへアラートが通知される。</p>

<p><img class="aligncenter size-full wp-image-1137" src="http://blog.dshimizu.jp/wp-content/uploads/zabbix-to-slack_01.png" alt="" width="669" height="337"></p>

<p><img class="aligncenter size-full wp-image-1138" src="http://blog.dshimizu.jp/wp-content/uploads/zabbix-to-slack_02.png" alt="" width="669" height="338"></p>

<h3>おわりに</h3>


<p>Zabbixのアラート発生時のアクションで、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%A7%A5%EB%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">シェルスクリプト</a>を実行させてSlackへ通知させる方法について書いた。</p>

<h3>参考</h3>


<ul>
    <li><a href="https://www.zabbix.com/jp/rn/rn3.4.1" target="brank" rel="noopener noreferrer">Release Notes for Zabbix 3.4.1</a></li>
    <li><a href="https://api.slack.com/incoming-webhooks" target="brank" rel="noopener noreferrer">Incoming Webhooks | Slack</a></li>
    <li><a href="https://dev.classmethod.jp/cloud/aws/zabbix-send-email/" target="_brank" rel="noopener noreferrer">プロキシ経由でZabbixのメール通知をしてみた ｜ Developers.IO</a></li>
    <li><a href="https://qiita.com/wapa5pow/items/2327561493015a833c97" target="_brank" rel="noopener noreferrer">Zabbix 3.2で障害時にSlack通知させる - Qiita</a></li>
    <li><a href="https://qiita.com/takashyx/items/3c9becf345cd58ca69bf" target="_brank" rel="noopener noreferrer">ZabbixからSlackに通知を送る - Qiita</a></li>
</ul>

</body>
