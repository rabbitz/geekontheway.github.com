---
layout: post
title: "Octopress: Codeblock"
date: 2011-12-03 12:21
comments: true
categories: octopress
---
With this plugin you can write blocks of code directly in your posts and optionally add titles and links.

# Syntax

Start and end must be in **one couple of brace percent**,they will also works in HTML block.

<pre>
 codeblock [title] [lang:language] [url] [link text] 

 code snippet

 endcodeblock 
</pre>

# Example

<pre>
 codeblock
Awesome code snippet
 endcodeblock
</pre>


You can also add syntax highlighting like this.

<pre>
 codeblock lang:objc
[rectangle setX: 10 y: 10 width: 20 height: 20];
 endcodeblock
</pre>

Including a file extension in the title enables highlighting

<pre>
 codeblock Time to be Awesome - awesome.rb
puts "Awesome!" unless lame
 endcodeblock
</pre>

Pygments supports many languages, but doesn’t recognize some file extensions. Add lang:your_language to force highlighting if the filename doesn’t work.

<pre>
 codeblock Here's an example .rvmrc file. lang:ruby
rvm ruby-1.8.6 # ZOMG, seriously? We still use this version?
 endcodeblock
</pre>

Add an optional URL to enable downloading or linking to source.

<pre>
 codeblock Javascript Array Syntax lang:js http://j.mp/pPUUmW MDN Documentation
var arr1 = new Array(arrayLength);
var arr2 = new Array(element0, element1, ..., elementN);
 endcodeblock
</pre>

The last argument link_text is optional. You may want to link to a source for download file, or documentation on some other site.