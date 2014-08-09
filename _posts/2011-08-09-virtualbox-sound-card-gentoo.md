---
layout: post
title: "Gentoo 下 VirtualBox 声卡独占"
description: "Gentoo 下 VirtualBox 声卡独占"
keywords: gentoo, virtualbox, 声卡
category: Linux
tags: [Gentoo, VirtualBox, Alsa, Virtual]
---

不知道为什么，去年 2 月份以来这个问题就一直存在，解决方案也早已提供，但 Gentoo 官方为什么一直不修复

具体表现为，VirtualBox 启动后，没声音，但系统其他程序使用 alsa 则一切正常

调 VirtualBox 的 guest 声卡为 oss or pulseaudio 来驱动，则 VirtualBox 中的客户机启动后，host 机的一切程序都不能使用 alsa.

<!-- more -->
解决方案为

    cd /usr/portage/media-libs/libsdl下，
    vim libsdl-1.2.14-r4.ebuild #（你当前使用的libsdl版本）

将其中的

    --disable-alsa-shared

注释掉，然后

    ebuild libsdl-1.2.14-r4.ebuild digest

最后重新编译 libsdl，即

    emerge libsdl

OK 了，现在 vbox 中的 guest 与当前的 host 不会再抢 alsa 了
