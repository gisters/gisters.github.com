---
layout: post
title: "Git 进阶：alias"
description: "Git 进阶：alias"
keywords: git, alias, linux, shell, config
category: Linux
tags: [Git, Usage]
---

现在的 Linux 发行版的 shell 环境，一般都可以使用 alias，好处是显而意见的，用简单的几个字母代大量的命令。没错，git 也提供了alias 的使用，让大家在使用中可以自定义简单的命令。

使用方法：

    $ git config --global alias.alias command

或者直接编辑 `.gitconfig` 文件，格式如下

```
[alias]
      alias= command
```

<!-- more -->

譬如，`git status` 命令，我们可以这样操作

    $ git config --global alias.s status

或者

```
[alias]
      s = status
```

这只是一个很简单的例子，可能大家都用不上，那么下面来个 `git log` 的例子。

`git log` 本身的显示很凌乱，我们稍微处理下让它直观一些譬如下面这行命令，我们来让 git 显示最近 3 次的 commit，以及 date author 信息

    $ git log --stat --decorate --abbrev-commit --max-count=3

呃，记得住吗？

我们通过 alias 将之缩短

    $ git config --global alias.l "log --stat --decorate --abbrev-commit --max-count=3"

![git l]({{ site.qiniudn }}/images/2012/06/git-l.png "git l")

那么以后只需 `git l` 即可得到你所需要的信息。上图现在很清晰明了了吧，什么？还是凌乱，看得头晕乎？那么再来个规则点的

```
git config --global alias.ll "log --graph --all --pretty=format:'%C(yellow)%h%C(cyan)%d%Creset %s %C(white)- %an, %ar%Creset' --decorate --abbrev-commit --max-count=20"
```

![git ll]({{ site.qiniudn }}/images/2012/06/git-ll.png "git ll")

通过 `git ll` 来查看，感觉怎么样了？

以下是在我的 `.gitconfig` 中的 alias 设置

```
[alias]
    ll= log --graph --all --pretty=format:'%C(yellow)%h%C(cyan)%d%Creset %s %C(w    hite)- %an, %ar%Creset' --decorate --abbrev-commit --max-count=20
    l = log --stat --decorate --abbrev-commit --max-count=3
    sbi = submodule init
    sbu = submodule update
    sbp = submodule foreach git pull
    sbc = submodule foreach git checkout master
```

参考资料：[https://git.wiki.kernel.org/index.php/Aliases](https://git.wiki.kernel.org/index.php/Aliases)
