---
layout: post
title: "Gentoo学习笔记"
description: "记录 Gentoo 安装过程"
category: Linux
tags: [Gentoo, CLI, Lerning]
---

> 本人曾记录于网易博客，现转回自己博客。

由于是在vbox3中练手，所以只给了4个G的空间

首先从网上下载 install-x86-minimal-xxx.iso

找最近日期的下载，mirrors.163.com 是个不错的源，速度很不错。

启动安装光盘，F1,比较讨厌 FB，更是由于在 vbox 中安装，所以选择启动 gentoo-nofb

<!-- more -->

接下来网络，net-setup eth0，回答几个问题，网络就上去了，测试网络ping -c 3 www.baidu.com

然后分区，fdisk 看的晕头转向，这里我使用 cfdisk 来分区，比较直观

    /dev/hda1 *  1   62  31216+    83  Linux
    /dev/hda2        63  1049996854  82 Linux swap
    /dev/hda3        1055 83223663072  83 Linux

应用文件系统

    # mke2fs /dev/hda1
    # mke2fs -j /dev/hda3
    # mkswap /dev/hda2
    # swapon /dev/hda2

挂载分区

    # mount /dev/hda3 /mnt/gentoo
    # mkdir /mnt/gentoo/boot
    # mount /dev/hda1 /mnt/gentoo/boot

进入目录

    # cd /mnt/gentoo

下载 stage3-i686-2008.0.tar.bz2 与 portage-latest.tar.bz2

    # links http://mirrors.163.com

解开Stage与Portage

    # tar xvjpf stage3-*.tar.bz2
    # tar xvjf /mnt/gentoo/portage-latest.tar.bz2 -C /mnt/gentoo/usr

接下来配置编译选项

    # nano -w /mnt/gentoo/etc/make.conf

    CFLAGS="-O2 -march=i686 -pipe"
    CXXFLAGS="${CFLAGS}"
    CHOST="i686-pc-linux-gnu"
    MAKEOPTS="-j2"(VBOX只能使用我一个核，所以加上这个参数)
    ACCEPT_kEYWORDS="~X86"

    #update
    GENTOO_MIRRORS="http://mirroers.163.com/gentoo \
        ftp://gentoo.anheng.com.cn/gentoo"
    SYNC="rsync://rsync.gentoo.org/gentoo-portage"

    INPUT_DEVICES="evdev synaptics virtualbox vmmouse"

    VIDEO_CARDS="vesa virtualbox"
    USE="-kde -qt3 -ipv6 -gpm 32bit -hal udev"

然后拷贝一个DNS信息

    # cp -L /etc/resolv.conf /mnt/gentoo/etc/

挂载/proc和/dev文件系统

    # mount -t proc none /mnt/gentoo/proc
    # mount --rbind /sys /mnt/gentoo/sys
    # mount --rbind /dev /mnt/gentoo/dev

chroot到新环境里

    # chroot /mnt/gentoo /bin/bash
    # env-update
    # source /etc/profile
    # export PS1="(chroot) $PS1"

OK，准备工作完成。
修改root密码

    #passwd root

增加一个用户

    #useradd -m -G users username

设置用户密码

    #passwd username

更新Portage树

    # emerge --sync

本地语言环境

    # nano -w /etc/locale.gen
    en_US ISO-8859-1
    en_US.UTF-8 UTF-8425
    zh_CN.GB18030 GB18030
    zh_CN.GBK GBK
    zh_CN.UTF-8 UTF-8
    zh_CN GB2312

更新环境

    # locale-gen

设置时区信息

    # cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

编辑/etc/fstab

    # nano -w /etc/fstab
    /dev/sda1 /boot ext2 noauto,noatime 1 2
    /dev/sda2 none swap sw 0 0
    /dev/sda3 / ext3 noatime 0 1
    /dev/cdrom /mnt/cdrom auto noauto,user 0 0
    shm /mnt/shm tmpfs nodev,nosuid,noexec 0 0

设主机名

    # nano -w /etc/conf.d/hostname
    HOSTNAME="VirtualBox"

网络

    # nano -w /etc/conf.d/net
    dns_domain_lo="localhost"
    nis_domain_lo="localhost"
    config_eth0=( "dhcp" )
    # rc-update add net.eth0 default

其他

    # nano -w /etc/hosts
    127.0.0.1 VirtualBox.localdomain VirtualBox localhost
    # nano -w /etc/conf.d/clock
    CLOCK="local"
    # emerge syslog-ng
    # emerge vixie-cron
    # emerge mlocate
    # USE="-gtk" emerge evms
    # emerge dhcpcd
    # rc-update add syslog-ng default
    # rc-update add vixie-cron default
    # emerge pciutils
    # emerge gentoolkit
    # emerge -avuDN world
    # revdep-rebuild && dispatch-conf
    # emerge layman
    # echo "source /usr/local/portage/layman/make.conf" >> /etc/make.conf
    # echo PORTDIR_OVERLAY=\"\" > /usr/local/portage/layman/make.conf
    # USE="-nowebdav" emerge subversion
    # layman -fa gentoo-china

下载安装内核源码

    # emerge gentoo-sources

查看内核源码版本并记住

    # ls -l /usr/src/linux

这里我显示的是

    lrwxrwxrwx    1 root   root    22 Oct 5 06:07 /usr/src/linux -> linux-2.6.24-gentoo-r5

检查下你的内核配置选项

    # cd /usr/src/linux
    # make menuconfig

注意，由于我使用的是 vbox3 的 pcnet32 虚拟网卡，所以这里要选中并编译进内核，否则编译出的内核启动不了网卡
开始编译,这个过程较长

    # make && make modules_install

（注：现在.33版本的kernel26，可以make localmodconfig来省却配置过程，再make menuconfig检查下即可

    # make mrproper && make clean
    # make localmodconfig
    # make menuconfig）

安装内核

    # cp arch/i386/boot/bzImage /boot/kernel-2.6.30-gentoo-r6

下载编译 grub

    # emerge grub

编辑配置文件

    # nano -w /boot/grub/grub.conf
    default 0
    timeout 30
    title Gentoo Linux 2.6.24-r5
    root (hd0,0)
    kernel /boot/kernel-2.6.24-gentoo-r5 root=/dev/sda3
    title Gentoo Linux 2.6.24-r5 (rescue)
    root (hd0,0)
    kernel /boot/kernel-2.6.30-gentoo-r6 root=/dev/sda3 init=/bin/bb

安装grub

    # grub --no-floppy
    grub> root (hd0,0)
    grub> setup (hd0)
    grub> quit
    # exit
    # cd ~
    #
    # umount /mnt/gentoo/boot /mnt/gentoo/dev /mnt/gentoo/proc /mnt/gentoo
    # reboot
