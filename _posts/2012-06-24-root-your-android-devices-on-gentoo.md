---
layout: post
title: "Gentoo 下 root 你的 Android 设备"
description: "Gentoo 下 root 你的 Android 设备"
keywords: gentoo, android, root
category: Linux
tags: [Gentoo, Android]
---

购了 Android 设备，当然要 root 了，否则很多功能用不到，譬如简单的截屏也需要 root 后的设备才行，更不要说修改 hosts 等文件了。

其次，windows 平台下有啥一键 root 的，Linux 下没有，不过操作其实也很简单，下面介绍在 Gentoo 下 root 你的安卓设备。

首先在 Gentoo 下搭建 android sdk 平台，请参阅这篇文章：[Gentoo 下通过 usb 连接 Android]({% post_url 2012-06-23-connect-android-with-usb-on-gentoo-and-arch %})

<!-- more -->

其次，去这个网址下载最新版本的 `su-bin` 并解压：[http://androidsu.com/superuser/](http://androidsu.com/superuser/)

再去这个网址下载最新版的 `DroidRazrRootMacLinux.zip` 并解压：[http://downloadandroidrom.com/file/DroidRazr/rooting](http://downloadandroidrom.com/file/DroidRazr/rooting)

并在设备中配置：设置 –> 应用程序 –> 开发，勾选 usb 调试，并通过 usb 连接你的计算机

确认下你的设备：

    $ adb start-server
    $ adb devices

如果认到你的 Android 设备，那么就可以开始了

    $ adb push /local/path/zergRush /data/local/tmp/
    $ adb shell chmod 6755 /data/local/tmp/zergRush
    $ adb shell ./data/local/tmp/zergRush
    $ adb shell mv /system/xbin/su /system/xbin/su.bak # 备份设备中原 su 文件
    $ adb push /local/path/su /system/xbin             # 把下载解压得到的 su 文件 推送到 android 设备中
    $ adb shell chmod 6755 /system/xbin/su             # 变更权限
    $ adb shell sync                                   # 同步缓存中的文件
    $ adb shell reboot                                 # 重启 android 设备

接下来就可以从网上下载最新版的 Superuser.apk：

    $ adb push /local/path/Superuser.apk /system/app

然后在手机中通过 Superuser 将 `su` 更新到最新版本即可。

当然最简单的 root 办法是运行 `DroidRazrRootMacLinux.zip` 中的 `runmelinux.sh` 来一键 root

参考：[http://davesource.com/Solutions/20120110.Safe-root-unlock-Samsung-Galaxy-Mini-on-Ubuntu-Linux.html](http://davesource.com/Solutions/20120110.Safe-root-unlock-Samsung-Galaxy-Mini-on-Ubuntu-Linux.html)
