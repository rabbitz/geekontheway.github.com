---
layout: post
title: "从ssl说起"
date: 2011-12-08 19:20
comments: true
categories: Rails,Deploy,Chef,Ubuntu 
---
起因是昨天要部署公司的一个测试项目Emall到linode服务器，作为新手的我艰难的使用
`cap staging deploy:cold`诸如此类的命令,结果一开始就报错，原因很简单，deploy.rb文件和~/config/deploy/staging.rb中的代码有错误，capistrano的机制改天得画出一个流程图来，不然老觉得难理解.错误：

 {% img http://p4.42qu.us/721/820/149300.jpg %}
 
 查了下原因，官方这么描述：
 
> Before deploying an app that uses Bundler, Add your Gemfile and Gemfile.lock to source control, but ignore the .bundle folder, which is specific to each machine.
{% codeblock %}
$ echo ".bundle\n" >> .gitignore
$ git add Gemfile Gemfile.lock .gitignore
$ git commit -m "Add Bundler support"
{% endcodeblock %}
然后在deploy.rb中加入`require "bundler/capistrano"`
 {% img http://p4.42qu.us/721/819/149299.jpg %}
数据没部署上去,`current/releases/shared/`这三个文件夹的结构得研究一下.

报错提示：
{% img http://p4.42qu.us/721/821/149301.jpg %}

好像是rvm不能选择ruby版本的问题。[后来发了一个帖子关于Linode上RVM的奇怪问题，每次ssh登陆服务器后，发现rvm并未选择ruby版本，即使上次登陆的时候`rvm --default use ree`][http://ruby-china.org/topics/386],用了大家给的方法后,没找到合适答案后,折腾开始了:

- `rvm uninstall ree`,`rvm install ree`报错了
- `rm -rf /usr/local/rvm`,并取消了rvm有关的环境设置,下载了ree的源码，编译，安装passenger出错,大改是说
`openssl lib installed no longer supports SSLv2`,老大注释了源码中sslv2相关的部分，ok了.然而Emall依然部署不上去,所以后来想了想,还是rebuild linode服务器吧。还是犹豫了一下,因为好久以来都是在上面部署的服务，很多服务还是处于一半的状况,没办法从头来吧.
- ssh到服务器，做最基本的配置：
{% codeblock %}
Welcome to Ubuntu!
 * Documentation:  https://help.ubuntu.com/
Last login: Wed Dec  7 21:24:41 2011 from 182.18.65.14

root@li389-178:~# echo "log.networking.io" > /etc/hostname
root@li389-178:~# hostname -F /etc/hostname

root@log:~# sudo dpkg-reconfigure tzdata

Current default time zone: 'Asia/Chongqing'
Local time is now:      Thu Dec  8 10:28:55 CST 2011.
Universal Time is now:  Thu Dec  8 02:28:55 UTC 2011.

{% endcodeblock %}
- 因为又要装很多服务，而且也会有很多配置,考虑到效率，选择使用chef。
- ok。今天的折腾就开始了，chef的文档太详细了！丰富到我在众多的流程转向If Else中不知方向...好吧,最后终于知道怎么新建一个node节点了，可是第一步`knife bootstrap host -u root -P passwd`就ssh keys出错了,应该是kes不匹配,修改了.ssh/known_hosts后ok了.