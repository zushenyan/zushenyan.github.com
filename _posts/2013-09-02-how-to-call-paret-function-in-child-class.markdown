---
layout: post
title: "How To Call Parent Function in Child Class"
date: 2013-09-02 23:39
comments: true
---

{% codeblock lang:js %}
SubClass.prototype.foobar = function(str) {
	BaseClass.prototype.foobar.call(this, str);
	console.log("bbb");
};
{% endcodeblock %}

yea, as simple as that.

Don't forget sub-class is made of this

{% codeblock lang:js %}
SubClass.prototype = new BaseClass();
{% endcodeblock %}

This makes all legit.

Full source view

{% codeblock lang:js %}
function BaseClass(){}
BaseClass.prototype.foobar = function(str) {
	console.log(str);
};

function SubClass(){}
SubClass.prototype = new BaseClass();
SubClass.prototype.constructor = SubClass;
SubClass.prototype.foobar = function(str) {
	BaseClass.prototype.foobar.call(this, str);
	console.log("bbb");
};
{% endcodeblock %}
