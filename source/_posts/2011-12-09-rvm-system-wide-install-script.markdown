---
layout: post
title: "分享一个全局RVM的安装脚本 Ubuntu 10.04 "
date: 2011-12-09 15:27
comments: true
categories: [Ubuntu,Rvm,Ruby,Rails,script]
---
{% codeblock lang:sh Includes: StackScript Bash Library %}
#!/bin/bash
# <udf name="admin_login" label="Admin Login" />
# <udf name="admin_password" label="Admin Password" />
# <udf name="group_name" label="RVM Group name" default="rvm" />
source <ssinclude StackScriptID="1">
system_update
hostname `get_rdns_primary_ip`
aptitude -y install sudo
groupadd wheel
cp /etc/sudoers /etc/sudoers.tmp
chmod 0640 /etc/sudoers.tmp
echo "%wheel ALL = (ALL) ALL" >> /etc/sudoers.tmp
chmod 0440 /etc/sudoers.tmp
mv /etc/sudoers.tmp /etc/sudoers
useradd -m -s /bin/bash -G wheel "$ADMIN_LOGIN"
echo "${ADMIN_LOGIN}:${ADMIN_PASSWORD}" | chpasswd
export rvm_group_name="$GROUP_NAME"
aptitude -y install curl bison build-essential zlib1g-dev libssl-dev libreadline5-dev libxml2-dev git-core
bash < <( curl -L http://bit.ly/rvm-install-system-wide )
usermod -a -G "$GROUP_NAME" "$ADMIN_LOGIN"
{% endcodeblock %}