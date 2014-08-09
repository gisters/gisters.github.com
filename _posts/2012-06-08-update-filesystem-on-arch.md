---
layout: post
title: "Arch 升级 Filesystem 的小问题"
description: "Arch 升级 Filesystem 的小问题"
keywords: arch, filesystem, linux, initscripts
category: Linux
tags: [Arch, Filesystem]
---

首先，昨晚已经注意到 Arch 官方的新闻，升级 filesystem-2012-6-2 将要面对的一些问题。

在大多数 Linux 平台下，initscripts 在启动时均会创建一些软链接，然而这些软链接又不属于任何包，Arch 的这次 filesystem 的升级就是解决这个问题。

方案是

    pacman -Syu --ignore filesystem && pacman -S filesystem --force

或

    rm -rf /var/run /var/lock && pacman -Syu && reboot

<!-- more -->

然而本人在升级的过程中却发生了意外的情况

>error: extract: not overwriting dir with file /var/lock

手动 `rm -rf` 来删除，却提示不能删除，`/var/lock` 正在被某软件包使用。放狗搜了下，也一无结果，头痛啊。

重启电脑后，忽然想到了之前在 `/etc/fatab` 中作的修改，原来在其中我手动将 tmpfs 挂到了 `/var/lock` 上…..结果就是.赶紧

    umount /var/lock

或者注释掉该行，重启，`pacman -Sf filesystem`

顺利通过升级，结果就是

- /var/run -> /run
- /var/lock -> /run/lock
