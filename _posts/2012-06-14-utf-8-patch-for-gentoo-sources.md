---
layout: post
title: "gentoo-sources 的 utf8 中文补丁"
description: "gentoo-sources 的 utf8 中文补丁"
keywords: gentoo, utf8, kernel, 中文补丁
category: Linux
tags: [Gentoo, Kernel, Patch]
---

之前一直使用孙海勇的 kernel utf8 中文 [patch](http://blog.chinaunix.net/space.php?uid=436750&do=blog&id=2123586)，去年年底以后，不知为什么，patch 不在更新，放狗搜了一圈，没人提供 patch 的更新。

主要原因是 gentoo-sources 升级到 3.4.2-r1 后，patch utf8 后出错，大体是 fbcon.c 文件 hack 出错。估计是 gentoo-sources 的这次更新的 patch 也有 hack fbcon.c 吧。那么好吧，自己动手丰衣足食。

<!-- more -->

按照出错提示，在 hack fbcon.c 文件时，仅第 11 个地方有问题。vim 进去，找到该地方，代码填充进去，保存，就 OK 了。

不太清楚下次 gentoo-sources 的更新是否还有问题，因为从 kernel.org 上 clone 了 3.4.2 的源码，打上原来的patch是没有问题的。为防止意外 diff 了一个新的 patch 放置于 github 上，也算减少自己折腾量，呵呵。我估计这次受影响的应该也仅 gentoo-sources-3.4.2-r1，因为我没有使用其他的 sources，不太清楚其他的 sources 是否受到影响。

patch 地址：[https://github.com/Ihavee/dotfiles/tree/master/gentoo/etc/portage/patches/sys-kernel/gentoo-sources](https://github.com/Ihavee/dotfiles/tree/master/gentoo/etc/portage/patches/sys-kernel/gentoo-sources)
