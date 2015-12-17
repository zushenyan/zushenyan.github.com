---
layout: post
title: "Front-end Job Interview Questions"
date: 2015-12-13T17:14:51+08:00
---

Source
---

[Front-end Job Interview Questions](https://github.com/h5bp/Front-end-Developer-Interview-Questions)

Won't answer every question. I don't know everything and some of them cover large enough to make standalone articles.

General Questions
---

> Talk about your preferred development environment.

Mac + Atom + Gulp + Webpack/Broserify + CLI.

> Which version control systems are you familiar with?

Git.

> Can you describe your workflow when you create a web page?

1. Build up working environment. e.g. create directories, set up webpack, initialize git...
2. Outline what features the site should have.
3. Outline the UI of the site.
4. Start working on it. (Write tests if possible)

> If you have 5 different stylesheets, how would you best integrate them into the site?

Assume each stylesheet is a file, and works for different parts of the website. The first I would do is adding "namespace" for each stylesheet to prevent class name conflict. The second thing I would do is concatenating these files into 1 to improve website download speed and browser response time.

> Can you describe the difference between progressive enhancement and graceful degradation?

Let's take `print this page` for example.

* Graceful Degradation - Provide a feature, but if the feature doesn't exist in older browsers, provide a lower level feature.

```html
<a href="javascript:print()">print this page</a>
<noscript>
	<p>You have to enable JavaScript to use this feature.</p>
</noscript>
```

Shows no script text only when client disabled JavaScript.

* Progressive Enhancement - Provide a feature, upgrade it only when current environment supports advanced functionality.

```html
<p>Please print this page.</p>
<script>
	(function(){
		// append a button under text above.
		// invoke print() when button is pressed.
	})();
</script>
```

Assume client disabled JavaScript, show button if JavaScript is allowed.

[Reference](https://www.w3.org/wiki/Graceful_degradation_versus_progressive_enhancement)

> How would you optimize a website's assets/resources?

* Minifying and bundling JS/CSS/HTML files - It can be done via some tools like Webpack or Uglify.
* Image sprite - Make many images into one big image file.
* Image inlining - Transform images into base64 string and inline them directly into HTML files.
* Image resizing - If no need to render images in actual resolution, resize it.
* Image compressing - Compress your into a smaller piece.
* Gzip - Enable gzip content compression on server side.
* Cache - Specify Cache-Control attribute for each file header on server side.
* CDN(Content Deliver Network) - Put resources into different domains to break the limitation of maximum connection count.

Assets/resourcess optimization is a way to reduce request times under HTTP/1.1 protocol.

Besides minifying, compressing, gzip and cache, I don't see any reason to do rest approaches once HTTP/2 becomes popular, thanks to multiplex.

> How many resources will a browser download from a given domain at a time? What are the exceptions?

Ranges from 6 to 8 connections per domain. It depends on what browser is user using.

The exceptions are SPDY and HTTP/2 protocols. Browsers that support these protocols only have to open one connection to download resources.

> Name 3 ways to decrease page load (perceived or actual load time).

Since I have already talked explained about optimization in "How would you optimize a website's assets/resources?" section, here I will only talk about actual load time:

* Put `link` in `head` section - This will make browser load stylesheets first.
* Put `script` at the bottom of document - Do this to prevent browser from render blocking.
* Enable `async` attribute in HTML5 - Specifying `async` attribute on `script` tag to allow browser executing code and parse page at the same time.
* Don't use complicated css query - `#foo ul li .bar` nested and complicated query like this. It is time costly for browsers to parse.

> Describe how you would create a simple slideshow page.

I think some points are needed to be clarified first:

1. What platforms will this application support? Browser? Mobile Browser? Desktop? Mobile Native App?
2. What screen size does this application target for?
3. What browser version will this application support down to?
4. What feature does it really have?
5. What is the estimated time schedule?

Second:

1. What tool should I use? Is it well documented? Does it have an active community for asking help?
2. What techniques might this application have involved?
3. Is there any similar work or tutorial that I can learn with?

> What is Flash of Unstyled Content? How do you avoid FOUC?

Browser renders default stylesheet(unstyled) before external stylesheet is received/loaded. The transition state between default stylesheet to external stylesheet is called FOUC.

This is caused by putting `link` tag to stylesheets after the `head` tag, or with `@import` because it loads stylesheet asynchronously.

Solution is pretty simple - put `link` in `head` and don't use `@import`.

> Explain some of the pros and cons for CSS animations versus JavaScript animations.

CSS Animation

- Pros
  - Natively GPU supported on mobile.
	- Easy to use.
- cons
  - Asynchronous actions, pause, stop... complicated animation control not possible.
	- Not supported by older browsers. Even if it supports, not every browser has the same implementation.
	- Hard to organize when animation grows.

JavaScript Animation

- Pros
  - GPU support.
	- Compared to CSS 3 animation, it is more widely supported.
	- Can do complicated flow control.
- Cons
  - Steep learning curve compares to CSS 3 animation.

> What does CORS stand for and what issue does it address?

Cross-Origin-Resource-Sharing, CORS, is a standard mechanism that let you access cross-domain resources with normal ajax request.

Without CORS, you can only access services from other domain via JSONP or server proxy.

You can enable this by simply adding an attribute in your file header from server side:

```
Access-Control-Allow-Origin: http://my-domain-name.com
```

HTML Questions
---

> What does a doctype do?

Also known as Document Type Definition - DTD.

There are many versions of HTML(e.g. HTML4.1, XHTML...). By defining `<!DOCTYPE html>` at the top of your HTML file tells the browser what's the current version of HTML your are using, and it let browser to parse your page in that standard.

Document with doctype declared will enter standard mode, otherwise or lack of URL will result in quirk mode.

> What's the difference between standards mode and quirks mode?

Quirks mode - also stands for **non-standard** mode or **backward-compability** mode, browsers will parse things a little different from standard mode.

Standard mode - also known as **strict mode**, browsers will follow mostly part of W3 specification for parsing.

Without declaring `<!DOCTYPE html>` may trigger quirks mode in older browsers.

[Quirks Mode and Standards Mode](https://developer.mozilla.org/en-US/docs/Quirks_Mode_and_Standards_Mode)

> What's the difference between HTML and XHTML?

XHTML stands for eXtensible HTML. You can also think it as HTML in XML or strict syntax HTML.

By sending `application/xhtml-xml` MIME type, browsers will treat this document as an XML file.

There are many minor differences between HTML and XHTML. Here I only name a few:

* All elements should have always be closed.
* Attributes must be lower case.
* Attributes minimization is not allowed.

For full specification visit [w3c](http://www.w3.org/TR/xhtml1/)

> Are there any problems with serving pages as application/xhtml+xml?

By doing this will let browsers treat this document as an XML file, which turns HTML into XHTML.

On IE 6,7,8 this will prompt a download dialog instead of rendering document.

> What are data- attributes good for?

You can define your own attributes. Saving data or state in an element instead of having another variable for recording in JavaScript.

> Describe the difference between a cookie, sessionStorage and localStorage.

Cookie

* Can access via JavaScript, or be disallowed to access via JavaScript via `http-only` attribute in header.
* Limited counts per domain.
* Only store up to 4 kb.
* Will be transmitted via packet in header.
* Can be sent to or set by server.
* Have various attributes to set via `Set-Cookie` attribute in header.

Session and local storage are all HTML5's API and also called web storage. They do mostly the same, except session storage delete data when session ends while local storage does not.

Web storage can hold up to 5 to 10 MB for each domain.

[What is the difference between localStorage, sessionStorage, session and cookies?](http://stackoverflow.com/questions/19867599/what-is-the-difference-between-localstorage-sessionstorage-session-and-cookies)

> Describe the difference between <script>, <script async> and <script defer>.

`<script>` - load and execute script synchronously. Browser will execute the code if encounter `<script>`. Will cause blocking.

`<script async>` - load and execute script asynchronously. Browser will execute the code and load other resource parallel. Will not cause blocking.

`<script defer>` - load and execute script synchronously. The only different is that it indicates browser should only execute the code only after the document is parsed.

> Why is it generally a good idea to position CSS <link>s between <head></head> and JS <script>s just before </body>? Do you know any exceptions?

Put CSS `<link>` in between `<head>` to avoid FOUC. Put `<script>` before `</before>` to avoid parse blocking.

Specifying `async` or `defer` in `<script>` if you really want to put `<script>` before `<link>`.

> What is progressive rendering?

It's actually an backend topic.

> Have you used different HTML templating languages before?

ejs.

CSS Questions
---

> What is the difference between classes and ID's in CSS?

Class stands for a collection of attributes under a name. It can be applied on many elements with same name(one to many). However, ID is unique to element and can only be applied on one element with the same ID(one to one).

> What's the difference between "resetting" and "normalizing" CSS? Which would you choose, and why?

Reset - undecorates or sets 0 to all browser default styles. You have to specify all styles yourself.

Normalize - preserves and ensures that default style on all browsers are the same.

I prefer normalize because go with reset will have to do extra works.

[What is the difference between Normalize.css and Reset CSS?](http://stackoverflow.com/questions/6887336/what-is-the-difference-between-normalize-css-and-reset-css)

> Describe Floats and how they work.

Float is a CSS property that should be taken from the normal flow and placed to the right or left of its container. Text or any inline element will wrap around it.

If an element has specified a value on float, that element's `display` property will be set to `block`.

It is always used with `clear` property to make desired element move down to the floated element. A common technique is **clearfix**

```css
.container:after {
	content: "";
	clear: both;
}
```

> Describe z-index and how stacking context is formed.

When elements overlap, `z-index` specifies the displaying order of an element. Larger z-index covers element with a lower one.

New elements in a container always follow parent's `z-index` value.

`z-index` can be negative to have elements displaying below.

> What are the various clearing techniques and which is appropriate for what context?

Traditional way:

```css
.clear {
	clear: both;
}
```

```html
<div class="container">
	<div class="float">foo</div>
	<div class="float">bar</div>
	<br class="clear" />
</div>
<div class="footer">This container should be cleared</div>
```

Overflow way:

```css
.container {
	overflow: hidden;
}
```

```html
<div class="container">
	<div class="float">foo</div>
	<div class="float">bar</div>
</div>
<div class="footer">This container should be cleared</div>
```

Clearfix:

```css
.clearfix:after {
	clear: both;
}
```

```html
<div class="container clearfix">
	<div class="float">foo</div>
	<div class="float">bar</div>
</div>
<div class="footer">This container should be cleared</div>
```

> What are your favourite image replacement techniques and which do you use when?

[image replacement](https://css-tricks.com/the-image-replacement-museum/)

> How would you approach fixing browser-specific styling issues?

see "What's the difference between "resetting" and "normalizing" CSS? Which would you choose, and why?".

> What are the different ways to visually hide content (and make it available only for screen readers)?

`display: none` or `visibility: hidden`.

Element with `display: none` will take place in view even it's invisible.

Element with `display: none` will not take place in view.

> Have you ever used a grid system, and if so, what do you prefer?

Only experienced grid system in Twitter Bootstrap.

> Have you used or implemented media queries or mobile specific layouts/CSS?

Hand-made? Sure, but actually, I use Twitter Bootstrap to solve RWD problems.

> What are the advantages/disadvantages of using CSS preprocessors? Describe what you like and dislike about the CSS preprocessors you have used.

SCSS.

Advantages:

* More ways like inheritance, variable... for writing modularized CSS.
* Some libraries like Bourbon, Compass provide nice polyfills.

Disadvantages:

* Extra learning/traning required.
* Need to setup up working environment before work begin.

> Explain how a browser determines what elements match a CSS selector.

The only thing I know is that browser matches selector from right to left.

> Describe pseudo-elements and discuss what they are used for.

Pseudo-elements like `element::first-child` or `element::after` are special elements that make styling more easy.

For example `p::first-line` will only style the first line of every paragraph.

> Explain your understanding of the box model and how you would tell the browser in CSS to render your layout in different box models.

Box model indicates element is composed of 4 parts: margin. border, padding, content.

* Margin - an empty area used for separating area with other elements.
* Border - the thickness between padding and margin.
* Padding - extending of content area.
* Content - the space text/media occupies.

You can switch box model with `content-box` and `border-box` in `box-sizing`. Default value is `content-box`.

* `content-box` - The `width` and `height` properties are measured only content size, no padding, border, or margin.
* `box-sizing` - The `width` and `height` properties are measured including content, padding, border, no margin.

> What does * { box-sizing: border-box; } do? What are its advantages?

Using `border-box` to calculate elements' `width` and `height` is more intuitive.

Say, I want to put 4 boxes in a horizontal block. Using `content-box` I have to calculate each box's border and content to fit into container, but using `border-box` I only have to set `width:25%` on each box and set each box's border without calculating.

> List as many values for the display property that you can remember.

* block
* inline-block
* inline
* flex
* inline-flex
* none
* table
* inline-table
* initial
* inherit
* ...

[Display CSS property on MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/display)

> What's the difference between inline and inline-block?

Browser will put elements with `display: inline-*` property on the same row. The difference between `inline` and `inline-block` is that `inline` elements with content empty won't effect by `width` or `height`.

> What's the difference between a relative, fixed, absolute and statically positioned element?

* Fixed - elements will be positioned independently and its' position is according to the whole view.
* Static - default value. Elements will be positioned depending on each other whose `position` property is not `absolute` or `fixed`.
* Relative - enable `top`, `bottom`, `left` and `right` properties, which let you move element relative to where it should be.
* Absolute - enable `top`, `bottom`, `left` and `right` properties. Elements with `position: absolute` will be positioned independently or be positioned according to the nearest container with `position: relative`.

> The 'C' in CSS stands for Cascading. How is priority determined in assigning styles (a few examples)? How can you use this system to your advantage?

`!important > style > id > class > tag`

If I really want something should not be changed, I will use `!important` to keep my style like it should be. Especially where you have other 3rd party, external stylesheets import in, styles may be conflict between each other, you will want to keep your own style untouched.

> What existing CSS frameworks have you used locally, or in production? How would you change/improve them?

Twitter Bootstrap, Bourbon, SCSS/SASS.

Personally, I use them to help me enhance UI or get rid of some repeat works.

> Have you played around with the new CSS Flexbox or Grid specs?

Tried Flexbox before, enough to replace some old trick like `float` and also works really well in RWD.

Tried Grid as well. It arranges layout beautifully and works well with media queries in RWD also.

> How is responsive design different from adaptive design?

Responsive - adapt different devices with one layout with minimum change.

* pros - easy to maintain.
* cons - screen sizes are many, however in most RWD only have 4 layout - desktop/latop/tablet/mobile, which only provides a basic comfort level, not the best user experience.

Adaptive - adapt different devices with different layouts.

* pros - provides best experience on different devices.
* cons - maintaining between layouts means extra work.

JS Questions:
---

> Explain event delegation

Event delegation means using parent container as children's event delegator. For example, we have a list here:

```html
<ul id="list">
	<li id="list-1">I am list item 1</li>
	<li id="list-2">I am list item 2</li>
	<li id="list-3">I am list item 3</li>
</ul>
```

We want to get inner text when clicking on one of its child. Of course, we can add event listener to each one, but it will become a little crazy if we have to add more list item.

By the power of **event bubbling**, there is an more elegant way to deal with this issue.

```js
document.getElementById("list").addEventListener("click", function(e){
	console.log(e.target.innerText);
});
```

> Explain how this works in JavaScript

`this` means the current owner of the scope.

When an object is created via `new`, JS binds `this` to the newly created object.

> Explain how prototypal inheritance works

In ES5

```js
var Person = function(name){
	this.name = name;
}

var John = function(name){
	Person.call(this, name);
}

John.prototype = Object.create(Person.prototype); // reference prototype to Person's prototype
John.prototype.constructor = Person; // set John's constructor to Person's constructor
```

In ES6. It's identical to ES5 prototype inheritance chain.

```js
class Person{
	constructor(name){
		this.name = name;
	}
};

class John extends Person{
	constructor(name){
		suepr(name);
	}
}
```

> What do you think of AMD vs CommonJS?

AMD is an JS module standard. Its implementation is RequireJS and only works on browser side. Meanwhile CommonJS works on server side.

Writing in RequireJS is somewhat cumbersome:

```js
requirejs(["directory/anotherJsFile"], function(anotherJsFile){
	// do something
});
```

CommonJS is more intuitive to programmers coming from other languages:

```js
var anotherJsFile = require("./directory/anotherJsFile");

var myObj = myObj;

module.exports = myObj;
```

> Explain why the following doesn't work as an IIFE: function foo(){ }();. What needs to be changed to properly make it an IIFE?

`function foo(){ }()` which is equal to `undefined()`. Executing things undefined is not possible.

The correct way is:

```js
(foo(){
	// do something.
})();
```

> What's the difference between a variable that is: null, undefined or undeclared? How would you go about checking for any of these states?

* undeclared - use variables before declaring interpreter will throw `ReferenceError: variable is not defined` in your face.
* undefined - use an declared variable without initializing its value will return `undefined`.
* null - `null` is a value to represent empty.

Checking state, assume its in Chrome console:

```js
> a
ReferenceError: a is not defined
> var a
> a
undefined
> a === null
false
```

> What is a closure, and how/why would you use one?

The most common usage is to return private member's value without exposing it.

```js
function(){
	var secret = {code: "can't tell you!"};
	return (function(){
		return secret;
	})();
}
```

Personally I would try to avoid using it if possible because closure extends variables' lifespan to its scope. This causes memory leak easily plus it's quite confusing to people coming from other languages.

> What's a typical use case for anonymous functions?

When I only need to do things one-time only or don't even invoke it in further.

> How do you organize your code? (module pattern, classical inheritance?)

I write ES6 with Babel and organize my module with ES6 `import` and `export` features.

As for `inheritance`, use `extends` instead.

> What's the difference between host objects and native objects?

Host objects means browser specific features. For example, DOM objects in Chrome is possibly different from IE.

Native objects means JavaScript's core feature like `array` or `Object`.

> Difference between: function Person(){}, var person = Person(), and var person = new Person()?

* `function Person(){}` - a function.
* `var person = Person()` - returns the result after executing Person function.
* `var person = new Person()` - bind `this` in `person` scope to `person`.

> What's the difference between .call and .apply?

Both of them invokes function they are calling for.

First argument accepts what `this` refers to when running that function.

The only difference is that `.call` accepts rest of the arguments with **arguments list** while `.apply` accepts **array of arguments**.

> Explain Function.prototype.bind.

Indicates what `this` refers to when running that function.

`Function.prototype.bind` only returns function, not executing.

> When would you use document.write()?

Testing.

> What's the difference between feature detection, feature inference, and using the UA string?

Feature detection tests the existence of a feature.

```js
if(window.XMLHttpRequest){
	var ajax = new XMLHttpRequest();
}
```

Feature inference checks if one feature exits, but uses another feature.

```js
if(document.getElementByTagName){
	var ele = document.getElementById("myEle");
}
```

UA string means identifying browser version with user agent. It should not be used anymore because you will have to maintain various version for different browsers.

```js
if(navigator.userAgent.indexOf("MSIE 7")){
	// do something
}
```

> Explain AJAX in as much detail as possible.

AJAX stands for Asynchronous JavaScript and XML. It's a technique to retrieve data from server without reloading the whole page.

This is how it is done in vanilla JS and modern browser.

```js
var ajax = new XMLHttpRequest();
ajax.addEventListener("load", function(){
	console.log(ajax.responseText);
});
ajax.open("GET", "http://mydomain.com");
ajax.send();
```

> Explain how JSONP works (and how it's not really AJAX).

JSONP stands for JSON with Padding. JSONP doesn't involve in AJAX at all and actually. To achieve JSONP, we need the help from the server side. Server side's code looks like this:

```js
var http = require("http");
var url = require("url");
var querystring = require("querystring");

http.createServer(function(req, res){
  var queryTemp = url.parse(req.url).query;
  var query = querystring.parse(queryTemp);
  res.writeHeader(200, {
    "content-type": "text/plain"
  });
  res.end(query.callback + "('Hello JSONP!')");
}).listen(3001);
```

Client:

```js
<!DOCTYPE html>
<html>
  <head>
    <title>jsonp test</title>
    <script id="jsonp"></script>
  </head>
  <body>
    <div id="text"></div>
    <button id="button">jsonp!</button>
    <script>
      var text = document.getElementById("text");
      var button = document.getElementById("button");
      var jsonp = document.getElementById("jsonp");

      function contentLoaded(){
        button.addEventListener("click", function(e){
          jsonp.src= "http://localhost:3001/?callback=changeText";
        });
      }

      function changeText(string){
        text.appendChild(document.createTextNode(string));
      }

      window.addEventListener("DOMContentLoaded", contentLoaded);
    </script>
  </body>
</html>
```

We fetch data by changing `<script>`'s `src` value to our target URL, and append full JavaScript code by gluing with result:

```html
<script src="jsonp">
	changeText('Hello JSONP!')
</script>
```

> Have you ever used JavaScript templating? If so, what libraries have you used?

EJS.

> Explain "hoisting".

Hoisting means any variable declaration will be processed before any code is executed in **current scope**. Take code below for example:

```js
a = 5;
var a;
```

is equal to

```js
var a;
a = 5;
```

> Describe event bubbling.

When an event is fired, it runs a callback function defined external. The event will keep **bubbling up** to its ancestor container until it hits the root of DOM or `event.stopPropagation()`.

See "event delegation" for detail.

> What's the difference between an "attribute" and a "property"?

Property is for DOM object or native JS object and attribute is for HTML tag.

> Why is extending built-in JavaScript objects not a good idea?

If you only have your own code in your work, that's fine, but when libraries are add in, extending native object may cause other's code broken because of overwriting other's code.

> Difference between document load event and document ready event?

There is no such `ready` event in DOM, so I assume `document ready` refers to jQuery's `$(document).ready` here.

`load` event in DOM tells browser to do something after **everthing including frames, images, asynchronous JavaScripts** are fully loaded.

`$(document).ready` indicates that it should occur as early as possible after the document has loaded.

> What is the difference between == and ===?

`==` equals in value, `===` equals in reference.

> Explain the same-origin policy with regards to JavaScript.

Same-Origin-Policy is an idea to prevent malicious script from accessing or intercepting data's in client browser. Same-Oirigin-Policy allows HTTP action request only if document and server are under the same domain.

There are two ways to "bypass" SOP, one is JSONP, the other is through CORS(Cross Origin Resource Sharing).

Read "Explain how JSONP works (and how it's not really AJAX)." for JSONP details.

CORS needs cooperation from server setting. You have to set `Access-Control-Origin-Allow: yourdomain.com` to domains which you want it to be allowed in header in order to access resource via normal AJAX call.

> Why is it called a Ternary expression, what does the word "Ternary" indicate?

Because it takes 3 arguments with 2 operators. A common Ternary expression is `:?`.

> What is "use strict";? what are the advantages and disadvantages to using it?

`use strict;` enables strict mode for JS parser. Strict mode is enabled by adding `use strict;` at the top of file or function scope.

Advantages - converts mistakes into errors.

* Using undeclared variable throws error.
* Using reserved keywords throws error.
* Naming arguments, properties with the same name throws error.
* and so on...

Disadvantages

* If you import script with strict mode on before any other scripts, you will force other scripts to go strict mode.

[strict mode on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)

> Why is it, in general, a good idea to leave the global scope of a website as-is and never touch it?

Because you probably will make a variable with same name as others will. That makes conflict.

> Why would you use something like the load event? Does this event have disadvantages? Do you know any alternatives, and why would you use those?

`load` event tells browser to do something only after **everthing including frames, images, asynchronous JavaScripts** are fully loaded.

If you want event function to execute before fully loaded frames, images, async scripts, use `domcontentloaded` instead.

> Explain what a single page app is and how to make one SEO-friendly.

> What is the extent of your experience with Promises and/or their polyfills?

Only tried it in ES6. Basic usage:

```js
new Promise((resolve, rejected) => {
	if(resolve){
		reslove("successed");
	}
	else{
		rejected("failed");
	}
})
.then((result) => {
	console.log(result);
})
.then((result) => {
	console.log(result);
});
```

The biggest advangtage of Promise is that it solves the problem of callback hell.

Polyfills:

* jQuery - Deferred
* Bluebird
* Q
* When

> What are the pros and cons of using Promises instead of callbacks?

pros - solves the problem of callback hell.
cons - currently require external library or polyfill to achieve this.

> What are some of the advantages/disadvantages of writing JavaScript code in a language that compiles to JavaScript?

Pros

* Benefit libraries that only exist in NodeJS world. (Make CommonJS stuff avialable on browser)
* The newest of language features.

Cons

* Have to setup environment before working.
* Have to orgranize more directories.
* Source map is needed.

> What tools and techniques do you use debugging JavaScript code?

Chrome Dev Tools.

> What language constructions do you use for iterating over object properties and array items?

Array

* classic `for`
* `arr.forEach()`

Object

* `for(var key in obj)`
* `Object.keys(obj).forEach()`

> Explain the difference between mutable and immutable objects. What is an example of an immutable object in JavaScript? What are the pros and cons of immutability? How can you achieve immutability in your own code?

Mutable object means its state is allowed to be changed over time. Immutable object's value doesn't change from the time it was created.

Immutable examples are primitive types like `String`, `Number`. You can't change the definition of `2` after executing `2 + 5`. No matter how you operate strings, the definition of `c` won't change.

Mutable examples are `array`, `object` or anything opposite to immutability. You can change the value of an array or object anytime and the result will be what you desired.

Immutable object won't be changed after it has been initialized. We can take advantage of this. Making immutable objects into a collection of cache since these objects don't change at all. Our program is actually accessing the same data. This is a good approach to saving memory by taking advantage of immutable. The downside of immutability is that it actually involving constantly deep clone and assigning. This is an overhead of trading computing speed for memory.

To achieve immutability on `array` or `object` or any type you want, you have to do deep clone, or simply use library like `immutable.js` developed by Facebook.

> Explain the difference between synchronous and asynchronous functions.

The difference is that sync function is **blocking** while async is **non-blocking**.

```js
function blocking(){
	console.log("1");
	console.log("2");
}

function nonBlocking(){
	setTimeout(function(){
		console.log("1");
	}, 1000);
	console.log("2");
}
```

> What is event loop? What is the difference between call stack and task queue?

Event loop is how JavaScript with single-threaded performs tasks without blocking.

Event loop is a queue of callback functions. When a asynchronous function executes, it is pushed into task queue and JavaScript will only start processing task queue after codes after async function are executed.

The difference between call stack and task queue is that **task queue** is a place where JavaScrip schedules async function while **call stack** is a place for JavaScript to trace what the current function is.

Network Questions
---

> Traditionally, why has it been better to serve site assets from multiple domains?

Because request is costly, so modern browsers establish 6 to 8 reusable connections to improve the download speed to prevent repeatly requsting, and because there is a limited connection count for each domain, we put resources on multiple domains.

> Do your best to describe the process from the time you type in a website's URL to it finishing loading on your screen.

Assume there is only HTTP request, no proxies:

1. Browser look up cache, if document exists in cache and not expires, then renders it.
2. OS makes DNS lookup of the IP address of given URL and replies to browser.
3. If that URL exists, browser opens a TCP connection to server.
4. Browser sends HTTP request to server.
5. Server parses HTTP request, sends it and may or may not close TCP connection(depends on `keep-alive` in header setting).
6. Browser parses HTTP status code and depends on what code it is to decide what to render.

> What are the differences between Long-Polling, Websockets and Server-Sent Events?

Long-Polling - use `setTimeout` and `AJAX` technique to repeatedly send request and wait for server response. Before websockets, web developer used to use this technique to simulate persistent connection.

Websocket - a real persistent connection via TCP. Unlike long-polling have to setup HTTP connection with constantly requesting, once establishing websocket you don't have to send another request to maintain the connection.

Server-sent - also called server push technique. When it comes to data exchange, servers are always passive, but with server push, server can be active too. Server push is like broadcast, whoever connects to the server is a subscriber. Server push can push desired event on specific channel.

> Explain the following request and response headers:
> Diff. between Expires, Date, Age and If-Modified-...
> Do Not Track
> Cache-Control
> Transfer-Encoding
> ETag
> X-Frame-Options

* Expires - will be labeled stale after specified date.
* Date - The date and time that message was sent.
* Age - The age that object has lived in seconds.
* If-Modified-Since - will return 304 unchanged status code.
* Transfer-encoding - In normal circumstance server will tell client the content length which server sent, but in some circumstance the content's length remains unknown, and server will send content in chunks. Specifying Transfer-encoding tells client server is sending in this way.
* ETag - an hash which is used to identify files's version.
* X-Frame-Options - clickjacking protection.
* Cache-Control - an attribute tells browser how

> What are HTTP actions? List all HTTP actions that you know, and explain them.

* GET - request a resource.
* POST - create or update an resource.
* PUT - update an existing resource.
* DELETE - delete resource.
* PATCH - partially modification to an resource.
* TRACE - echoes the received request so you can see information that is changed by intermediated servers.
* HEAD - request a response header identical to GET method. It's used to get meta information without sending the whole content.
* OPTIONS - returns what method target server supports for a specified URL.
* CONNECT - converts a request connection to as transparent TCP/IP.
