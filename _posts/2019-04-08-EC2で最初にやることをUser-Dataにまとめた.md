---
layout: post
Categories:  ["tech"]
Description:  " AWSでEC2(Ligthsail含む)を使うときに最初にやることを手で実行するのは疲れるのでUserDataにまとめた。 "
Tags: ["AWS", "tech"]
date: "2019-04-08T10:35:00+09:00"
title: "EC2で最初にやることをUser Dataにまとめた"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>でEC2(Ligthsail含む)を使うときに最初にやることを手で実行するのは疲れるのでUserDataにまとめた。</p>
</body>

<!-- more -->

<body>
<p>以下を実行するようにしている。他に必要なことがあれば追加する。</p>

<ul>
    <li>OSユーザ/グループの作成</li>
    <li>作成したユーザのsudo設定</li>
    <li>ec2-userでのログイン無効化</li>
    <li>timezone変更(<a class="keyword" href="http://d.hatena.ne.jp/keyword/UTC">UTC</a>-&gt;Asia/Tokyo)</li>
    <li>Localtimeの変更</li>
</ul>


<p>上記を実行するUserDataは下記。</p>

<pre class="terminal"> #!/bin/bash

#
# * Initial Setup EC2 common
#

#
# * Variables
#
SCRIPT=$(basename $0)

USER=sysadmin  # 任意のユーザ名を指定
GROUP=sysadmin  # 任意のグループ名を指定
USER_ID=10000
GROUP_ID=10000
SHELL=/bin/bash
HOME_DIR=/home/${USER}
SSH_KEY_DIR=/home/${USER}/.ssh
SSH_PUB_KEY=${SSH_KEY_DIR}/authorized_keys


########################################
# * Create ${USER} User And Group
########################################

echo "##### Start: Create ${USER} User and ${GROUP} Group #####"

groupadd -g ${GROUP_ID} ${GROUP}
useradd -u ${USER_ID} -g ${GROUP} -G wheel -s ${SHELL} -d ${HOME_DIR} -m ${USER}
id ${USER}

echo "##### Finished: Create ${USER} User and ${GROUP} Group #####"

sleep 1


#
# * Modify sudo
#
echo "=== Start: Modify sudo ==="
echo "${USER} ALL = NOPASSWD: ALL" &gt; /etc/sudoers.d/sysadmin
echo "=== Finished: Modify sudo ==="

sleep 1


########################################
# * Create SSH Key for ${USER} user
########################################

echo "##### Start: Create SSH Key for sysadmin user #####"

if [ ! -e ${SSH_KEY_DIR} ] ; then
          mkdir ${SSH_KEY_DIR}
fi

### 任意の公開鍵を設定
echo "******" &gt; ${SSH_PUB_KEY}

chmod 700 ${SSH_KEY_DIR}
chmod 600 ${SSH_PUB_KEY}
chown -R ${USER}:${GROUP} ${SSH_KEY_DIR}

ls -lth ${SSH_KEY_DIR}

echo "##### Finished: Create ssh Key  for ${USER} user #####"

sleep 1


########################################
# * Disable ec2-user Login
########################################

echo "##### Start: Disable ec2-user Login #####"

cp -pr /etc/ssh/sshd_config{,.`date +%Y%m%d_%H%M%S`}
echo -e "\n\n# Disable ec2-user Login\nDenyUsers ec2-user" &gt;&gt; /etc/ssh/sshd_config &amp;&amp; systemctl reload sshd

echo "##### Finished: Disable ec2-user Login #####"

sleep 1


########################################
# * Modify Timezone File
########################################

echo "##### Start: Modify Timezone #####"

cp -pr /etc/sysconfig/clock{,.`date +%Y%m%d_%H%M%S`}
sed -i.org -e 's/ZONE=\"UTC\"/ZONE=\"Asia\/Tokyo\"/g' /etc/sysconfig/clock

echo "##### Finished: Modify Timezone #####"

sleep 1


########################################
# * Modify Localtime File
########################################

echo "##### Start: Modify Localtime #####"

cp -pr /etc/localtime{,.`date +%Y%m%d_%H%M%S`}
ln -sf /usr/share/zoneinfo/Japan /etc/localtime

echo "##### Finished: Modify Localtime #####"

sleep 1


echo "========== ${SCRIPT} Scritp Finished =========="
 </pre>

</body>
