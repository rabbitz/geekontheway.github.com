---
layout: post
title: "Octopress: Configuration files"
date: 2011-12-02 07:44
comments: true
categories: octopress
---


# _config.yml


{% codeblock lang:yaml %}
# ----------------------- #
#      Main Configs       #
# ----------------------- #

url: http://geekontheway.github.com
title: Geekontheway 
subtitle: Stay hungry Stay foolish
author: Bill Zh
simple_search: http://google.com/search

# Default date format is "ordinal" (resulting in "July 22nd 2007")
# You can customize the format as defined in
# http://www.ruby-doc.org/core-1.9.2/Time.html#method-i-strftime
# Additionally, %o will give you the ordinal representation of the day
date_format: "ordinal"

# RSS / Email (optional) subscription links (change if using something like Feedburner)
subscribe_rss: /atom.xml
subscribe_email:
# RSS feeds can list your email address if you like
email:

# ----------------------- #
#    Jekyll & Plugins     #
# ----------------------- #

# If publishing to a subdirectory as in http://site.com/project set 'root: /project'
root: /
permalink: /blog/:year/:month/:day/:title/
source: source
destination: public
plugins: plugins
code_dir: downloads/code
category_dir: blog/categories
markdown: rdiscount
pygments: true # default python pygments have been replaced by pygments.rb

paginate: 10          # Posts per page on the blog index
pagination_dir: blog  # Directory base for pagination URLs eg. /blog/page/2/
recent_posts: 5       # Posts in the sidebar Recent Posts section
excerpt_link: "Read on &rarr;"  # "Continue reading" link text at the bottom of excerpted articles

titlecase: true       # Converts page and post titles to tilecase

# list each of the sidebar modules you want to include, in the order you want them to appear.
# To add custom asides, create files in /source/_includes/custom/asides/ and add them to the list like 'custom/asides/custom_aside_name.html'
default_asides: [asides/recent_posts.html, asides/github.html, asides/twitter.html, asides/delicious.html, asides/pinboard.html, asides/googleplus.html]

# Each layout uses the default asides, but they can have their own asides instead. Simply uncomment the lines below
# and add an array with the asides you want to use.
# blog_index_asides:
# post_asides:
# page_asides:

# ----------------------- #
#   3rd Party Settings    #
# ----------------------- #

# Github repositories
github_user: geekontheway
github_repo_count: 0
github_show_profile_link: true
github_skip_forks: true

# Twitter
twitter_user:
twitter_tweet_count: 4
twitter_show_replies: false
twitter_follow_button: true
twitter_show_follower_count: false
twitter_tweet_button: true

# Google +1
google_plus_one: true
google_plus_one_size: medium

# Google Plus Profile
# Hidden: No visible button, just add author information to search results
googleplus_user: xiaoliang@networking.io
googleplus_hidden: false

# Pinboard
pinboard_user:
pinboard_count: 3

# Delicious
delicious_user:
delicious_count: 3

# Disqus Comments
disqus_short_name:
disqus_show_comment_count: false

# Google Analytics
google_analytics_tracking_id:

# Facebook Like
facebook_like: false
{% endcodeblock %}


# config.rb


{% codeblock lang:ruby %}
require 'yaml'
YAML::ENGINE.yamler = 'syck'
project_type = :stand_alone

# Publishing paths
http_path = "/"
http_images_path = "/images"
http_fonts_path = "/fonts"
css_dir = "public/stylesheets"

# Local development paths
sass_dir = "sass"
images_dir = "source/images"
fonts_dir = "source/fonts"

line_comments = false
output_style = :compressed

{% endcodeblock %}


#config.ru

{% codeblock lang:ruby %}
require 'bundler/setup'
require 'sinatra/base'

# The project root directory
$root = ::File.dirname(__FILE__)

class SinatraStaticServer < Sinatra::Base  

  get(/.+/) do
      send_sinatra_file(request.path) {404}
    end

  not_found do
      send_sinatra_file('404.html') {"Sorry, I cannot find #{request.path}"}
    end

  def send_sinatra_file(path, &missing_file_block)
    file_path = File.join(File.dirname(__FILE__), 'public',  path)
    file_path = File.join(file_path, 'index.html') unless file_path =~ /\.[a-z]+$/i  
    File.exist?(file_path) ? send_file(file_path) : missing_file_block.call
  end

  end
{% endcodeblock %}

