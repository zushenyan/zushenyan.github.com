---
layout: post
title: "Backend Fundamental - Cookie and Session"
date: 2015-11-20T22:35:27+08:00
tags: [backend, web]
---

# Yet Another Cookie & Session Explanation?
Currently I am working on a website with user account written with Node. I did have some backend experience though, but it was around 2 years ago. Grabbing memories back needs some effort.

To engrave these concepts into my brain more deeper, I have to write it down, and this time, it will go advance rather than simply explaining "Cookie is this and Session is that. Done!"

# Why Use Cookie & Session?
HTTP is a stateless protocol, which means it forgets anything about information it carried last time. In order to maintain a persistent communication between client and server, cookie and session were invented.

# Cookie
Cookie is a traditional way to save **non-sensitive** user informations in browsers. To sum up the feature of Cookie:

* Cookie is a key-value set.
* Cookie is sent via HTTP.
* Cookie is an attribute of HTTP header.
* Every cookie has its own attributes:
  * Domain - tells the browser which domain it belongs to. Usually this should be current resource's top domain. If you set it to `.com`, every sub-domain of `.com` like `google.com` will be able to access this cookie.
  * Path - almost identical to the `domain` attribute, but only relative to current resource's top domain. This tells the browser when to send this cookie on what path.
  * Expires - tells the browser to delete this cookie on a specified date.
  * Max-Age - tells the browser to delete this cookie on a specified time relative to now in second.
  * Secure - can only be sent via HTTPS or not. Make it on for more serious security concern.
  * HttpOnly - should be allowed to access by JavaScript or not. Normally it's on to prevent from Cross-Site-Script attack.
* Can only support up to 4096 bytes in size.

When I mention about **non-sensitive** informations I mean:

* Session ID - most websites nowadays no longer store "shopping cart" or anything user-sensitive in the cookie. Instead, They store an unique ID generated by the server for looking up corresponding user at server side database.
* Personal Preferences - the background user like, the font size user preferred...etc.
* Browsing Habits - remember login, the last page user visited, the browsing histories of one user...etc.

The reason why cookie should never store sensitive data like username, password or credit card number is that cookies are more easily to be intercepted or tampered. Though you can send cookies by **https** or using **signed cookie**(which will be introduced latter) to store hashed data, exposing more informations means lower the cost and difficulty of cracking, and thus the best way is don't even store sensitive data to the cookie.

Typical ways to set cookie in Node:

```js
var express = require("express");

var app = express();

app.get("/", function(req, res, next){
  var rawCookie = req.get("cookie");
  var cookie = {count: 1};
  if(rawCookie){
    cookie.count = parseInt(rawCookie.split("=")[1]);
    cookie.count++;
  }
  res.append("set-cookie", "count=" + cookie.count + ";max-age=5");
  res.send("count: " + cookie.count);
});

app.listen(3000);
```

This will increase `count` by one when user visits `localhost:3000` in 5 seconds. If user doesn't visit again in 5 seconds, set count to 1.

Accessing to cookie is such a pain, right? `cookie-parse` to the rescue!

`npm install cookie-parse` and then...

```js
var express = require("express");
var cookieParser = require("cookie-parser");

var app = express();

app.use(cookieParser());

app.get("/", function(req, res, next){
  var count = parseInt(req.cookies.count, 10);
  count = count ? count + 1 : 1;
  res.cookie("count", count.toString(), {maxAge: 5000});
  res.send("count: " + count);
});

app.listen(3000);
```

Tada! I am sure there are something like `cookie-parser` which exists in other languages.

# Common Variation Of Cookies

#### Session Cookie
Session cookie is contrary to persistent cookie(or just called it cookie) and only lives when user is still browsing the website. When user closes the browser, the session cookie is deleted by the browser as well.

A common usage of session cookie is storing session ID. Session ID is an unique ID generated by the server and was stored when connection is established. Session ID is used for letting the server know who the current user is in order to look up relative informations in the database.

Setting a session cookie is simple - just leave `expires` and `maxAge` attributes empty.

Use `npm install express-session` for saving time of building another wheel.

```js
var express = require("express");

var app = express();

app.use(require("express-session")({
  secret: "i am a secret",
  resave: false,
  saveUninitialized: true
}));

app.get("/", function(req, res, next){
  var count = req.session.count;
  count = count ? count + 1 : 1;
  req.session.count = count;
  res.send("count: " + req.session.count);
});

app.listen(3000);
```

