---
layout: post
title: "Octopress: Blockquote"
date: 2011-12-03 12:48
comments: true
categories: octopress
---
The blockquote plugin takes and **author, source, title and quote, and outputs** semantic HTML.

> Note that they also must start and end with brace and persent. 

#Syntax

 >blockquote [author[, source]] [link] [source_link_title] 
 >
 >Quote string
 >
 endblockquote
 
You’ll notice there are two entries for source; one after author, and one after link. If you are citing from a printed work or a speech or something you cannot link to, use the first method Author's Name, Cited Work. If you’re going to link to the work, omit the first source method, and add a source title after the link Author's Name http://source.com Article title.

###Examples

{% blockquote @allanbranch https://twitter.com/allanbranch/status/90766146063712256 %}
Over the past 24 hours I've been reflecting on my life & I've realized only one thing. I need a medieval battle axe.
{% endblockquote %}


{% blockquote Seth Godin http://sethgodin.typepad.com/seths_blog/2009/07/welcome-to-island-marketing.html Welcome to Island Marketing %}
Every interaction is both precious and an opportunity to delight.
{% endblockquote %}
