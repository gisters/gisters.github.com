---
layout: post
title: "升级 CentOS 内核至 4.9"
category: Linux
tags: [CentOS, BBR]
---

#### 介绍

Linux Tovalds 于 2016 年 12 月 11 日发布了 Kernel 4.9 正式版本，带来了一些令人激动的特性以及一些驱动的更新。Linux 稳定内核维护者 Greg Kroah-Hartman 也早已宣布下一个长期支持版（LTS）内核将是 Linux 4.9。来自 Google 的 BBR (Bottleneck Bandwidth and RTT) TCP 拥塞控制 （congestion control） 算法也在这个版本并入了主线。为了体验 BBR TCP，迫不及待的需要将 CentOS 7 的内核升级至该版本。具体的更新可以查阅：[Linux Kernel 4.9 release notes](https://lkml.org/lkml/2016/12/11/102)。

#### 安装

要在 CentOS 上安装最新的内核版本，我们需要增加一个 [ELRepo](http://elrepo.org/tiki/tiki-index.php) 源。

首先，让我们添加 ELRepo GPG key：

    rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org

<!-- more -->

为 RHEL-6，SL-7，CentOS-7 源：

    rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm

老版本也可以享受 kernel 4.9，譬如为 RHEL-6，SL-6，CentOS-6 添加 ELRepo 源：

    rpm -Uvh http://www.elrepo.org/elrepo-release-6-6.el6.elrepo.noarch.rpm

为 RHEL-5，SL-5，CentOS-5 添加 ELRepo 源：

    rpm -Uvh http://www.elrepo.org/elrepo-release-5-5.el5.elrepo.noarch.rpm

当然，别忘了 fastestmirror 还是需要的

    yum install yum-plugin-fastestmirror

最后，安装 kernel 4.9

    yum --enablerepo=elrepo-kernel install kernel-ml

当然，将 kernel-ml 选为第一启动，首先查看系统的内核以及顺序

    awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg

看下你当前默认启动项

    grub2-editenv list

将 kernel-ml 版本的内核设置为默认启动内核

    grub2-set-default N

以后升级内核默认启用 kernel-ml，编辑文件 `/etc/sysconfig/kernel`

    DEFAULTKERNEL=kernel-ml

同时编辑文件 `/etc/sysconfig/kernel`，在 `[elrepo-kernel]` 下

    enabled=1

重启后，通过 `uname -a` 查看内核是否切换到 4.9，譬如我的

    $ uname -a
    Linux box 4.9.0-1.el7.elrepo.x86_64 #1 SMP Sun Dec 11 15:43:54 EST 2016 x86_64 x86_64 x86_64 GNU/Linux

#### 开启 BBR TCP

    echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
    echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf

重启后，首先 `uname -a` 看下内核是否切换到 4.9，然后执行下面明亮查看内核是否开启 TCP BBR：

    sysctl net.ipv4.tcp_available_congestion_control
    sysctl net.ipv4.tcp_congestion_control

查看 tcp_bbr 模块是否加载：

    lsmod | grep tcp_bbr

##### Gentoo Kernel

打开 BBR TCP 开关，并将默认 TCP 拥塞控制设为 BBR：

    Networking support  --->
        Networking options  --->
            -*- TCP/IP networking
            [*]   TCP: advanced congestion control  --->
                <*>   BBR TCP
                Default TCP congestion control (BBR)  --->
                    ( ) Cubic
                    (X) BBR
                    ( ) Reno

#### 题外话

只说下体验，kimsufi 法国的盒子，本来上行在 4MB 以下，现在基本维持在 8MB 以上。

同时说一下，阿里云的 vps，CentOS 7 可以升级到 kernel 4.9，其他的发行版没试过。

参考：<https://www.ostechnix.com/install-linux-kernel-4-9-centos-ubuntu/>
