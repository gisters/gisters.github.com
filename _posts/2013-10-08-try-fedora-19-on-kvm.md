---
layout: post
title: "尝试 Fedora 19"
description: "重做记录，因上月重做系统的时候，[zim][1] 记录的笔记忘记备份而全部遗失。"
keywords: "fedora, 升级, 清理, LVM"
category: Linux
tags: [Fedora, KVM, LVM]
---

重做记录，因上月重做系统的时候，[zim][1] 记录的笔记忘记备份而全部遗失。-_-!!

有个问题折腾了很久，就是采用 [Fedora][2] 19 的 live 安装光盘安装好后，无法启动，一直卡在grub2之后的界面。当初没理解，以为 kernel 没装上，chroot 进去各种折腾，就是不行。遂装了 Fedora 18，从 Fedora 18 升上去了。后来 google 后才发现，原来只要[多等会][3]就能进界面了。

#### Fedora 18 升级 19

首先安装一个工具 `fedup`，通过 fedup 来升级：

    # yum install fedup -y
    # yum update
    # reboot
    # fedup-cli --reboot --network 19

<!-- more -->
期间会提示重启系统，重启后直接进入 fedup 引导，升级，按 `Esc` 可以查看升级进度。

重装引导：

    # yum reinstall grub2*
    # grub2-mkconfig -o /boot/grub2/grub.cfg

之前最好装个 yum 的插件：

    # yum install yum-fastestmirror

#### 清理

清除旧内核：

    # package-cleanup --oldkernel --count 1

清理一些没有被系统任何组件依赖的包，可以使用 `package-cleanup` 来查看：

    # package-cleanup --leaves

然后删除，也可以这样一步到位，多执行几次：

    # yum remove $(package-cleanup --leaves)

#### LVM 分区调整

Fedora 默认分区是采用 [LVM][4](Logical Volume Mnager) 的，由于我给 kvm guest 2GB 的内存，于是系统给我分配了 4GB 的 swap，有必要调整下，虚拟机嘛，测试用用的。

调整 lvm 分区前 root 只有 5.5GB 左右的空间，差点升级 Fedora19 的时候空间不足：

```
[root@localhost ~]# lvmdiskscan
 /dev/fedora/swap [       4.01 GiB]
 /dev/sda1        [     500.00 MiB]
 /dev/fedora/root [       5.48 GiB]
 /dev/sda2        [       9.51 GiB]
```

首先关闭swap：

    # swapoff -v /dev/fedora/swap

减少3.5GB后重新挂上：

    # lvm lvreduce /dev/fedora/swap -L -3.5G
    # mkswap /dev/fedora/swap
    # swapon /dev/fedora/swap

3.5GB的空间放到root上：

    # lvextend -L +3.5G /dev/fedora/root
    # resize2fs /dev/fedora/root

调整后的分区：

```
[root@localhost ~]# lvmdiskscan
 /dev/fedora/swap [     492.00 GiB]
 /dev/sda1        [     500.00 MiB]
 /dev/fedora/root [       9.03 GiB]
 /dev/sda2        [       9.51 GiB]
```

#### 一些细微的调整

    # hostnamectl set-hostname Virtio                                   # 设置hostname
    # yum install gnome-tweak-tool                                      # Gnome必备工具
    # yum install wqy-microhei-fonts                                    # 文泉驿微米黑字体
    # yum remove wqy-zenhei-fonts
    # yum localinstall kingsoft-office-9.1.0.4184-0.1.a12p1.i686.rpm    # 自动解决依赖

#### 待续......

[1]: http://zim-wik.org
[2]: http://fedoraproject.org
[3]: https://fedoraproject.org/wiki/Common_F19_bugs#Reboot_after_non-live_install_often_delayed_for_a_minute_or_so
[4]: http://www.sourceware.org/lvm2
