---
title: A cheatsheet of the git submodule command
date: 2023-11-01 12:12:33
tags: git
---

Owning to needing to change the theme of hexo, I need a way to modify them with `git submodule`. Here is a quick `cheatsheet` for myself.

# Why git submodule

One of the means to maintain a repository in another repository.

# Cheatsheet

To add a repository:

`git submodule add <repository> `

To add a repository with a specific directory folder

`git submodule add <repository> <direcotry>`

To modify the code for a submodule

```bash
$ cd submodule-folder
$ vim file-to-be-modified
$ git commit -am "foo"
$ git push origin a-remote-branch
$ cd root-folder
$ git commit -am "update"
```

To pull the latest commits for a submodule

```bash
$ git submodule update remote --merge
```

Specifying which branch to pull for a submodule

Open `.gitmodules` and add the `branch` field to the config

```
[submodule "themes/landscape"]
	path = themes/landscape
	url = https://github.com/zushenyan/hexo-theme-landscape.git
	branch = andrew
```

# Links

- https://git-scm.com/book/en/v2/Git-Tools-Submodules
- https://stackoverflow.com/questions/1777854/how-can-i-specify-a-branch-tag-when-adding-a-git-submodule