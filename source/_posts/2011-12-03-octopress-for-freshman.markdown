---
layout: post
title: "Octopress: 新手教程"
date: 2011-12-03 09:16
comments: true
categories: otocpress
---

Octopress基于jekyll，刚开始使用起来也比较复杂：

>Octopress会有两个分支：source（编写博客）和master（生成好的博客），就像这样：

{% codeblock lang:sh %}

git remote -v

octopress	git://github.com/imathis/octopress.git (fetch)
octopress	git://github.com/imathis/octopress.git (push)
origin	git@github.com:geekontheway/geekontheway.github.com.git (fetch)
origin	git@github.com:geekontheway/geekontheway.github.com.git (push)


git branch -a 

* source
  remotes/octopress/HEAD -> octopress/master
  remotes/octopress/compass
  remotes/octopress/configuration
  remotes/octopress/edge
  remotes/octopress/generate_environment
  remotes/octopress/gh-pages
  remotes/octopress/master
  remotes/octopress/move_rakefile_configs
  remotes/octopress/post_names
  remotes/octopress/rake_minify_js
  remotes/octopress/refactor_code_highlight
  remotes/octopress/refactor_deployment
  remotes/octopress/refactor_js
  remotes/octopress/site
  remotes/octopress/site-deploy-test
  remotes/octopress/subdir
  remotes/octopress/thor

{% endcodeblock %}

其中只有origin仓库和source分支是必须的，其余分支或仓库建议删掉。

我们来看一下source分支都有什么：

{% codeblock %}

CHANGELOG.markdown  
config.ru   
 _deploy  部署文件夹，在.gitignore中被设置了
.DS_Store 文件夹显示属性，在.gitignore中被设置了
Gemfile.lock  
.gitignore  
plugins  
.pygments-cache   
.rbenv-version Ruby版本有特殊要求 这个文件在.gitignore中被设置了   
.rvmrc   Ruby版本有特殊要求
.sass-cache  
source
config.rb           
_config.yml  
Gemfile  
.git          
.idea   Rubymine设置文件，在.gitignore中被设置了    
public  在.gitignore中被设置了  
Rakefile         
README.markdown  
sass    
.slugignore  
.themes

{% endcodeblock %}

其中_deploy,source,public这三个文件夹很有趣：

{% img http://p4.42qu.us/721/689/144049.jpg %}

- 如果你是和别人合作博客，或者自己同时在好几个电脑上写博客，每次开始之前，`git pull origin source`获得最新的文件,`rake generate`生成新的页面

- 我们在source分支做了博客的发布，或者改变了博客的设置之后，`rake generate`生成网站

- `rake watch`+pow 或者`rake review`+http://localhost:4000就可以看到我们所做的变化

- 确认无误后，`rake deploy`文章就发布到了博客中

- 当然，不要忘了更新项目 `git push origin source`

> 特别的，如果你新建了博客或者克隆了博客，记得在source分支`rake setup_gh_pages`执行初始化，当然，你可能也需要`bundle install`
>
> 如果是新建的Repo ，记得

{% codeblock lang:sh %}
$ mkdir yourrepo
$ cd yourrepo
$ git init
# 其实这这时如果你多新建一个index.html文件的话，github会为你生成一个jekyll博客。
$ touch README
$ git add .
$ git commit -m 'first commit'
$ git remote add origin git@github.com:username/yourname.github.com.git
$ git push origin master

{% endcodeblock %}

对于新手有几个提醒:

- 时常`git status`,`git log`避免误操作
- 不要在github上直接编辑文件
- 想清楚了再下手
- github pages的 username 大小写敏感。如果用户名和username不一致的话,默认会生成这个Repo的project pages。

