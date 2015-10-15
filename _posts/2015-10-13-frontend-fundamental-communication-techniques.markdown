---
layout: post
title: "Frontend Fundamental - Communication Techniques"
date: 2015-10-13T18:38:11+08:00
---

Polling, long-polling, websocket and blah blah blah, these tech buzz words have existed for many years, and the shameful thing is, as an frontend engineer(at least I think I am), I can't even explain to someone else what the heck are them clearly, and that's why I come up and write it down.

I will use Nodejs as backend with below demonstrations.

# Polling
The most common technique when a client wants to fetch data from server.

Client establishes a connection between server, sends request, server responds and closes this connection.

```js title:"server.js"
var http = require("http");
var fs = require("fs");

var index = null;

fs.readFile("./index.html", function(err, file){
  if(err){
    throw err;
  }
  index = file.toString();
});

http.createServer(function(req, res){
  if(req.url === "/"){
    res.writeHeader(200, {
      "content-type": "text/html"
    });
    res.end(index);
  }
  else if(req.url === "/polling"){
    res.writeHeader(200, {
      "content-type": "text/plain"
    });
    res.end("polling~");
  }
}).listen(3000);

console.log("server is running...");
```

```html title:"index.html"
<!DOCTYPE html>
<html>
  <head>
    <title>index</title>
  </head>
  <body>
    <div id="text"></div>
    <script>
      var text = document.getElementById("text");
      var ajax = new XMLHttpRequest();
      ajax.addEventListener("readystatechange", function(){
        if(ajax.readyState === 4){
          if(ajax.status === 200){
            text.appendChild(document.createTextNode(ajax.responseText));
          }
        }
      });
      ajax.open("GET", "http://localhost:3000/polling");
      ajax.send();
    </script>
  </body>
</html>
```

![polling](/images/ct/polling.png)

# Long-Polling
In some applications like stock market, online games and friends status, you need to get **realtime** data from server. Before Websocket was born, web programmers usually use long-polling ajax technique to simulate realtime data exchange.

The difference between traditional polling and long-polling is that long-polling sends a request, and **waits** until server responds and closes connection and finally, opens another one. When repeatedly doing this, we call it **keep-alive** connection.

The Pros are:
* Server side code remains unmodified in most case. Only need to change client side code.
* Our data updates in a fancy manner, which leads our customers happy, profit!

The Cons are:
* Opening and closing connections repeatedly means unnecessary bandwidth cost.
* Client sometimes gets unexpected results because long-polling fires requests regularly on a fixed time, however server might respond in longer or shorter time than what client expected.

Let's add following codes to simulate time of processing.
```js title:"server.js"
...
else if(req.url === "/polling"){
  setTimeout(function(){
    res.writeHeader(200, {
      "content-type": "text/plain"
    });
    res.end("polling~");
  }, 2500);
}
...
```

```html title:"index.html"
<!DOCTYPE html>
<html>
  <head>
    <title>index</title>
  </head>
  <body>
    <div id="text"></div>
    <script>
      var text = document.getElementById("text");

      (function longPolling(){
        setTimeout(function(){
          var ajax = new XMLHttpRequest();
          ajax.addEventListener("readystatechange", function(){
            if(ajax.readyState === 4){
              if(ajax.status === 200){
                text.appendChild(document.createTextNode(ajax.responseText));
                longPolling();
              }
            }
          });
          ajax.open("GET", "http://localhost:3000/polling");
          ajax.send();
        }, 3000);
      })();
    </script>
  </body>
</html>
```

![long-polling](/images/ct/long-polling.png)

# Websocket
Luckily, we don't need to simulate realtime anymore (unless you need to support old browsers) because now we get the **real realtime** technique with Websocket!

Websocket lets client and server to communicate with each other asynchronously, which means, client and server can send and get data at the same time without waiting for each other (non-blocking).

Modern browsers (not you IE8 and IE9!) this day all support native Websocket, but for the cross platform compatibility, many people use 3rd party libraries to get rid of handling compatibility themselves.

In this example I use `socket.io` as our Websocket helper: `npm install socket.io`

```js title:"server.js"
var app = require("http").createServer(handler);
var io = require("socket.io")(app);
var fs = require("fs");

app.listen(3000);

function handler(req, res){
  fs.readFile(__dirname + "/index.html", function(err, file){
    if(err){
      res.writeHeader(500, {
        "content-type": "text/plain"
      });
      res.end(filename + " : not found");
    }

    res.writeHeader(200, {
      "content-type": "text.html"
    });
    res.end(file);
  });
}

io.on("connection", function(socket){
  socket.emit("server_says", "Hello Client");
  socket.on("client_says", function(data){
    console.log(data);
  });
});

console.log("server is running...");
```

```html title:"index.html"
<!DOCTYPE html>
<html>
  <head>
    <title>index</title>
    <script src="https://cdn.socket.io/socket.io-1.3.7.js"></script>
  </head>
  <body>
    <div id="text"></div>
    <button id="button">Hello Server!</button>
    <script>
      var text = document.getElementById("text");
      var button = document.getElementById("button");
      var socket = io("http://localhost:3000");
      socket.on("server_says", function(data){
        text.appendChild(document.createTextNode(data));
      });

      button.addEventListener("click", function(e){
        socket.emit("client_says", "Hello Server!");
      });
    </script>
  </body>
</html>
```

