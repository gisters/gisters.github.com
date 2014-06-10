---
layout: post
title: "在 Windows 8 & Linux 双系统上无法安装微软补丁 KB2965065"
description: "问题拖了将近一个月，一直无解，查看 Windows 8 的日志，说是启动分区错误导致补丁安装失败，该补丁貌似就是针对 boot 的更新，这让干掉了 Windows 隐藏分区的 Linux & Windows 双系统的用户咋搞？况且我还是三系统。"
keywords: "windows, kb2965065, linux"
category: Linux
tags: [Grub, Syslinux, Windows]
---
{% include JB/setup %}

问题拖了将近一个月，一直无解，每次安装微软补丁 KB2965065 都是失败，查看 Windows 8 的日志，说是启动分区错误导致补丁安装失败，该补丁貌似就是针对 boot 的更新，这让干掉了 Windows 隐藏分区的 Linux & Windows 双系统的用户咋搞？况且我还是三系统。

今天看到新闻说，微软 Windows 8 更新服务期限限制到6月10号，于是，晚上下决心解决该问题。

<!-- more -->
放狗搜，不停的切换关键字，终于在一个社区找到了解决方案。

>You are getting INACCESSIBLE\_BOOT\_DEVICE when BFSVC AI tries to access the System Partition (the one with bootmgr). Using diskmgmt.msc, verify that the System Partition is marked Active.

Grub2 用户非常简单，配置加一行让系统认为 Windows 8.1 分区是 bootable 分区

    parttool (hd0,msdos3) boot+

msdos3 为你 Windows 8.1 所在的分区，最终的结果类似于

```
menuentry "Microsoft Windows 8.1" {
    insmod chain
    set root=(hd0,msdos3)
    parttool (hd0,msdos3) boot+
    chainloader +1
}
```

而我使用 Syslinux，不幸的是没有相关解决方案，翻篇了 syslinux 的 [wiki](http://www.syslinux.org/wiki/index.php/Comboot/chain.c32) 也没有相关的说明。

实在是没辙了，只能使用笨办法：

    sudo cfdisk /dev/sda

将 windows 所在分区设为 bootable，重启，装补丁 KB2965065，顺利安装完成。

最后，用 Gentoo 的 liveusb 启动机子，重新将 Linux 的 /boot 所在目录设为 bootable。

参考：[http://www.eightforums.com/windows-updates-activation/46487-can-t-install-kb2965065-2.html](http://www.eightforums.com/windows-updates-activation/46487-can-t-install-kb2965065-2.html)

顺便说下，用 syslinux 引导 clover 黑苹果，也很简单。

```
LABLE macosx
    MENU LABEL Mac OS X with Clover
    com32 chain.c32
    APPEND hd1 1
```

很熟悉是不是，Syslinux 通过 BIOS 引导黑苹果所在硬盘的 mbr，从而启动 clover 来引导黑苹果。UEFI 则没尝试，有谁成功引导 win & mac 的可以告知下。
