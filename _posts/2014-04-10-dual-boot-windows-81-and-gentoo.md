---
layout: post
title: "Windows 8.1 和 Gentoo 双启动"
description: "记录 Windows 8.1 和 Gentoo 双启动。"
keywords: "gentoo, windows, diskpart, syslinux"
category: Linux
tags: [Windows, Gentoo]
---

昨晚折腾了下 Windows 8.1 与 Gentoo 的双系统启动，采用的是 Syslinux，过程中出现一些问题，主要是记录下 Diskpart 的一些用法。

安装顺序有两种：

- 先安装 Windows 8.1，其次安装 Gentoo
- 先安装 Gentoo，其次安装 Windows 8.1

<!-- more -->
#### 一、 先 Windows 8.1，后 Gentoo

首先需要在 Windows 下打开`命令提示符(管理员)`，将启动分区设为 Windows 8.1 所在的分区。

    C:\Windows\Ststem32>bcdboot.exe C:\Windows /s C:
    已成功创建启动文件

好了，启动文件转移到 Windows 8.1 所在分区后，重启电脑。接着需要将隐藏分区干掉。`win+r`快捷键，输入`diskpart`回车。

首先输入 `list disk` 列出可用磁盘

    DISKPART> list disk
    磁盘 ###    状态            大小        可用        Dyn     Gpt
    --------    -------------   -------     -------     ---     ---
    磁盘 0      联机            119 GB      0B
    磁盘 1      联机            931 GB      1024 KB

选择磁盘 0

    DISKPART> select disk 0
    磁盘 0 现在是所选磁盘。

列出磁盘 0 所有分区

    DISKPART> list partition
    分区 ###        类型                大小        偏移量
    -------------   ----------------    -------     -------
    分区    1       主要                100 MB      1024 KB
    分区    2       主要                 58 GB       101 MB
    分区    3       主要                 48 GB        58 GB
    分区    4       主要                 11 GB       107 GB

选择分区1

    DISKPART> select partition 1
    分区 1 现在是所选分区。

删除隐藏分区

    DISKPART> delete partition override
    DiskPart 成功删除了所选分区。

好了，下面可以用 Gentoo 的 LiveUSB 工具去安装 Gentoo 了。

其实以上删除分区的步骤完全没必要做，完全可以在安装 Gentoo 的过程中格式化它，并通过 `cfdisk` 来将该隐藏分区设为`非活动分区`。

#### 二、 先 Gentoo 后 Windows 8.1

单独安装 Gentoo 与 Windows 8.1 的过程就不多说了。安装好 Windows 8.1 之后，需要 Gentoo 的 LiveUSB 去引导，随后格式化隐藏分区

    # mkfs.ext2 /dev/sda1

然后就各种mount后chroot

    # mount /dev/sda3 /mnt/getoo
    # mount /dev/sda1 /mnt/gentoo/boot
    # mount -t proc none /mnt/gentoo/proc
    # mount -o bind /dev /mnt/gentoo/dev
    # chroot /mnt/gentoo /bin/bash
    # env-udate && source /etc/profile

随后按照文章[《使用 Syslinux 替换掉 Grub》]({% post_url 2013-09-08-syslinux-instead-of-grub %})重装下 Syslinux 就完工。
