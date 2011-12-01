---
layout: post
title: "从Capsitrano说起"
date: 2011-12-08 19:20
comments: true
categories: Rails,Deploy,Chef,Ubuntu 
---

# Capistrano with bundler

起因是昨天要部署公司的一个测试项目bar到linode服务器,部署代码是之前写的,正好拿来学习了:

`$ gem list capistrano`

> *** LOCAL GEMS ***
>
> capistrano (2.9.0)
>
> capistrano-ext (1.2.1)

{% codeblock title: deploy.rb %}

# -*- encoding : utf-8 -*-
set :stages, %w(production staging)
set :default_stage, "production"
require 'capistrano/ext/multistage'

set :application, "bar"

set :use_sudo, false
set :scm, :git
set :deploy_via, :remote_cache

default_run_options[:pty] = true
ssh_options[:forward_agent] = true


set :git_shallow_clone, 1
set :git_enable_submodules, 1

{% endcodeblock %}

{% codeblock title: deploy/staging.rb %}

# -*- encoding : utf-8 -*-
set :user, "root"
set :repository, "git@github.com:foo/bar.git"
set :branch, "master"
set :deploy_to, "/home/staging/www/bar"

role :web, "foo.bar.com" # Your HTTP server, Apache/etc
role :app, "foo.bar.com" # This may be the same as your `Web` server
role :db, "foo.bar.com", :primary => true # This is where Rails migrations will run

# tasks
namespace :deploy do
  task :start, :roles => :app do
    run "touch #{current_path}/tmp/restart.txt"
  end

  task :stop, :roles => :app do
    # Do nothing.
  end

  desc "Restart Application"
  task :restart, :roles => :app do
    run "touch #{current_path}/tmp/restart.txt"
  end

  desc "Symlink shared resources on each release"
  task :symlink_shared, :roles => :app do
    run "ln -nfs #{shared_path}/config/database.yml #{release_path}/config/database.yml"
    run "ln -nfs #{shared_path}/config/mailers.yml #{release_path}/config/mailers.yml"

    #run "mkdir #{release_path}/public/stylesheets/compiled"

    run "ln -nfs #{shared_path}/ckeditor_assets #{release_path}/public/ckeditor_assets"
    run "ln -nfs #{shared_path}/system #{release_path}/public/system"
    run "chmod 0777 #{release_path}/public/stylesheets/compiled"
  end
end

after 'deploy:update_code', 'deploy:symlink_shared'

namespace :db do
  desc "migrate db"
  task :migrate, :roles => :app do
    run "cd #{release_path} && RAILS_ENV=production rake db:migrate"
  end
end

{% endcodeblock %}


运行部署后报错：

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

数据没部署上去,应该是链接没做好,部署脚本是之前同事写的,关于Symlink这部分,我又谷歌了一下:

`Update @ 2011-12-9 11:17`

- The `current `directory is where the most current version of your Rails application is located. Each time you deploy your application, the contents of this directory are overwritten with your new changes.

- The `releases` directory stores previous releases of your application, which allow Capistrano to "roll back" changes in the event that something goes wrong. Each time you deploy your application, the contents of the current directory are moved to the releases before the new version is copied to the current directory. You will want to limit the number of previous releases that are stored to preserve disk space. This can be done by running cap deploy:cleanup.

- The `shared` directory contains assets that are independent of the version of your application. In other words, the shared directory is left alone during a deployment, which makes it a perfect place to hold uploaded files and documents that you want to preserve between migrations.This is a great time to mention that that config/database.yml file is a wonderful candidate for storing in the shared directory and symlinking to it. By doing this, you can avoid having to publish your development and testing passwords, and multiple developers can easily collaborate on the same project.

Rather than having to manually create our symlinks,  Luckily, we can create a Capistrano deployment recipe that automates this task.Since our application is already up and running, we will only be adding some things to the end of the recipe that is currently in place for your web host. In this example we create a namespace called "customs" for our new tasks:

{% codeblock %}

namespace(:customs) do
  task :config, :roles => :app do
    run <<-CMD
      ln -nfs #{shared_path}/system/database.yml #{release_path}/config/database.yml
    CMD
  end
  task :symlink, :roles => :app do
    run <<-CMD
      ln -nfs #{shared_path}/system/uploads #{release_path}/public/uploads
    CMD
  end
end

{% endcodeblock %}

In order to get Capistrano to perform these tasks each time we deploy our application, we need to set some hooks by placing the following code at the bottom of our deploy.rb file:

{% codeblock %}

after "deploy:update_code", "customs:config"
after "deploy:symlink","customs:symlink"
after "deploy", "deploy:cleanup"

{% endcodeblock %}

The first line tells Capistrano that after it has performed the update_code methods, which updates the Rails application, that it should symlink to our shared database.yml file so that Rails has the production database configuration settings.

The next line tells Capistrano that once it has finished symlinking the application (a normal deploy involves symlinking to the current directory), it should then create the symlink for our uploads directory.

Finally, I have added a third line that tells Capistrano to finish it all up by running the cap deploy:cleanup task, which removes all but the last 5 releases from the releases directory. We do this for good housekeeping reasons so that we are not bloating our server with releases that we don't need.

# RVM Issue

