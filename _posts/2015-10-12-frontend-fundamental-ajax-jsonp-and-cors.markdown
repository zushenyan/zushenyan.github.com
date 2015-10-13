---
layout: post
title: "Frontend Fundamental - AJAX, JSONP and CORS"
date: 2015-10-12T23:42:01+08:00
---

Sometimes frontend noob like me will run into Same-Origin-Policy when deal with Ajax things. This article is a note to myself to get clear of what is it and how to deal with it when encounter.

# Same-Origin-Policy
Same-Origin-Policy is a concept of preventing malicious script from getting sensitive data through [CCRF](Cross-site request forgery) and [XSS](https://en.wikipedia.org/wiki/Cross-site_scripting). Under this policy, browser permits script in one page to access data in the other only if they are under the same domain.

Since SOP forbids this way, then why do we still need to access data from other domain? The reason is that in web development, sometimes we store resources like html, css and js on different server/domain in order to reduce the burden of server. Another reason is that today's web  is RESTful, which means we operate another server from different domain in browser with public API they provide.

# AJAX
One way to access cross domain resource without violating SOP is to use current server which sends pages to our browser as a proxy.

By doing this, fetching cross domain resources is our server's job now since **SOP only works on client side, not server side**.

> client's request <---> our server as proxy <---> another server

# JSONP
SOP restricts where we can request resources we want, however, there are still exceptions.

These exceptions are `script`, `img`, `link` tags.

According to [the draft of origin](http://tools.ietf.org/html/draft-abarth-principles-of-origin-00) (or [this question](http://stackoverflow.com/questions/10530554/why-is-the-html-script-tag-not-subject-to-the-same-origin-policy) from stackoverflow if you are lazy like me), it says that because not wanting to make web development a painful thing, they decided not to treat every page as an independent origin, and thus they made some exceptions.

The spirit of this approach is that you still need the server holding resources you want implements a way to provide this service. We will see how to do this with following code. Let's code!

```js title:"server 1"
var http = require("http");
var fs = require("fs");

var html = null;

fs.readFile("jsonp.html", function(err, file){
  if(err){
    throw err;
  }
  html = file.toString();
});

http.createServer(function(req, res){
  res.writeHeader(200, {
    "content-type": "text/html"
  });
  res.end(html);
}).listen(3000);

console.log("domain 1 is running...");
```

```js title:"server 2"
var http = require("http");

http.createServer(function(req, res){
  res.writeHeader(200, {
    "content-type": "text/plain"
  });
  res.end("Hello JSONP!");
}).listen(3001);

console.log("domain 2 is running...");
```

```HTML title:"jsonp.html"
<!DOCTYPE html>
<html>
  <head>
    <title>jsonp test</title>
  </head>
  <body>
    <div id="text"></div>
    <button id="button">ajax!</button>
    <script>
      function contentLoaded(){
        var text = document.getElementById("text");
        var button = document.getElementById("button");
        button.addEventListener("click", function(e){
          var ajax = new XMLHttpRequest();
          ajax.addEventListener("readystatechange", function(){
            if(ajax.readyState === 4){
              if(ajax.status === 200){
                text.appendChild(document.createTextNode(ajax.responseText));
              }
            }
          });
          ajax.open("GET", "http://localhost:3001");
          ajax.send();
        });
      }

      window.addEventListener("DOMContentLoaded", contentLoaded);
    </script>
  </body>
</html>
```

```plain title:"Result in Chrome console"
XMLHttpRequest cannot load http://localhost:3001/. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost:3000' is therefore not allowed access.
```

Well, of course it doesn't work since it violates SOP. Let's correct this.

```js title:"server 2"
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

console.log("domain 2 is running...");
```

```html title:"jsonp.html"
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

When you click `jsonp!` button `text` div should be appened with `Hello JSONP!` text.

# CORS
Cross-Origin-Resource-Sharing, CORS, is a standard mechanism that let you access cross-domain resources with standard ajax request. You don't need those sorcery ways to do the magic anymore.

By simply enabling the following header in server 2:

`Access-Control-Allow-Origin: http://localhost:3000`

Visit [here](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) if you want more headers for customization.

```js title:"server 2"
var http = require("http");

http.createServer(function(req, res){
  res.writeHeader(200, {
    "content-type": "text/plain",
    "access-control-allow-origin": "http://localhost:3000"
  });
  res.end("Hello CORS!");
}).listen(3001);

console.log("domain 2 is running...");
```

```js title:"index"
<!DOCTYPE html>
<html>
  <head>
    <title>CORS test</title>
  </head>
  <body>
    <div id="text"></div>
    <button id="button">CORS rocks!</button>
    <script>
    function contentLoaded(){
      var text = document.getElementById("text");
      var button = document.getElementById("button");
      button.addEventListener("click", function(e){
        var ajax = new XMLHttpRequest();
        ajax.addEventListener("readystatechange", function(){
          if(ajax.readyState === 4){
            if(ajax.status === 200){
              text.appendChild(document.createTextNode(ajax.responseText));
            }
          }
        });
        ajax.open("GET", "http://localhost:3001");
        ajax.send();
      });
    }
    window.addEventListener("DOMContentLoaded", contentLoaded);
    </script>
  </body>
</html>
```

We are now be able to acess cross-domain resources in standard ajax way without any errors, weeeeeeeeee!

# References

* [Same-Origin-Policy on Wikipedia](https://en.wikipedia.org/wiki/Same-origin_policy)
* [Cross-site request forgery](https://en.wikipedia.org/wiki/Cross-site_request_forgery)
* [Cross-site scripting](https://en.wikipedia.org/wiki/Cross-site_scripting)
* [JSON Vs JSONP Vs CORS](http://stackoverflow.com/questions/15136602/json-vs-jsonp-vs-cors)
* [usamadar.com: SOP, JSONP and CORS](http://usamadar.com/2012/06/24/getting-around-browsers-same-origin-policy-sop-with-proxies-script-injection-jsonp-and-cors/)
* [Why is the HTML SCRIPT tag not subject to the same origin policy](http://stackoverflow.com/questions/10530554/why-is-the-html-script-tag-not-subject-to-the-same-origin-policy)
* [Principles of the Same-Origin Policy](http://tools.ietf.org/html/draft-abarth-principles-of-origin-00)

[Same-Origin-Policy on Wikipedia]: https://en.wikipedia.org/wiki/Same-origin_policy
[Cross-site request forgery]: https://en.wikipedia.org/wiki/Cross-site_request_forgery
[Cross-site scripting]: https://en.wikipedia.org/wiki/Cross-site_scripting
[JSON Vs JSONP Vs CORS]: http://stackoverflow.com/questions/15136602/json-vs-jsonp-vs-cors
[usamadar.com: SOP, JSONP and CORS]: http://usamadar.com/2012/06/24/getting-around-browsers-same-origin-policy-sop-with-proxies-script-injection-jsonp-and-cors/
[Why is the HTML SCRIPT tag not subject to the same origin policy]: http://stackoverflow.com/questions/10530554/why-is-the-html-script-tag-not-subject-to-the-same-origin-policy
[Principles of the Same-Origin Policy]: http://tools.ietf.org/html/draft-abarth-principles-of-origin-00
[CORS on wikipedia]: https://en.wikipedia.org/wiki/Cross-origin_resource_sharing
