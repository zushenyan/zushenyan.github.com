---
layout: post
title: "Asynchronous Programming in JavaScript"
date: 2015-10-26T22:24:59+08:00
tags: [javascript, concurrent, meta]
---

# Purpose
Just a learning note here. There are already tons of glorious posts talking about this topic, so don't expect to see something super edge, extreme frontier technology here.

# What is asynchronous?
Before moving on, knowing what exactly is Asynchronous is essential.

Synchronous, contrary to Asynchronous, means blocking. That means computer can only run a task at one time. Before finishing this one, another will not start.

When we mention about Asynchronous, we are referring to non-blocking, which means, computer runs a tasks without "blocking" other tasks.

And don't confuse Asynchronous with words Concurrency or Parallel, they are different things.

Parallel usually refers to true multi-tasking, which means computer distributes several tasks among processors. In this way, tasks are executed in real simultaneously.

Concurrent is a concept of having a group of tasks sharing an execution thread with each other to let them run simultaneously. Asynchronous and Synchronous are just descriptions of state between tasks.

# How does it work in JavaScript?
JavaScript is an event-driven language and it uses an event-loop to get codes run in asynchronous. JavaScript is also called single-threaded language because it can only run a piece of code at a time.

So, does that mean JavaScript is really a single-threaded language? Hmm, not accurate. No matter what platform JavaScript is on, either client side(browsers) or server side(Node.js), there is actually a thread pool underneath it.

After digging here and there on the web, I realized to explicitly explain how does it work is a huge thing, and it really hurts your head. For the sake of simplicity, here is my simple version:

JavaScript on both client and server side use one thread for code execution, another one for taking care of event loop, and all of others for handling events and triggering handlers. Whenever interpreter runs into async code like `button.click()`(UI events) or `fs.readFile()` (I/O events), it pushes callbacks into an event queue, and after all things in main function are done, it pops callbacks in queue to get these executed.

And...that's it. :P

To get more illustration, visit [this awesome site](http://latentflip.com/loupe/). The website visualizes how event loop works in JavaScript in a very clear manner, and if it still can't satisfy you, scroll down to References & Resources for further reading.

# Callback
So, how do we take advantage of async? Well, the answer is pretty simple:

```js
setTimeout(function(){
  console.log("Wolrd!");
}, 1000);

console.log("Hello");
```

When you set an callback on an event, you are using async. Another common practice is using ajax:

```js
var ajax = new XMLHttpRequest();
ajax.open("GET", "http://www.example.com");
ajax.addEventListener("readystatechange", function(){
  if(ajax.readyState === 4){
    if(ajax.state === 200){
      console.log(ajax.responseText);
    }
  }
});
ajax.send();
```

Callback really fulfills our need for async, but when our program grows larger, our callback logic may become complicated as well, which results in some nasty and hard-to-maintained code.

```js
request("http://www.example.com/api/data1", function(result, status){
  if(status === "OK"){
    request("http://www.example.com/api/data2", function(result, status){
      if(status === "OK"){
        request("http://www.example.com/api/data3", function(result, status){
          console.log(result);
        });
      }
    });
  }
});
```

What worse than callback hell is that you can't even catch exceptions which was thrown in callback. This will lead our program unpredictable.

```js
try{
  setTimeout(function(){
    throw new Error("you can't catch me!");
  }, 1000);
}
catch(e){
  console.log("Caught one!");
  console.log(e);
}
// Here is actually exception was thrown.
```

Luckily, there is a new feature called Promise which can save you from this mess.

# Promise
Promise is ES6's feature. With this, you can simplify complicated callbacks into human readable one:

```js
// if request returns Promises
request("http://www.example.com/api/data")
.then((result) => {
  return result.status === "OK" ? request("http://www.example.com/api/data2") : throw new Error("Something went wrong while requesting data 2");
})
.then((result) => {
  return result.status === "OK" ? request("http://www.example.com/api/data3") : throw new Error("Something went wrong while requesting data 3");
});
```

This is much easier to read, right? Did I just mention about exception?

```js
new Promise((resolve, reject) => {
  let result = doSomething();
  if(result){
    resolve(result);
  }
  else {
    reject(result.reason);
  }
})
.then((result) => {
  console.log("Succeed: " + result);
})
cathc((reason) => {
  console.log("Oops, something went wrong: " + reason);
});
```

In this way you can solve async problem elegantly.

Using Babel like me? Insert `require("babel/polyfill")` at the head of your code to enable this goodie.

If in your environment ES6 is not allowed, don't be sad, there are tons of libraries implementing this awesome feature!

* [Bluebird](http://bluebirdjs.com/docs/getting-started.html)
* [jQuery - Deferred](https://api.jquery.com/category/deferred-object/)
* [Q](http://documentup.com/kriskowal/q/)
* [when](https://github.com/cujojs/when/blob/master/docs/api.md)
* ...I only named a few, google `js promise lib` for more!

# Async/Await
What if you could write async code like it was sync? ES7 provides a new feature called `async/await`. By combining it with promise, you can write async code in sync style!

```js
IamAsync();

console.log("Hello");

async function IamAsync(){
  let result = await new Promise((resolve, reject) => { setTimeout(resolve, 3000, "World!"); });
  return result;
}

// output:
// Hello
// (wait for 3 seconds...)
// World!
```

Currently `async/await` is only available in Babel by running with `babel source.js -o compiled.js --stage 0` and beware that it is still in experiment, everything is subject to change.


# References & Resources
* [How to articulate the difference between asynchronous and parallel programming?](http://stackoverflow.com/questions/6133574/how-to-articulate-the-difference-between-asynchronous-and-parallel-programming)
* [What is the difference between concurrency, parallelism and asynchronous methods?](http://stackoverflow.com/questions/4844637/what-is-the-difference-between-concurrency-parallelism-and-asynchronous-methods)
* [How does a single thread handle asynchronous code in JavaScript?](https://www.quora.com/How-does-a-single-thread-handle-asynchronous-code-in-JavaScript)
* [Get the door, Javes! Understanding Sync & Async in JavaScript](https://www.discovermeteor.com/blog/understanding-sync-async-javascript-node/)
* [Philip Roberts: What the heck is the event loop anyway? JSConf EU 2014](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
* [Promises for asynchronous programming](http://exploringjs.com/es6/ch_promises.html)
* [A Primer on ES7 Async Functions](https://code.tutsplus.com/tutorials/a-primer-on-es7-async-functions--cms-22367)
* [ES7 Async/Await](http://rossboucher.com/await/#/)
* [The Evolution of Asynchronous JavaScript](https://blog.risingstack.com/asynchronous-javascript/)
* [2. What is Node.js?](http://book.mixu.net/node/ch2.html)
* [Is javascript guaranteed to be single-threaded?](http://stackoverflow.com/questions/2734025/is-javascript-guaranteed-to-be-single-threaded)
* [How does a single thread handle asynchronous code in JavaScript?](https://www.quora.com/How-does-a-single-thread-handle-asynchronous-code-in-JavaScript)
