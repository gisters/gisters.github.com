---
layout: post
title: "Funtoo 快速安装"
description: "Funtoo 快速安装"
keywords: funtoo, gentoo, daniel robbins
category: Linux
tags: [Funtoo, Installation]
---

[Funtoo](http://www.funtoo.org)，由 Gentoo 创始人 Daniel Robbins 亲手定制，是 Gentoo 的变种。它提供了以下不同于 Gentoo 的特点和优势：

1. 本地的 UTF – 8 编码支持；
2. 以 Git 为基础的 Portage tree 与 Funtoo Overlay；
3. 使用更加紧凑的 mini-manifest 增强 Portage；
4. 每十二个小时自动获取 Gentoo 的新变化；
5. GPT/GUID boot 支持，与改进的 boot 配置 ( Funtoo boot-update 和 boot.conf )；
6. 增强型网络配置 ( core-network )；
7. 使用 Funtoo 的 Metro 构建工具构造最新的稳定与不稳定的 Stages，支持多种处器型号：x86, i686, amd64, athlon-xp, pentium4, core2 ，core2_32, opteron,opteron_32。

<!-- more -->

下面是具体的安装步骤

发现，Funtoo 相比于 Gentoo 最大的区别就是简化了安装步骤，Portage 改为 Git 管理且包含了 Sunrise Overlay，以及 Grub、net 设置变简单了。如果是第一次安装，则推荐 Funtoo；如果已经用上最新 Portage 的 Gentoo，则完全没有必要切换到 Fentoo。

```
Create ext4 filesystem with labels
==================================
# mkswap -L swap /dev/sda2
# mkfs.ext4 -L root /dev/sda3
# mkfs.ext4 -L home /dev/sda4
 
Setting reserved blocks percentage
==================================
# tune2fs -m 1 /dev/sda3
# tune2fs -m 0 /dev/sda4
 
Mount swap and other partitions
================================
# swapon /dev/sda2
# mount /dev/sda3 /mnt/gentoo
 
Check if the date is correct
===========================
# date
 
Go to the installation directory
=================================
# cd /mnt/gentoo
 
Download and extract the stage3 file for your system. I use amd64. You can find other architecturess here:
 
http://distro.ibiblio.org/pub/linux/distributions/funtoo/funtoo/
 
========================================================================
 
# wget http://distro.ibiblio.org/pub/linux/distributions/funtoo/funtoo/amd64/funtoo-amd64-2010.09.05/stage3-amd64-2010.09.05.tar.xz
 
# tar xpf stage3-amd64-2010.08.22.tar.xz
 
Download and extract the portage tree, you find the most recent snapshot here: http://distro.ibiblio.org/pub/linux/distributions/funtoo/~funtoo/snapshots/
=====================================
# wget http://distro.ibiblio.org/pub/linux/distributions/funtoo/~funtoo/snapshots/portage-2010.09.10.tar.xz
# cd /mnt/gentoo/usr
# tar xf /mnt/gentoo/portage-2010.08.27.tar.xz
 
Mount your home partition (if you have one)
============================================
# mount /dev/sda4 /mnt/gentoo/home
 
Configure your compile options
==============================
# nano -w /mnt/gentoo/etc/make.conf
 
e.g. add for a quadcore processor to your make.conf
MAKEOPTS="-j5"
 
Chroot in your Funtoo install
=============================
# cp -L /etc/resolv.conf /mnt/gentoo/etc/
# mount -t proc none /mnt/gentoo/proc
# mount -o bind /dev /mnt/gentoo/dev
 
# chroot /mnt/gentoo /bin/bash
# env-update
# source /etc/profile
# export PS1="(chroot) $PS1"
 
Activate portage for first usage
================================
# cd /usr/portage
# git checkout funtoo.org
# emerge --sync
 
Select a profile, I choose a desktop profile
=================
 
# eselect profile list
# eselect profile set 2
 
Configure your timezone (mine is the Netherlands)
=================================================
 
# ls /usr/share/zoneinfo
# cp /usr/share/zoneinfo/Europe/Amsterdam /etc/localtime
 
Install a kernel. I use genkernel for convience
===============================================
# emerge gentoo-sources genkernel
 
Verify if /usr/src/linux is pointing to the right kernel
========================================================
# ls -l /usr/src/linux
 
Configure & compile the kernel
==============================
# genkernel --menuconfig all
 
Edit the required config files
==============================
# nano -w /etc/fstab
# nano -w /etc/conf.d/hostname
# nano -w /etc/rc.conf
# nano -w /etc/conf.d/keymaps
# nano -w /etc/conf.d/hwclock
 
Enable dhcp at boot
====================
# rc-update add dhcpcd default
 
Set a root password
===================
# passwd
 
Emerge neceserry system services and the grub bootloader.
============================================
# emerge syslog-ng vixie-cron grub boot-update
 
Enable syslog and cron
======================
# rc-update add syslog-ng default
# rc-update add vixie-cron default
 
Install grub
============
# grub-install --no-floppy /dev/sda
# boot-update
 
Add a user
==========
# useradd -m -G audio,cdrom,portage,usb,users,plugdev,video,wheel -s /bin/bash john
# passwd john
 
Remove portage snapshot & stage
==============================
# rm -v  /portage-2010.09.10.tar.xz
# rm /stage3-amd64-2010.09.05.tar.xz
 
Exit & reboot in your funtoo installation
=========================================
# exit
# cd
# umount /mnt/gentoo/dev /mnt/gentoo/proc /mnt/gentoo/home /mnt/gentoo
# reboot
 
Post install configuration
==========================
 
Install xorg
============
# emerge xorg-server
 
Enable hal
==========
# /etc/init.d/hald start
# rc-update add hald default
 
Install a DE
========
# emerge -av kdebase-meta
```

参考：

[http://forums.funtoo.org/viewtopic.php?id=5](http://forums.funtoo.org/viewtopic.php?id=5)

[http://www.funtoo.org/wiki/Funtoo_Linux_Installation](http://www.funtoo.org/wiki/Funtoo_Linux_Installation)
