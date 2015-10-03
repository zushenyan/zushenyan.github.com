---
layout: post
title: "JavaScript Module Pattern"
date: 2013-09-02 23:57
comments: true
---

Just a simple code and note here.

For more info about module pattern refer [here](http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html).

{% codeblock lang:js %}
var obj = (function(){
	var obj = function(){
		//constructor
	}

	obj.prototype.foobar = function() {
		// do things here
	};

	return obj;
})();
{% endcodeblock %}
