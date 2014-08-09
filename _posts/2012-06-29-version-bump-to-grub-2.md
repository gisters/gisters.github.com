---
layout: post
title: "将 Grub 升级到 2.00"
description: "grub 自动升级到了 2.00"
keywords: grub2, gentoo
category: Linux
tags: [Grub, Gentoo, Configuration]
---

老实说之前一直用的 Grub 0.97，就一个启动界面嘛，何必搞的那么复杂。今天 Gentoo 中 `eix-sync` 后，grub 自动升级到了 2.00，为了防止后面意外发生，遂今天努力读了下 grub2 的文档，也顺便 install 到了 `/dev/sda`。

总的来说还是蛮简单的，首先 `/etc/default/grub` 中配置下，一目了然的配置

```
GRUB_DISTRIBUTOR="Gentoo"
GRUB_DEFAULT=0                                        #默认启动grub菜单中的第一项，和grub1中一个意思
GRUB_HIDDEN_TIMEOUT=1                                 #隐藏倒计时
GRUB_HIDDEN_TIMEOUT_QUIET=true
GRUB_TIMEOUT=0                                        #倒计时秒数
GRUB_CMDLINE_LINUX_DEFAULT=""                         #kernel行的附加命令，譬如quiet splash等
GRUB_CMDLINE_LINUX=""
#GRUB_TERMINAL=console                                #图形终端
#GRUB_GFXMODE=640x480                                 #分辨率
#GRUB_THEME="/boot/grub2/themes/starfield/theme.txt"  #grub2主题配置的路径
#GRUB_BACKGROUND="/boot/grub2/mybackground.png"       #背景图片
#GRUB_DISABLE_LINUX_UUID=true                         #uuid
#GRUB_DISABLE_RECOVERY=true                           #recovery模式
```

<!-- more -->

这里我将倒计时设置到了 0，因为很少启动到 windows 下，懒得等待。

其次，`/etc/grub.d/40-custom`

```bash
#!/bin/sh
exec tail -n +3 $0
# This file provides an easy way to add custom menu entries.  Simply type the
# menu entries you want to add after this comment.  Be careful not to change
# the 'exec tail' line above.

menuentry "Gentoo Linux 3.4.4" {
set root=(hd0,1)
linux   /boot/3.4.4-gentoo root=/dev/sda5 resume=/dev/sda6
}

menuentry "Gentoo Linux 3.4.4 (rescue)" {
set root=(hd0,1)
linux   /boot/3.4.4-gentoo root=/dev/sda5 init=/bin/bb
}
```

很简单，和 grub1 没啥区别，当然如果有 windows 系统的话，则在上面文件中最下面加入

```bash
menuentry "Microsoft Windows XP" {
    insmod chain
    set root=(hd0,2)
    chainloader +1
}

menuentry "Windows 7 BIOS/MBR" {
     insmod part_msdos
     insmod chain
     parttool hd1,msdos1 hidden+ boot-
     parttool hd1,msdos2 hidden- boot+
     chainloader (hd1,msdos2)+1
}
```

最后生成配置文件

    # grub2-mkconfig -o /boot/grub2/grub.cfg

如果出现 no file 错误，则先 touch 一个空白文件先，在执行配置文件的生成

最后安装到 mbr

    # grub2-install --no-floppy /dev/sda

详细配置可参考：[http://wiki.gentoo.org/wiki/GRUB2](http://wiki.gentoo.org/wiki/GRUB2)
