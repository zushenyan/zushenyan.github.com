---
layout: post
title: "Frontend Fundamental - HTTP 1.1"
date: 2015-10-10T12:37:59+08:00
---

# Purpose
A study note for myself.

# Tools I Use
* Nodejs
  * I will use Nodejs as server side foundation. No expressjs or some other fancy libraries here. Only vanilla Nodejs you need to know.
* Curl
  * A powerful command line tool to transfer data from or to server. It's builtin if users are using Mac or Linux OS.
* Chrome
  * A happy browser.

# Introduction
HTTP protocol is a fundamental when it comes to nowadays web. No matter you are an frontend or backend guy, knowing it is surely an important thing.
In this article, I will cover topics from basic features, headers to useful practices.

# HTTP Architecture
In version 1.0, it was **multiple connection**, which means it opens a new connection per request/response. To solve the overhead of establishing connection for each request/response, version 1.1 comes with **persistent connection**, which means only uses a single connection for subsequent request/response to solve the establishing overhead.

![from wikipedia](https://upload.wikimedia.org/wikipedia/commons/d/d5/HTTP_persistent_connection.svg)

Image from [wikipedia](http-persistent-connection).

# HTTP Basic Features
* Media independent - Any types can be exchanged between client and server as long as they know what types each other are sending to.
* Stateless - Server won't record anything about each other, like how long the connection last and blah blah blah... However you can write extra codes to record things.

# HTTP Status Codes
When server responses, it returns a 3-digit number back which tells client some kind of informations. Here I only listed partial content. For full list of status code check [here](http-status-code).

* 1xx - Pure information to inform client.
  * 100 - Continue.
	* 101 - Switch protocol.
* 2xx - Success.
  * 200 - Ok.
	* 201 - Resource is created or request is completed.
* 3xx - Redirection.
  * 301 - Moved permanently. Request URI has moved to an new URL.
	* 307 - Moved temporary.Request URI has temporary moved to an new URL.
* 4xx - Client error.
  * 403 - Forbidden. Requested page was forbidden to access.
	* 404 - Not found.
* 5xx - Server error.
  * 500 - Internal error. Server met unexpected problem.
	* 503 - Service unavailable. Server temporary down.

# HTTP Methods
HTTP defines methods to indicate what action should be performed on server side.

* GET			- Retrieves data from server with given URI.
* POST		- Sends data to server. For example, sending forms, upload files.
* PUT			- Creates(if doesn't exist) or updates target resource with new uploaded one.
* DELETE	- Removes target resource with given URI.
* HEAD		- Identical to GET, but only interested in head content.
* CONNECT - Converts request into a TCP/IP tunnel.
* OPTIONS - Describes communication options with server.
* TRACE		- Echoes received request so that client can see if there are any modification from intermediate servers.
* PATCH		- Updates partial modification to a resource.

Here are some examples:

#### GET
Our server looks like:
``` js
var http = require("http");
http.createServer(function(request, response){
  response.writeHeader(200, {"Content-Type": "text/plain"});
  response.end("Hello World!");
}).listen(3000);
console.log("server is running...");
```
With `curl -v http://localhost:3000`, you will get:
``` plain
* Rebuilt URL to: http://localhost:3000/
*   Trying ::1...
* Connected to localhost (::1) port 3000 (#0)
> GET / HTTP/1.1
> Host: localhost:3000
> User-Agent: curl/7.43.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Content-Type: text/plain
< Date: Sat, 10 Oct 2015 07:44:31 GMT
< Connection: keep-alive
< Transfer-Encoding: chunked
<
* Connection #0 to host localhost left intact
Hello World!%
```

#### POST
Server:
``` js
var http = require("http");
http.createServer(function(request, response){
  request.on("data", function(chunks){
    console.log(chunks.toString());
    response.end();
  });
}).listen(3000);
console.log("server is running...");
```
With `curl -X POST -d @style.css -v http://localhost:3000`, server side console prints:
``` plain
server is running...
div {	color: red;}
```
Our style.css on disk looks like:
``` css
div {
  color: red;
}
```

# HTTP Headers
There are tons of headers http protocol has. Here only list some popular headers. For full list checkout [here](header-list):

In general, there are three types of header:

* General header - Works with both request and response.
  * Connection				- Indicates what type of method should be used for communication.
  * Date							- The time this message was sent.
  * Cache-Control 		- Indicates the conditions to cache or not, which I will cover in practice later.
  * Upgrade 					- Tells what additional protocols it supports and would like to switch if client/server available these protocols.
* Request header - only works with request.
  * Accept						- What Content-Type it accepts.
  * Accept-Encoding		- What Content-Encoding it accepts.
  * Cookie						- By using cookie header field, client can send cookie to server.
  * User-Agent				- Client's identity. For example, Mozilla/4.0, IE/10.0...
* Response header - only works with response.
  * Content-Encoding	- Tells what type of encoding has been applied to the entity body([explanation](entity-message-1)).
  * Etag							- Used to tag version or identity for a file.
  * Expires						- Gives a date after which the response is considered stale.

# HTTP Headers in Practice

#### Content-Encoding
Content-Encoding is often used in sending files with minimal size as possible. Imagine a scenario you have minified all of your files - your html, css and js. All things look great, right? But actually, you can take it further by enabling Content-Encoding with gzip.

Server:
``` js
var fs = require("fs");
var http = require("http");
var zlib = require("zlib");

var page = null;

fs.readFile("./index.html", function(err, file){
  if(err){
    throw err;
  }
  page = file.toString();
});

var header = {
  "Content-Type": "text/html",
  "Content-Encoding": "gzip",
};

http.createServer(function(request, response){
  zlib.gzip(page, function(err, buff){
    response.writeHeader(200, header);
    response.end(buff);
  });
}).listen(3000);
console.log("server is running...");
```
Send request by using Chrome

![content-encoding](/images/content-encoding.png)

As you can see, the file size has reduced from 3.7kb to 2.0kb, which is roughly 50% size reduction.

#### Cache-Control
Server:
```js
var fs = require("fs");
var http = require("http");

var html = null;
var css = null;

var htmlHeader = {
  "Content-Type": "text/html",
  "Cache-Control": "private, max-age=8"
};

var cssHeader = Object.assign({}, htmlHeader);
cssHeader["Content-Type"] = "text/css";

fs.readFile("./index.html", function(err, file){
  if(err){
    throw err;
  }
  html = file.toString();
});

fs.readFile("./style.css", function(err, file){
  if(err){
    throw err;
  }
  css = file.toString();
});

http.createServer(function(request, response){
  if(request.url === "/"){
    response.writeHeader(200, htmlHeader);
    response.end(html);
  }
  else if(request.url === "/style.css"){
    response.writeHeader(200, cssHeader);
    response.end(css);
  }
  else {
    response.writeHeader(404);
    response.end();
  }
}).listen(3000);

console.log("server is running...");
```
Header field `Cache-Control: private, max-age=8` means only cacheable on client machine and cache it for 8 seconds.

![cache-control](/images/cache-control.png)

Our css file is cached thanks to `Cache-Control`.

Note: By clicking refresh icon, Chrome will send a request with header `Cache-Control: max-age=0` which will force Chrome itself to refetch files it requests. If you want to see cache behaviors, press enter in the URL search bar instead.

# http-equiv
Not everyone is able to reach server configuration. In order to access request header, a typical usage in frontend is via `http-equiv`
```html
<meta http-equiv="refresh" content="2">
```
With `http-equiv`, you can do almost what you can do as on the server side.

# References
* [Wikipedia](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Status_codes)
* [TutorialPoint](http://www.tutorialspoint.com/http/index.htm)
* [MDN Http protocol](https://developer.mozilla.org/en-US/docs/Web/HTTP)
* [html5rocks content security policy](http://www.html5rocks.com/en/tutorials/security/content-security-policy/)
* [Entity body and message body question 1](entity-message-1)
* [Entity body and message body question 2](entity-message-2)

[entity-message-1]: http://stackoverflow.com/questions/13808619/confusion-about-entities-as-used-in-the-http-specification
[entity-message-2]: http://stackoverflow.com/questions/2273837/which-one-is-the-message-and-which-one-the-entity-in-http-terminology
[http-status-code]: https://en.wikipedia.org/wiki/List_of_HTTP_status_codes
[http-persistent-connection]: https://en.wikipedia.org/wiki/HTTP_persistent_connection
[header-list]: https://en.wikipedia.org/wiki/List_of_HTTP_header_fields