As you can see in Chrome developer's network tool, websocket helps us maintain a persistent connection.
![websocket client](/images/ct/websocket-client.png)

![websocket server](/images/ct/websocket-server.png)

# WebRTC
Unlike Websocket's client-to-server structure, Web Real-Time Communication is peer-to-peer structure which allows browsers to exchange data like video, audio with each other directly. Browser-to-browser, in this fashion, data exchange is more efficient.

So, regarding the tag of "browser-to-browser", that means we don't need a server, right? Sadly, you do, but server here merely acts as a connector, not a proxy, as a result, data won't be passed through server.

WebRTC currently is not being widely supported by all browsers. See [here](http://caniuse.com/#feat=rtcpeerconnection).

Here I use [peerjs](http://peerjs.com/) as 3rd party helper to overcome cross browser compatibility.

`npm install peer` for our server.

`npm install peerjs` for client side, but here I use direct link instead:

`<script src="http://cdn.rawgit.com/peers/peerjs/master/dist/peer.js"></script>`

```js title:"server.js"
var PeerServer = require('peer').PeerServer;
var server = PeerServer({port: 3000, path: "/"});

console.log("server is running...");
```

```html title:"index.html"
<!DOCTYPE html>
<html>
  <head>
    <title>index</title>
    <script src="http://cdn.rawgit.com/peers/peerjs/master/dist/peer.js"></script>
  </head>
  <body>
    <div id="output"></div>
    <script>
      var output = document.getElementById("output");
      var peer1 = new Peer("Mike", {host: "localhost", port: 3000, path: "/"});
      var peer2 = new Peer("Bob", {host: "localhost", port: 3000, path: "/"});

      peer1.on("open", function(){
        appendMessage(peer1.id + " is ready to connect with others.");
      });

      peer1.on("connection", function(c){
        c.on("open", function(){
          c.send("Hello " + c.peer);
        });
        c.on("data", function(data){
          appendMessage(data);
        });
      });

      var c = peer2.connect(peer1.id);
      c.on("data", function(data){
        appendMessage(data);
      });

      setTimeout(function(){
        c.send("Hello " + c.peer);
      }, 1000);

      function appendMessage(message){
        var p = document.createElement("p");
        p.appendChild(document.createTextNode(message));
        output.appendChild(p);
      }
    </script>
  </body>
</html>
```

![webRTC result](/images/ct/webrtc-result.png)

# Server Push
Unlike Websocket which opens a duplex connection for client and server, server push (aka server sent) only opens a unidirectional connection for client and server. Server, as an message sender, only push message to client. Client, as a message receiver, only accepts message from server.

Since there is a Websocket for duplex connection, why would someone only want one-way connection? In come scenarios like news feeds or friends notifications, client requests only once for page, and waits for following responses from server.

```js title:"server.js"
var http = require("http");
var fs = require("fs");

http.createServer(function(req, res){
  if(req.headers.accept === "text/event-stream"){
    if(req.url === "/events"){
      res.writeHeader(200, {
        "content-type": "text/event-stream"
      });
      setInterval(function(){
        res.write("data: Hello Server Push!\n\n");
      }, 2000);
    }
  }
  else {
    res.writeHeader(200, { "content-type": "text/html"});
    res.write(fs.readFileSync(__dirname + "/index.html"));
    res.end();
  }
}).listen(3000);

console.log("server is running...");
```

```html title:"index.html"
<!DOCTYPE html>
<html>
  <head>
    <title>index</title>
  </head>
  <body>
    <div id="output"></div>
    <script>
      var output = document.getElementById("output");
      var es = new EventSource("/events");

      es.addEventListener("message", function(e){
        appendMessage(e.data);
      });

      function appendMessage(message){
        var p = document.createElement("p");
        p.appendChild(document.createTextNode(message));
        output.appendChild(p);
      }
    </script>
  </body>
</html>
```

![server push](/images/ct/server-push.png)

# References

* [stackoverflow: websocket vs long-polling vs webrtc vs server sent events](http://stackoverflow.com/questions/10028770/html5-websocket-vs-long-polling-vs-ajax-vs-webrtc-vs-server-sent-events)
* [simple long-polling with javascript and jquery](http://techoctave.com/c7/posts/60-simple-long-polling-example-with-javascript-and-jquery/)
* [stackoverflow: what are websockets, sse and comet?](http://stackoverflow.com/questions/11077857/what-are-long-polling-websockets-server-sent-events-sse-and-comet?lq=1)
* [finding the right nodejs websocket implementation](https://medium.com/@denizozger/finding-the-right-node-js-websocket-implementation-b63bfca0539)
* [MDN: writing websocket client applications](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_client_applications)
* [webRTC on wikipedia](https://en.wikipedia.org/wiki/WebRTC)
* [what is webrtc and how does it work](http://www.innoarchitech.com/what-is-webrtc-and-how-does-it-work/)
* [peejs.com](http://peerjs.com/)
* [Stream Updates with SSE](http://www.html5rocks.com/en/tutorials/eventsource/basics/?redirect_from_locale=tw)
