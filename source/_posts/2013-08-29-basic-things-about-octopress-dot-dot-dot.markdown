---
layout: post
title: "Basic Things About Octopress..."
date: 2013-08-29 21:35
comments: true
tags: octopress
---

# Deploy relative

## Make a new post

In octopress root directory, type

{% codeblock %}
username$ rake new_post["title name"]
{% endcodeblock %}

you can find this post in source/_posts

## Publish blog & post

{% codeblock %}
username$ rake generate
username$ rake deploy
{% endcodeblock %}

after entering github username & password you are done.

## Delete a post

Simply go to source/_posts ,delete what post you don't want and run

{% codeblock %}
username$ rake generate
username$ rake deploy
{% endcodeblock %}

## Preview and watch changes of blog

{% codeblock %}
username$ rake generate
username$ rake preview
{% endcodeblock %}

# Edit relative

## Add block

see [here](http://octopress.org/docs/plugins/codeblock/)

## Link

{% codeblock %}
This is [an example](http://example.com/ "Title") inline link.
{% endcodeblock %}

## Image

{% codeblock %}
![alt text](url)
{% endcodeblock %}

![cat](http://farm6.staticflickr.com/5216/5475775811_f3974c5d07.jpg)