报错提示：
{% img http://p4.42qu.us/721/821/149301.jpg %}

好像是rvm不能选择ruby版本的问题。[后来发了一个帖子关于Linode上RVM的奇怪问题，每次ssh登陆服务器后，发现rvm并未选择ruby版本，即使上次登陆的时候rvm的默认ruby选项][http://ruby-china.org/topics/386],用了大家给的方法后,没找到合适答案后,rebuild linode服务器.

`Update @ 2011-12-9 12:57`

还是找到了Rvm的解决方案:

When you use capistrano it uses a non-interactive ssh session which doesn't run .profile or .bash_rc and it gets you a really bare bones environment. You will have to specify all the variables you need to use. This is especially important with RVM which relies on a lot of variables to work properly.

The following can be added to your capistrano recipe:
{% codeblock %}
set :default_environment, {
'PATH' => "/usr/local/sphinx/bin:/usr/local/rvm/gems/ruby-1.9.2-p0/bin:/usr/local/rvm/gems/ruby-1.9.2-p0@global/bin:/usr/local/rvm/rubies/ruby-1.9.2-p0/bin:/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
'RUBY_VERSION' => 'ruby-1.9.2-p0',
'GEM_HOME' => '/usr/local/rvm/gems/ruby-1.9.2-p0',
'GEM_PATH' => '/usr/local/rvm/gems/ruby-1.9.2-p0:/usr/local/rvm/gems/ruby-1.9.2-p0@global',
'BUNDLE_PATH' => '/usr/local/rvm/gems/ruby-1.9.2-p0',
'MY_RUBY_HOME' => '/usr/local/rvm/rubies/ruby-1.9.2-p0',
:'RAILS_ENV' => 'production'
} 
{% endcodeblock %}
You'll probably have to tweak this for your particular enviroment, but hopefully this gives you a good starting point to work from.

#Rebuild Linode
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

#Chef-Cookbooks&Recipe

- 因为又要装很多服务，而且也会有很多配置,考虑到效率，选择使用chef-repo,本地建立chef workstation。
- 表示一下,chef的文档太详细了!
- 前阵子修改了服务器域名转向,需要修改一下.SSH中的known_hosts,删掉冲突的行就行了.
- chef-repo依然是git版本控制,很强大

先看个在远程服务器node上安装apache2的sample:

{% codeblock local:workstation %}
Zh:chef-repo bill$ knife cookbook site install apache2
Zh:chef-repo bill$ knife node run_list node name add "recipe[apache2]"
Zh:chef-repo bill$ ls cookbooks/
apache2	
Zh:chef-repo bill$ knife node run_list add "recipe[apache2]"
Zh:chef-repo bill$ knife cookbook upload apache2
Uploading apache2             [1.0.2]
upload complete
Zh:chef-repo bill$ knife node show log -r
run_list:  recipe[apache2]
{% endcodeblock %}

{% codeblock node:nodename %}
nodename:# sudo chef client
{% endcodeblock %}


##安装workstation配置chef-client

- 因为使用的是Hosted-Chef,所以首先你要有一个opscode的账号,免费版的提供五个节点和两个管理员权限.然后创建一个orgnazition，会提示你下载knife配置文件和密钥文件,然后也到Users的界面生成密钥,一起下载下来
- 安装依赖:ruby ree +,ruby-dev,libopenssl-ruby,ri,irb,build-essential,wget,ssl-cert,git-core
- 安装rubygems:下载源码后解压，cd到该路径,执行`sudo ruby setup.rb --no-format-executable`
- 安装chef，创建必须的目录
{% codeblock %} 
Zh:~ bill$ sudo gem install chef
Zh:~ bill$ git clone https://github.com/opscode/chef-repo.git
  
  # create chef configuration directories:
Zh:~ bill$ mkdir -p ~/chef-repo/.chef
  
  # copy the keys and knife configuration you download into this directory
Zh:~ bill$ cp USERNAME.pem ~/chef-repo/.chef
Zh:~ bill$ cp ORGANIZATION-validator.pem ~/Documents/chef-repo/.chef
Zh:~ bill$ cp knife.rb ~/chef-repo/.chef
{% endcodeblock %}  
- 测试一下knife是否与Hosted Chef API 建立了连接

{% codeblock %}
Zh:chef-repo bill$ knife client list
  ORGNIZATION-validator
{% endcodeblock %}

> workstation就完成了,现在可以下载并上传recipe,以及用knife管理node的配置安装工作了.你可以选择将workstation这台电脑配置成chef-client,或者在一个node上配置chef-client并用workstation管理它.

### 配置本地的chef client

{% codeblock %}
Zh:~ bill$ cd chef-repo
Zh:~ bill$ knife configure client ./client-config
#After this done,you will have a client-config directory in your local repository:

Zh:~ bill$ sudo mkdir /etc/clef
Zh:~ bill$ sudo cp -r ~/chef-repo/client-config/* /etc/chef

#You will now need to run chef-client for the first time,so your node is added to the client list.
Zh:~ bill$ sudo chef-client

#Check if works
Zh:~ bill$ cd chef-repo
Zh:chef-repo bill$ knife client list 
{% endcodeblock %}

#### cookbook
#### recipe

### 配置远程的chef client




