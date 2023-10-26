---
title: How to resolve timeout when cloning mega-sized repository with git
date: 2023-10-26 15:21:08
tags: git
---

```bash
> git clone url-to-a-repository
error: RPC failed; curl 92 HTTP/2 stream 0 was not closed cleanly: PROTOCOL_ERROR (err 1)
```

So, recently I was trying to clone a mega-sized repository while working on a company project. However, whenever I wanted to clone it with `git clone` it kept popping up the error above. It looks like the root cause is that there is a buffer limit when I need to pull it with HTTPS.

The solution to this is to 
1. Use `SSH` to clone the repository.
2. Use `HTTPS` but pull the content of the repository incrementally.

Due to being unable to resolve SSH credential in a short time frame, I chose the second approach

```bash
> git clone xxx --depth 1
> cd xxx
> git fetch --unshallow
```

With the commands above, the content can be pulled part by part, instead of in one big chunk.

If it still errors with the same message, instead of `git fetch --unshallow`, try using the following command:

```bash
> git fetch --depth=100
> git fetch --depth=200
> git fetch --depth=300
# until the content has been fully pulled
```