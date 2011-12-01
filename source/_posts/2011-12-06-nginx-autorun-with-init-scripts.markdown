---
layout: post
title: "nginx自启动设置"
date: 2011-12-06 19:40
comments: true
categories: [Rails,Nginx,Ubuntu,Centos,Redhat]
---

大二使用ubuntu的时候，了解了一些开机自动运行服务的命令和配置，不过一直没机会也没环境去操作。所以这几天配置服务器时候就麻烦了一些。为什么会麻烦呢？因为不好的习惯，国人喜欢百度，喜欢不加思索复制粘贴，goole上又全是英文，不一定每个人都英语好。其实就是一个很小的问题，百度上是一大堆重复的讲的不清不楚的，没有平台观念，版本观念的[不过确实有例外，CSDN上很多文章还是不错的，程序员的个人博客里的文章也都有思考在里面]，其实说回来，linux自身的版本过多，发行版中的一些设置也一直在变，从技术上讲是好事，可是给普通用户带来了学习的压力。

 >假定你：
 
 - 熟悉linux的运行级别[runlevel]
 {% codeblock %}
 0	关闭（或终止）系统
 1	单用户模式：通常又称为 s 或 S
 6	重启系统
 2	没有网络的多用户模式
 3	有网络的多用户模式
 5	有网络和 X Window System的多用户模式
 {% endcodeblock %}

 
 分享一下ngnix配置自动启动脚本：


{% gist 1436747 %}



>Ubuntu natty

- 在/etc/init.d中建立脚本nginx，添加脚本进去，chmod+x
- /usr/sbin/update-rc.d -f nginx defaults

>or

- sysv-rc-conf 设定2345运行nginx

要启动或者停止,重启

`/etc/init.d/nginx start/stop/restart`


>CentOS5

- 在/etc/init.d中建立脚本nginx，添加脚本进去，chmod+x
- /usr/sbin/update-rc.d -f nginx defaults

>or

- /sbin/chkconfig nginx 2345 on

要启动或者停止,重启

`service nginx start/stop/restart`











