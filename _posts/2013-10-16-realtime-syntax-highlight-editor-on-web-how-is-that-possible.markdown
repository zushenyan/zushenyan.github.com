---
layout: post
title: "Realtime Syntax Highlight Editor on Web, How Is That Possible?"
date: 2013-10-16 13:29
comments: true
---

# Story
Although I have already played around some front-end techniques, I still think my webdev-fu is noob as hell, so maybe I should start from scratch. How about basic DOM structure? Hmm, good. There are many realtime syntax highlight editor, I alway wonder how they work, and building one myself won’t be hard, right? Challenge accepted!

# Google Hard
The quickest way to know how one thing works is to find one similar thing and study it. As far as I know, there are two editors with active communities — [Code Mirror](http://codemirror.net/) and [Ace Edtior](http://ace.c9.io/#nav=about), but I am lazy as fuck, and reading other people’s code without any guides is really hard, so I decide to do some google about how it works.

# Approach 1 – ContentEditable
Ace Editor is too modularized to make a quick understand. What I need is a one-filed, code-not-so-long example to make a quick structure overview. Code Mirror has some additional libaray, but the main program remains in one file, so I think maybe it’s good for a starter.

While I was googling guides about Code Mirror, I happened to find [this article](http://codemirror.net/1/story.html) about how Code Mirror 1 was made. This inspired me. Without knowing there was already an new version of Code Mirror 2, I started coding in this way.

The basic idea of this way is to get raw data string from parsing contentEditable div’s inner HTML, and trying to attach span tag with syntax. Basically, contentEditable way is likely, and Code Mirror 1 did it. To me, it’s a hard way. The main problem is, the behavior of key inputs differ from browser to browser, which means if you hope to get the same data from all platforms, you will be disappointed. In actually, you will get all different versions of data.

Sure you will say “Why don’t you change those behaviors to one? Problem solved!” Well, you won’t like the idea in the end, because you need to **FUCKING REMAP ALL THE CONTROL KEY BEHAVIORS** to act like the same, which being said, you need to integrate all enter key versions into one, all shift-enter key versions into one, all cmd-enter key versions into one… It’s only for enter keys, not to mention other keys’ combination, and… there are 5 mainstream browsers. Ouch, it hurts.

After one-week hard coding, it exhausted me. This is not a good idea. I need a better solution.

# Approach 2 – Hidden Textarea
This is the way what Code Mioor 2 and Ace Editor made. You can read [this article](http://codemirror.net/doc/internals.html) if you already feel the pain from approach 1.

The key concept is that it hides a textarea from users’ eyes, and makes them believe they are typing in textarea. What they see, in actually, is **many divs** pretending like a textarea.

The main challenge from this approach is that since you are not using the default element, you need to mimic your own textarea element — everything inside the textarea, including scrollbar, caret, selection and mouse events.

Though this is not hard, I still want to find an easy way to achive my goal because what I want to do is make a simple realtime syntax highlight editor, not another full functional editor.

During my work on the appraoch 2 way, suddenly an idea came to my mind “Hey, why don’t I just overlay a transparent textarea on the visual view? Using the default textarea can save a lot of works!”

So I started my overlay textarea journey.

# Approach 3 – Overlay Textarea
[Here is my final work.](http://zushenyan.github.io/SRSHME/)

Basic functions:

* Some Markdown syntaxes highlight.
* Browser default key mapping.
* Browser default mouse behaviors.

Tested on the latest version of Chrome, Firefox, Safari, Opera and IE 10, and be aware of that this code is not optimized. If you paste a lot of text into this textarea, I am sure it will be laggy as fuck.

On IE 10 you may experience the late responce of caret because IE’s caret visual is not controllable in textarea. In order to show only one caret, I choose to hide my own caret to prevent from showing two carets at once.

What I do here is simply overlay a transparent textarea over a visual div. Basically, the concept is similar to approach 2 but sacrifices the possiblity of further customizations for lazy code. I don’t need to write mimic scrollbars and mosue event anymore.

# Conclution
To make a fully functional online text editor, you need to write **a lot of codes** to solve problems like right-to-left text order, line & word wrap, wide characters and so on.

If you are curious about how those realtime syntax highlight editors are possible, I hope this post can help.
