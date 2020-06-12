---
layout: post
title: "不要记录 bash 操作历史"
category: Linux
tags: [Bash]
---

很多时候，我们可能不想记录某一个登陆进程中的 bash 操作历史。

譬如，你在教学过程中，演示一些危险的 shell 操作，而被系统记录；譬如，我们偶尔进行一些不可描述的操作时，我们当然不想这次的操作被记录，所以我们想清除这次的登陆操作。

原因很多，可能正经，可能不正经。下面是具体的一些方法。

#### 移除当前进程的操作历史，保留之前历史

##### 变量 HISTFILE

    unset HISTFILE; exit

<!-- more -->

##### 杀掉 console

    kill -9 $$

#### 彻底移除所有历史

##### history 选项

    history -c; history -w; exit

##### 变量 HISTSIZE

    HISTSIZE=0; exit

##### 删除 history 记录的文件

    rm -f $HISTFILE; unset HISTFILE; exit
    or
    cat /dev/null > ~/.bash_history; exit

当然，一般人，也用不到清除操作历史。
