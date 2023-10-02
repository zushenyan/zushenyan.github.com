---
layout: post
title: "How to pass \"this\" into anonymous function"
date: 2013-08-29 22:28
comments: true
tags: javascript
---

very useful when using callback relative things.

{% codeblock lang:js %}
var me = this;
var a = func1(function(){
	me.doSomething();
});
{% endcodeblock %}
