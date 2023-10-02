---
layout: post
title: "Differences Between sync & async, blocking & non-blocing and event-driven"
date: 2013-09-07 22:34
comments: true
tags: [concurrent, meta]
---

I always thought I understood the concept between blocking & non-blocking, synchronous & asynchronous and could tell the differences, but after I found this [post](http://stackoverflow.com/questions/3231018/how-does-event-driven-i-o-allow-multiprocessing), a chaotic storm exploded in my brain! These were totally different two things in my mind!

Terminologies can have different meanings with things, even though they have the same skin. The definition of terminologies differ from context.

Originally I thought blocking & non-blocking, synchronous & asynchronous were only talking about relationships between client and server structure, but when it comes to server, or computer, and disk & memory in itself, it has other meanings.

So what's the difference between these two fields? Before that, I need to define three objects:

* client
* server
* disk(socket)

Definitions in client & server:

* Synchronous

	* Blocking

		server serve only one client at a time.

	* Non-blocking

		server can serve many clients at a time, but clients will need to constantly check if their requests are accepted. (I can't find a better definition of non-blocking in client & server relationship, so I assume it's similar to the ajax polling technique.)

	* Event-driven

		server can serve mant clients at at time, and with a single process on a callback.

* Asynchronous - server will create a new thread for each client on connecting.

Definitions in server & disk(socket):

* Synchronous

	* Blocking

		process will send a recvform system call to kernal, and then kernal will start preparing data process needs, and finally kernal return with result back to process. During the data preparation the process is blocked.

	* Non-blocking

		process will keep sending recvform to kernal. If is not ready, kernal will send a EWOULDBLOCK back to process, or will start data preparation.

	* Event-driven

		also named select/poll. It's similar to the blocking IO, but use select & poll system call to achive non-blocking.

* Asynchronous - create a new thread and let it handle all the call sending and receiving. Kernal will deliver a signal when data has been prepared.

A connection is established which means a socket stream has been created for client and server, so, in my opinion, definition in server & disk(socket) also works in client & server relationship.

refered from readings:

- [IO - 同步，异步，阻塞，非阻塞 （亡羊补牢篇）](http://blog.csdn.net/historyasamirror/article/details/5778378)
- [How does event driven I/O allow multiprocessing?](http://stackoverflow.com/questions/3231018/how-does-event-driven-i-o-allow-multiprocessing)
- [asynchronous vs non-blocking](http://stackoverflow.com/questions/2625493/asynchronous-vs-non-blocking)
- [event-driven structure in client & server](http://stackoverflow.com/questions/5325342/help-with-event-driven-tcp-server)
- [what is event driven web server](http://stackoverflow.com/questions/3981566/what-is-event-driven-web-server)
- [Asynchronous I/O](https://en.wikipedia.org/wiki/Asynchronous_I/O)

Point it out if I have mistaken something!
