---
title: Merge Branch From Another Repo
date: 2016-12-12 22:38:26
tags: git
---

Just a quick TIL note here.

If you have ever encountered a similar scenario:

> You have repo A and repo B.
> Repo B is derived from repo A.
> You are currently on repo B, and want to merge branch (or pick commit) from repo A.
> How to achieve that?

Here is what git command `remote` magically does:

1. `git remote add repoA addressOfRepoA`
2. Assume you are on `master` branch of repo B, and want to merge `master` branch from repo A.
3. `git pull repoA master`
4. Done.

Actually an repo doesn't have limitation on how many `remote repo` it can have, so if you run into similar case like me, hope this post help.

References:
- [Git Basics - Working with Remotes](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes)
- [StackOverflow - git merge different repositories](http://stackoverflow.com/questions/2949738/git-merge-different-repositories)
