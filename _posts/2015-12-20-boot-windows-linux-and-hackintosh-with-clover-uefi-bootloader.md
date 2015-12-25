---
layout: post
title: "Clover 启动黑苹果、Windows、Linux"
category: Linux
tags: [Clover, EFI]
---

这两天花了点时间，将 windows 10 & Gentoo 所在的硬盘全部换成 gpt，然后全部由 Clover 来引导。我本机的情况是，windows 10 & Gentoo 共用一块 SSD，黑苹果单独一块 SSD。

![Clover UEFI](http://cdn.09hd.com/images/2015/12/clover.jpg)

<!-- more -->
黑苹果不动，因为本身就是 Clover 引导的，Windows 10 则用 gpt 分区并重装了系统。而 Gentoo 的引导，有些复杂，因为 Clover 貌似不能直接启动 kernel （看到 [Archlinux Wiki](https://wiki.archlinux.org/index.php/Clover)）上的介绍，可惜不成功，Clover 中压根就不出现 linux 启动菜单。所以我的 Gentoo 引导的做法是 Clover -> Grub2 -> kernel。

由于 Clover 能自动扫每块硬盘引导区，所以，不需要配置 Clover，唯一要做的，似乎也只剩下 grub2 UEFI 的安装了。

此时，出现一个问题，敝人的 Gentoo liveusb 是 mbr 的，chroot 进 Gentoo 后，无法完成 Grub2 UEFI 的安装。无奈，在 Windows 下用 [Rufus](http://rufus.akeo.ie) 制作了一个 ubuntu 的 UEFI 的 u 盘启动盘。启动后，chroot 进 Gentoo，才顺利完成 Grub UEFI 的安装。

之前安装 windows 10 的时候是自动分区的，然后磁盘压缩一个空间给 Gentoo 使用，所以，那块 SSD 上的分区是这样的：

```
/dev/sda1 			Windows Recovery
/dev/sda2 			EFI
/dev/sda3 			Nucrisift Reserved
/dev/sda4 			Windows 10
/dev/sda5 			Gentoo
```

ubuntu 的 UEFI 启动 u 盘启动，选试用，启动到桌面，打开终端

```
$ sudo su -
$ mkdir /mnt/gentoo
$ mount /dev/sda5 /mnt/gentoo
$ mkdir /mnt/gentoo/boot/efi
$ mount /dev/sda2 /mnt/gentoo/boot/efi
$ mount -t proc proc /mnt/gentoo
$ mount --rbind /sys /mnt/gentoo/sys
$ mount --rbind /dev /mnt/gentoo/dev
$ chroot /mnt/gentoo /bin/bash
$ env-update && . /etc/profile
```

成功 chroot 进 Gentoo 环境后，通过 emerge 安装一个 grub，再编辑下 `/etc/default/grub` 与 `/etc/grub.d/40-custom` 后，生成配置文件，并安装：

```
$ grub2-mkconfig -o /boot/grub/grub.cfg
$ grub2-install --target=x86_64-efi --efi-directory=/boot/efi
```

OK，基本差不多了，主板开机项多出一个 Gentoo 启动项，Clover UEFI 也能顺利识别。