By using `express-session`, a cookie named `connect.sid` will be stored with a **hashed value plus a signature aside** in the browser.

As for session ID security topic I'd recommend [here](http://security.stackexchange.com/questions/92122/why-is-it-insecure-to-store-the-session-id-in-a-cookie-directly) for further reading.

#### Signed Cookie
In web development world, you will accidentally bump into a word - **Signed Cookie**. What is it? Basically, a signed cookie stores a value by appending a signature after it. According to frameworks, they claimed that by singing values, server can determine if this value is tampered, however there are some debates on this topic:

* [Why is it insecure to store the session ID in a cookie directly?](http://security.stackexchange.com/questions/92122/why-is-it-insecure-to-store-the-session-id-in-a-cookie-directly)
* [Why sign the cookie?](https://github.com/expressjs/session/issues/68)
* [Why doesn't this module store the raw session ID in the cookie directly?](https://github.com/expressjs/session/issues/176)
* [Signed session cookies. A good idea?](http://stackoverflow.com/questions/3240246/signed-session-cookies-a-good-idea)

I am not an expert at security so can't do any assertion. I will just leave it here.

```js
var express = require("express");
var cookieParser = require("cookie-parser");

var app = express();

app.use(cookieParser("write down your secret here"));

app.get("/", function(req, res, next){
  var count = parseInt(req.signedCookies.count, 10);
  count = count ? count + 1 : 1;
  res.cookie("count", count, {signed: true});
  res.send("count: " + count);
});

app.listen(3000);
```

This is a showcase of how to use signed cookie in Node with express' extension module. Signature changes whenever `count` changes.

# Session
We have dealt with storing informations on the client side, so what exactly session is? Well, session is just an description of state that when the connection between client and server is alive, session lives, when connection closes, session ends.

The main task that session runs is telling the server which one is the current client by giving each client an unique id, it's called session ID. There are several possible places to store the session ID:

#### Memory
Fast, but not practical. Let's assume you have prepared a database for storing the actual data of users, and only store session ID in memory, and use session ID to look up corresponding user informations in database, but in most cases, we expect hundreds of clients to maintain its' session between server, which means these session data will eat up some part of memory, which it's not a smart move, and plus, you will have to deal with data sharing problem like race condition in concurrent environments.

#### Cookie
A common place to store session ID. Usually used with database where actual user information lays.

#### Database
We also generate something corresponding to the session ID for indexing the actual place where user information belongs to and still have to combine forces of memory and cookie to store our session ID, why? Because indexing in database is way more time costly than indexing in memory.

Here I use [Redis](http://redis.io/) to give a quick database showcase. Install package file from Redis to benefit full support.

```js
var express = require("express");
var session = require("express-session");
var db = require("redis").createClient();

var app = express();

db.on("error", function(err, reply){
  console.log(err);
});

db.on("connect", function(err, reply){
  console.log("connected to database...");
});

app.use(session({
    secret: "such secret much safe wow",
    resave: false,
    saveUninitialized: true,
  }));

app.get("/", function(req, res, next){
  var count = 1;
  db.hget([req.session.id, "count"], function(err, reply){
    if(err){console.log(err); return;}
    if(reply){
      count = ++reply;
      db.hset([req.session.id, "count", count], logErr);
    }
    else{ db.hset([req.session.id, "count", count], logErr); }
    res.send("count: " + count);
  })
});

function logErr(err, reply){
  if(err){ console.log(err); }
}

app.listen(3000);
```

# Conclusion
Web development is all about security. When developers want to take advantages of cookie and session, some principles should be concerned:

* Cookie should not store sensitive data.
* Set cookie's `secure` attribute on if HTTPS is available.
* Set cookie's `HttpOnly` attribute on if possible to prevent man-in-the-middle from tampering.
* Use Signed Cookie technique to have signed values.
* If you still consider Signed Cookies are not secure enough, deal it like you deal with passwords.

# References
* [Wikipedia - HTTP Cookie](https://en.wikipedia.org/wiki/HTTP_cookie)
* [What information is OK to store in cookies?](http://stackoverflow.com/questions/706858/what-information-is-ok-to-store-in-cookies)
* [What are “signed” cookies in connect/expressjs?](http://stackoverflow.com/questions/11897965/what-are-signed-cookies-in-connect-expressjs)
* [Signed session cookies. A good idea?](http://stackoverflow.com/questions/3240246/signed-session-cookies-a-good-idea)
* [cookie 和 session](http://wiki.jikexueyuan.com/project/node-lessons/cookie-session.html)
