---
layout: post
title: "Git 进阶：color/diff"
description: "gentoo 默认的 git 真的很朴素，默认配置实在是太简单了。"
keywords: git, color, diff, gentoo
category: Linux
tags: [Git, Usage]
---

![git diff]({{ site.qiniudn }}/images/2012/06/git-diff.png "git diff")

Gentoo 默认的 git 真的很朴素，默认配置实在是太简单了。

<!-- more -->
譬如 diff，譬如 color，

我们还是先将 git 的颜色部分配置好吧。

```
[color]
    branch = auto
    diff = auto
    status = auto
    grep = auto
    ui = auto
    interactive = auto
[color "branch"]
    current = yellow reverse
    local = yellow
    remote = green
[color "diff"]
    meta = yellow
    frag = magenta
    old = red
    new = green
[color "status"]
    added = yellow
    changed = green
    untracked = cyan
```

现在操作 `git status/diff/branch` 时是否眼前一亮？再也不用在白茫茫的一片中寻找信息了。

下面讲git diff的语法

```
git diff [options] [<commit>] [--] [<path>...]
git diff [options] --cached [<commit>] [--] [<path>...]
git diff [options] <commit> <commit> [--] [<path>...]
git diff [options] [--no-index] [--] <path> <path>
```

```
$ git diff                                                比较工作目录树与缓存区之间的区别
$ git diff head                                           比较工作目录树（包括暂存的和未暂存的修改）与版本库最后一个版本的差别
$ git diff [filename]                                     比较具体的文件
$ git diff --stat                                         统计用，显示每个修改的文件的统计
$ git diff [commit] [filename]                            比较当前文件与之前某commit之间的区别
$ git diff [commit]:[old-name] [new-name]                 比较重命名的文件
$ git diff [branch]..[other-branch]                       比较两个branch之间的commit
$ git diff [branch]...[other-branch]                      Compare the tip of other-branch and the closest ancestor (fork point) on [branch]
$ git diff [branch]:[filename] [other-branch]:filename    比较两个分支之间的具体文件的区别
$ git diff --no-index [filename] [filename]               Compare any two files, even if they’re not in a repo :O
```

譬如如果想提取两次 commit 之间的 diff 的话就很简单

    $ git diff oldcommit newcommit > patch

记住，使用 `git diff` 比较本地修改之前，一定要先 `git add .` 将修改加到缓冲区。

`git diff` 的具体用法，请

    $ git diff --help

参考资料：[http://www.kernel.org/pub/software/scm/git/docs/git-diff.html](http://www.kernel.org/pub/software/scm/git/docs/git-diff.html)
