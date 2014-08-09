---
layout: post
title: "Git Alias 引起的权限问题"
description: "Git Alias 引起的权限问题"
keywords: git alias 权限
category: Linux
tags: [Git]
---

```
^_^ ~/Documents/git/overlay $ git ll
fatal: cannot exec 'git-ll': Permission denied

^_^ ~/Documents/git/overlay $ git config --get alias.ll
log --graph --all --pretty=format:'%C(yellow)%h%C(cyan)%d%Creset %s %C(white)- %an, \
%ar%Creset' --decorate --abbrev-commit --max-count=25
```

<!-- more -->

解决办法

    $ echo $PATH |tr ':' '\n' |xargs ls -ld

查看哪个目录没有权限 x，譬如我的

    $ sudo chmod +x /usr/games/bin/

然后就可以了

参考资料：[http://stackoverflow.com/questions/7997700/git-aliases-causing-permission-denied-error](http://stackoverflow.com/questions/7997700/git-aliases-causing-permission-denied-error)
