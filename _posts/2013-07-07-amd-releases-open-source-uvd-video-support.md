---
layout: post
title: "AMD 的开源 UVD 支持"
description: "早在今年4月2号，Phoronix 就爆出了 AMD 开源 UVD 驱动支持的新闻，懒惰的我知道这个周末，才开始正式这次变动，顺便顺利的将自己的 **Radeon HD 7850** 加入了 UVD。"
keywords: "radeon, ati, amd, uvd, linux"
category: Linux
tags: [Radeon, Kernel, Firmware]
---

早在今年4月2号，[Phoronix](http://www.phoronix.com/scan.php?page=article&item=amd_opensource_uvd&num=1) 就爆出了 AMD 开源 UVD 驱动支持的新闻，懒惰的我直到这个周末，才开始正式开始加入这次变动，也很顺利的将自己的 Kernel 加入了 **Radeon HD 7850** 的 UVD 支持。

主要步骤如下：

1. `emerge --sync` 同步你的 portage 树
2. 确保 `/etc/portage/make.conf` 中的 `VIDEO_CARDS` 为 **radeon**，而非 **fglrx**
3. 用 `x11-drivers/xf86-video-ati` 替换你原来的 `x11-drivers/ati-drivers`
4. 安装最新版 `media-libs/mesa`、`x11-drivers/radeon-ucode`
5. 最后编译 portage 中最新版 kernel

<!-- more -->
主要是 kernel 配置说明下

```
Processor type and features  --->
    [*] MTRR (Memory Type Range Register) support
Device Drivers  --->
    Graphics support  --->

            Generic Driver Options  --->
            [*]   Include in-kernel firmware blobs in kernel binary
            (radeon/<YOUR-MODEL>.bin)
            (/lib/firmware) Firmware blobs root directory

        <*> /dev/agpgart (AGP Support)  --->
            <*> AMD Opteron/Athlon64 on-CPU GART support

        <*> Direct Rendering Manager (XFree86 4.1.0 and higher DRI support)  --->
            <*> ATI Radeon
            [*] Enable modesetting on radeon by default

        <*> ATI Radeon
            [ ]   Enable userspace modesetting on radeon (DEPRECATED)

        -*- Support for frame buffer devices  --->
            [*]   Enable firmware EDID
```

其中，`Include in-kernel firmware blobs in kernel binary` 需要的文件按 [这里](http://wiki.gentoo.org/wiki/Radeon#Firmware) 来选择

譬如我的 **Radeon HD 7850** 就是 `(radeon/PITCAIRN_ce.bin radeon/PITCAIRN_mc.bin radeon/PITCAIRN_me.bin radeon/PITCAIRN_pfp.bin radeon/PITCAIRN_rlc.bin radeon/PITCAIRN_smc.bin radeon/TAHITI_uvd.bin)`

如果你的 Kernel 已经编译完成，并且选择了正确的 firmware，那么在启动过程中会出现

> [drm] UVD initialized successfully.

参考：

- [http://wiki.gentoo.org/wiki/Radeon#Firmware](http://wiki.gentoo.org/wiki/Radeon#Firmware)
- [http://chithanh.blogspot.com/2013/04/new-mesa-features-for-adventurous.html](http://chithanh.blogspot.com/2013/04/new-mesa-features-for-adventurous.html)
- [http://www.x.org/wiki/RadeonFeature/](http://www.x.org/wiki/RadeonFeature/)
