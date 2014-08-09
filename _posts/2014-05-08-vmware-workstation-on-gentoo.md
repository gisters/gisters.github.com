---
layout: post
title: "Gentoo 上安装 VMware Player"
description: "Gentoo 上安装 VMware Player 的一些记录"
keywords: "gentoo,vmware"
category: Linux
tags: [Gentoo, VMware]
---

在 Mac OS X 上分别体验了 VirtualBox 与 VMware Fusion，结果 VMware Fusion 的显卡性能确实是高于 VirtualBox，今晚决定在台式机 Gentoo 上也准备体验下 VMware Player。

    # echo "app-emulation/vmware-tools vmware_guest_linux vmware_guest_windows" >> /etc/portage/package.use
    # emerge -av app-emulation/vmware-player
    # emerge --config vmware-player

Gentoo 的 Portage 系统本身就有 VMware Player 的 ebuild，安装很简单。期间可能会出现一些错误。譬如我就出现了 `app-emulation/vmware-tools` 安装失败的情况

<!-- more -->
```
/inode.c:49:4: warning: (near initialization for ‘RootInodeOps.lookup’) [enabled by default]
/var/tmp/portage/app-emulation/vmware-modules-279.2/work/vmblock-only/linux/inode.c: In function ‘InodeOpFollowlink’:
/var/tmp/portage/app-emulation/vmware-modules-279.2/work/vmblock-only/linux/inode.c:224:4: error: implicit declaration of function ‘vfs_follow_link’ [-Werror=implicit-function-declaration]
cc1: some warnings being treated as errors
make[3]: *** [/var/tmp/portage/app-emulation/vmware-modules-279.2/work/vmblock-only/linux/inode.o] Error 1
/var/tmp/portage/app-emulation/vmware-modules-279.2/work/vmblock-only/linux/control.c: In function ‘ExecuteBlockOp’:
/var/tmp/portage/app-emulation/vmware-modules-279.2/work/vmblock-only/linux/control.c:282:9: warning: assignment from incompatible pointer type [enabled by default]
make[2]: *** [_module_/var/tmp/portage/app-emulation/vmware-modules-279.2/work/vmblock-only] Error 2
make[1]: *** [sub-make] Error 2
```

搜索后查询到这个页面：[https://bugs.gentoo.org/show_bug.cgi?id=508204](https://bugs.gentoo.org/show_bug.cgi?id=508204)

安装说明，打 3 个补丁，再 patch 一下 ebuild，随后顺利安装。

![Windows 8.1 on VMware]({{ site.qiniudn }}/images/2014/05/vmware-windows.png)

当然，期间因为 ati 闭源驱动更新，重新编译下 kernel。由于我的显卡是 radeon hd 7850，芯片代号为 **PITCAIRN**，于是

```
Device Drivers  --->
    Generic Driver Options  --->
        -*- Userspace firmware loading support
            [*] Include in-kernel firmware blobs in kernel binary
                (radeon/<YOUR-MODEL>.bin)
                (/lib/firmware) Firmware blobs root directory
```

需要

```
/lib/firmware/radeon/PITCAIRN_ce.bin
/lib/firmware/radeon/PITCAIRN_mc2.bin
/lib/firmware/radeon/PITCAIRN_mc.bin
/lib/firmware/radeon/PITCAIRN_me.bin
/lib/firmware/radeon/PITCAIRN_pfp.bin
/lib/firmware/radeon/PITCAIRN_rlc.bin
/lib/firmware/radeon/PITCAIRN_smc.bin
/lib/firmware/radeon/TAHITI_uvd.bin
```

之前编译内核时，由于 `x11-drivers/radeon-ucode` 早期版本，缺了 `PITCAIRN_mc2.bin`，补上，最后 VMware Player 非常顺利，没遇到其他问题。

记录完毕！

#### Update: 2014-05-09

已用 vmware-player 替换 vmware-workstation，个人使用免费。最大的区别是 vmware player 中 guest os 不能多开，不过，本来就是应急的，无所谓了。
