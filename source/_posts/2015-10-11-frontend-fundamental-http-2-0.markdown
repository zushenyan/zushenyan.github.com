---
layout: post
title: "Frontend Fundamental - HTTP 2.0"
date: 2015-10-11T10:48:37+08:00
tags: HTTP/HTTPS
---

# What is HTTP 2.0?
Since HTTP protocol was released, Web world has developed rapidly, and compared to the time in which HTTP was born, today's Web content is way more richer, and due to the heavy request loading Web has evolved, HTTP 1.1 is not capable sooner or later. In order to handle the job, HTTP have to be updated.

HTTP 2.0 is based on SPDY(pronounced speedy) which is an experiment networking protocol developed by Google. SPDY aims to improve performance and security. In Feb. 2015, Google announced that SPDY will soon be deprecated since there is already a standard - HTTP 2.0.

# HTTP 2.0 Features (differences to HTTP 1.1)
* Communicate in binary, not textual.
* Multiplex
* Header compression
* Server push

#### Binary Communication
In HTTP 1.1 textual way, there are [4 ways to parse message](http://www.w3.org/Protocols/rfc2616/rfc2616-sec4.html#sec4.4) when in HTTP 2 there is only one way to parse, and hence its more efficient.

#### Multiplex
With HTTP 1.1, Most browsers create 6 to 8 connections to download resources from server in parallel; In HTTP 2.0, only one connection is needed. Not like HTTP 1.1, HTTP 2.0 is non-blocking. Client can send requests at once and get responses out of order, while HTTP 1.1 can only send request by request in a connection. It's like a car driving through a tunnel, but only one at a time.

#### Header compression
Even though there are already performance optimizations to compress our content, say **image sprite, in-lining, resource bundle**, headers still take space(say cookies, referer...), and for environment like mobile with limited bandwidth, it's more crucial. That's why we need header compression.

#### Server Push
Server can send response without any request from client.

Traditional scenario:

> Client: Hey server, I want index.html!
>
> Server: (Gives index.html)
>
> Client: Hey server, I want style.css!
>
> Server: (Gives style.css)
>
> Client: Thanks!

With server push:

> Client: Hey server, I want index.html!
>
> Server: (Gives index.html, style.css and lots of resources client didn't ask for but do need.)
>
> Client: Err...these are what I want! thanks!

# Platforms Support
[Can I use http2?](http://caniuse.com/#search=http2)

Currently, except for some mobile browsers and old IE, most modern browsers support HTTP 2.0.

# HTTP 2.0 In Practice
First install `node-http2` for Nodejs.

`npm install node-http2`

And from the [HTTP/2 FAQ](HTTP/2 Frequently Asked Questions), it says:

> # Does HTTP/2 require encryption?
> No. After extensive discussion, the Working Group did not have consensus to require the use of encryption (e.g., TLS) for the new protocol.
>
> However, some implementations have stated that they will only support HTTP/2 when it is used over an encrypted connection, and currently no browser supports HTTP/2 unencrypted.

So prepare your certificate and private key, we still need them.

Server:
```js
var http2 = require("http2");
var fs = require("fs");

var options = {
  key: fs.readFileSync("./server.key"),
  cert: fs.readFileSync("./server.cert")
};

http2.createServer(options, function(req, res){
  res.writeHead(200, null, {
    "content-type": "text/plain"
  });
  res.end("we are using http2 weeeeeeeee");
}).listen(3000);

console.log("Server is running...");
```
Run it in `https` protocol with Chrome

![http2](/images/http2.png)

In the protocol field, "h2" means we are using HTTP2 protocol.

# References
* [wikipedia-spdy](wikipedia-spdy)
* [HTTP/2 Frequently Asked Questions](HTTP/2 Frequently Asked Questions)
* [http2.akamai.com/](http2.akamai.com/)
* [http2-demo](http2-demo)
* [max-parallel-http-connections-in-a-browser](max-parallel-http-connections-in-a-browser)
* [http2-and-java-current-status](http2-and-java-current-status)
* [innovating-with-http-2.0-server-push](innovating-with-http-2.0-server-push)
* [http2-is-minifying-js-concatenate-js-css-and-using-spritewith-spdys(with-spdy)-no-longer-needed](http://stackoverflow.com/questions/28630108/http2-is-minifying-js-concatenate-js-css-and-using-sprites-no-longer-needed)
* [difference-between-http-pipeling-and-http-multiplexing-with-spdy](difference-between-http-pipeling-and-http-multiplexing-with-spdy)

[wikipedia-spdy]: https://en.wikipedia.org/wiki/SPDY
[HTTP/2 Frequently Asked Questions]: http://http2.github.io/faq/
[max-parallel-http-connections-in-a-browser]: http://stackoverflow.com/questions/985431/max-parallel-http-connections-in-a-browser
[http2.akamai.com/]: https://http2.akamai.com/
[http2-demo]: https://http2.akamai.com/demo
[http2-and-java-current-status]: http://www.slideshare.net/SimoneBordet/http2-and-java-current-status
[innovating-with-http-2.0-server-push]: https://www.igvita.com/2013/06/12/innovating-with-http-2.0-server-push/
[http2-is-minifying-js-concatenate-js-css-and-using-spritewith-spdys(with-spdy)-no-longer-needed]: http://stackoverflow.com/questions/28630108/http2-is-minifying-js-concatenate-js-css-and-using-spritewith-spdys(with-spdy)-no-longer-needed
[difference-between-http-pipeling-and-http-multiplexing-with-spdy]: http://stackoverflow.com/questions/10480122/difference-between-http-pipeling-and-http-multiplexing-with-spdy
