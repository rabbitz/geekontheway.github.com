---
layout: post
title: "Rails-Dependencies"
date: 2011-12-24 06:10
comments: true
categories: dependcy
---


>Mac

Xcode,`rvm 安装1.9以上版本的ruby需要设置编译器为gcc`

>Ubuntu 10.04 LTS

{%codeblock%}
apt-get install wget vim build-essential openssl libssl-dev libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libxml2-dev libxslt-dev autoconf automake libtool imagemagick libpcre3-dev libmysqld-dev  



{%endcodeblock%}

>CentOS 5

{%codeblock%}

yum groupinstall "Development Tools"
yum install zlib-devel wget openssl-devel pcre pcre-devel make gcc gcc-c++ curl-devel mysql-devel

# gem install mysql --no-rdoc --no-ri -- --with-mysql-dir=/usr/bin --with-mysql-lib=/usr/lib/mysql --with-mysql-include=/usr/include/mysql

{%endcodeblock%}

更多阅读

[Ubuntu Packages Search](http://packages.ubunut.com/ "Ubuntu Packages Search")
