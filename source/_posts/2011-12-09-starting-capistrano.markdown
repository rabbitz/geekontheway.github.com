---
layout: post
title: "初学capistrano"
date: 2011-12-09 15:56
comments: true
categories: [Capistrano,Deploy,Rails,Gem]
---

上一篇博客[从capistrano说起](http://geekontheway.github.com/blog/2011/12/08/from-ssl-to-ssh/)谈到了capistrano,刚接触有很多地方不懂，就拿出这篇博客做一个记录,有什么新的发现和问题，都会放上来.

##capistrano-ex

注意到安装`capistrano`的时候还要安装`capistrano-ex`这个gem包,官方的介绍是 `capistrano-ex allows you to setup multiple deploy environments`.

使用方法：

- 定义stage:
{% codeblock lang:ruby config/deploy.rb %}

set :stages, %w(production development)
set :default_stage, "development"
require 'capistrano/ext/multistage'

{% endcodeblock %}

- 设置stage

在config目录下创建deploy目录，并在其中修改各stage的配置.

{% codeblock lang:ruby config/ %}

$ cat production.rb

set :rails_env, "production" 
role :web, "productionserver.com"
role :app, "productionserver.com"
role :db,  "productionserver.com", :primary => true

$ cat development.rb

set :rails_env, "development"
role :web, "developmentserver.com"
role :app, "developmentserver.com"
role :db,  "developmentserver.com", :primary => true

{% endcodeblock %}

- 运行特定stage的deploy

`cap stagename deploy`

