---
layout: post
title: "黑苹果的一些技巧汇总"
category: Mac
tags: [OS X, Hackintosh]
---

白果虽然省心，但是如果要性能的话，那么就必须要 Mac Pro 了，移动的 U 不是用来干重力活的。可是囊中羞涩，所以，几乎黑苹果几乎是接下来的唯一选择。

之前写过一篇文章[OS X 的一些技巧汇总]({% post_url 2014-01-09-os-x-tips-and-tricks %})，今天再纪录下黑果的一些使用纪录。

###### 重建内核扩展缓存

    $ sudo touch /System/Library/Extensions
    $ sudo kextcache -u /

至于权限修复就不说了，就是 chmod & chown 的使用，譬如

    $ sudo chown -R root:wheel /System/Library/Extensions/

<!-- more -->
###### 修改显示机型

- 用 clover 配置文件修改机型
- 编辑 `~/Library/preferences/com.apple.SystemProfiler.plist` 文件来修改机型

###### 重建 Mac 的 Recovery HD 分区

    $ dmtest ensureRecoveryPartition / \
    /Volumes/OS\ X\ Install\ ESD/BaseSystem.dmg 0 0 \
    /Volumes/OS\ X\ Install\ ESD/BaseSystem.chunklist

###### 制作 U 盘安装盘

譬如 OS X 10.10 的制作

    $ sudo /Applications/Install\ OS\ X\ Yosemite.app/Contents/Resources/createinstallmedia --volume /Volumes/iPlaySoft --applicationpath /Applications/Install\ OS\ X\ Yosemite.app --nointeraction

###### 忽略一些硬件更新补丁

黑苹果用的是模拟白果硬件的方式，故一些针对白果的硬件补丁，在黑果上应该是安装不上的，表现形式就是一只更新，重启后又更新，不断更新。那么忽略它吧，譬如上次的 Thunderbolt Firmware Update

    $ sudo softwareupdate --ignore ThunderboltFirmwareUpdate1.2
