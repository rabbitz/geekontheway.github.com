---
layout: post
title: "配置nginx的一些思考"
date: 2011-12-06 19:02
comments: true
categories: [Rails,Nginx,Ubuntu,Centos,Redhat]
---

大二使用ubuntu的时候，了解了一些开机自动运行服务的命令和配置，不过一直没机会也没环境去操作。所以这几天配置服务器时候就麻烦了一些。为什么会麻烦呢？因为不好的习惯，国人喜欢百度，喜欢不加思索复制粘贴，goole上又全是英文，不一定每个人都英语好。其实就是一个很小的问题，百度上是一大堆重复的讲的不清不楚的，没有平台观念，版本观念的[不过确实有例外，CSDN上很多文章还是不错的，程序员的个人博客里的文章也都有思考在里面]，其实说回来，linux自身的版本过多，发行版中的一些设置也一直在变，从技术上讲是好事，可是给普通用户带来了学习的压力。

 言归正传，Ubuntu现在使用sysv-rc-conf,Redhat系[redhat,centos,fedora]使用chkconfig。

 分享一下ngnix配置自动启动脚本：


{% gist: 1436747 %}



>Ubuntu

-1.在/etc/init.d中建立脚本nginx，添加脚本进去，chmod+x
-2.ubuntu：/usr/sbin/update-rc.d -f nginx defaults
-3.sysv-rc-conf 设定2345运行nginx

`/etc/init.d/nginx start/stop/restart`


>RedHat

-1.在/etc/init.d中建立脚本nginx，添加脚本进去，chmod+x
-2./sbin/chkconfig nginx 2345 on


`service nginx start/stop/restart`











