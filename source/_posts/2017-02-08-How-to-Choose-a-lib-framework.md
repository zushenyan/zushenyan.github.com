---
title: How to Choose a Lib/Framework
date: 2017-02-08 18:57:05
tags: [meta, frontend]
---

Choosing a tool is not an easy thing, especially in today's front-end domain, the cycle of tools is like light speed. Developers never catch up.

> Learn as many tools(frameworks, libraries) as you can.

They said. I like the spirit, but in reality, your time is limited, you have to workout, wash clothes, pay debt and blah blah, so I think the appropriate quote should be

> Learn things you really need.

Just learn something that is really worth you put time in, spot things that aren't.

I am absolutely not a pro in picking tools, but I still want to share some ideas when evaluating them.

### Does the tool you currently use solve the problem? Does it solve elegantly?

By elegantly, I mean easy to maintain.

Some solutions are really simple and easy to use, but as program grows, it will no longer be easy to maintain.

In that case, it's necessary to switch tool.

The closest example I think it's [callback hell in JavaScript](http://callbackhell.com/).

### Innovative & General Concept

Do you think the tool is innovative enough to be a game changer or next rock star? Any criticism on it?

Do we already have a similar concept before? Or just another old trick with new skin?

If this is an general concept that exist in textbook, then in my opinion, it is worth learning.

By general concept, if you want examples, I mean something like Functional Programming(lodash, ramda), or Functional Reactive Programming(Rxjs, Bacon).

Even if you leave JavaScript for an new journey, they still exist as another implementation or as an abstraction form.

Something that can be carried around is more worth time investment.

### Is it an lib or framework?

Libraries tend to solve problems in a particular aspect. They don't solve the whole puzzle, instead, only specializing in a small domain.

Frameworks tend to provide a set of solutions for problems from many aspects. They are more suitable in particular scenarios and good for develop groups which want to reduce the learning curve for fresh crew.

Tools considered as library are often more composable with other libraries, which means they are highly independent and have been designed as minimum unit in that language as possible (like class or function).

### Active Community

Active community means more discussion, issue tickets, tutorials.

If you run into an issue you should find a solution in minimum effort.

### Good Documents & Examples

Everyone loves well structured, up-to-dated documents with rich examples, right?

### Does it have tests?

Tests are about quality and stability. I don't believe lib/framework without tests.

### Backed by who?

Tools that are backed by large company should be more likely to continue maintaining since they have more resource, and that means the project is less likely to be abandoned.

### Reference

* [Hype Driven Development](https://blog.daftcode.pl/hype-driven-development-3469fc2e9b22#.li3y15euo)
