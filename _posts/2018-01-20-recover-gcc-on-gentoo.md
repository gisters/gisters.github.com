---
layout: post
title: "Gentoo 上恢复 GCC"
category: Linux
tags: [Gentoo, GCC]
---

最近好久没升级 Gentoo，冒出一堆新 profile，于是手贱的 `eselect profile set xxx`，呃，选了一个 no-multilib。

奇妙的是，我居然按照提示重新编译了整个 world。

懵逼了，回不去 multilib 了，手贱要剁手。

Gentoo 什么都好，就是一旦完成了 no-multilib，就再也无法简单的切换到 multilib 了。

不过，天无绝人之路，办法总是有的。stage 3 中就有现成的 GCC 的二进制版，我们需要的只是打包一下，copy 到原系统安装。

<!-- more -->

#### 简单操作

##### 提取 GCC

创建一个目录，如同第一次安装 Gentoo 时候那样做，下载最新的 stage 3，chroot 进去，打包 GCC。

    mkdir /mnt/gentoo
    cd /mnt/gentoo
    wget http://mirrors.163.com/gentoo/releases/amd64/autobuilds/current-stage3-amd64-systemd/stage3-amd64-systemd-20180113.tar.bz2
    tar xvpf stage3-amd64-systemd-20180113.tar.bz2
    chroot /mnt/gentoo /bin/bash
    quickpkg sys-devel/gcc
    exit

##### 安装二进制 GCC

将打包的二进制 GCC 拷贝到原来系统对应的目录，安装他。

    mkdir -p /usr/portage/packages/sys-devel
    cp -v /mnt/gentoo/usr/portage/packages/sys-devel/gcc-*.tbz2 /usr/portage/packages/sys-devel
    emerge -1 --usepkgonly gcc

##### 重建工具链

选择刚才安装的 GCC，并且重建下工具链，呃，这一步之前，可能需要重新编译下 `sys-apps/sandbox`。

    gcc-config -l
    gcc-config N
    source /etc/profile
    FEATURES='-sandbox -usersandbox' emerge -1 sys-apps/sandbox
    emerge -1 sys-devel/libtool sys-devel/binutils sys-libs/glibc

#### 通用操作

一般情况下，问题至此解决。

不过呢，实际情况是非常复杂的，譬如 stage 3 的 keywords 并非 `~x86 ~amd64`，而你的系统可能就是 `~x86 ~amd64`，版本不一致。没关系，chroot 之前当作安装全新的 Gentoo 那样去操作，chroot 之后升级到最新版本呢，然后提取相关二进制包。

创建chroot临时目录，下载最新的 stage 3 与 portage。

    mkdir /mnt/gentoo
    cd /mnt/gentoo
    wget http://mirrors.163.com/gentoo/releases/amd64/autobuilds/current-stage3-amd64-systemd/stage3-amd64-systemd-20180113.tar.bz2
    tar xvpf stage3-amd64-systemd-20180113.tar.bz2
    wget http://mirrors.163.com/gentoo/releases/snapshots/current/portage-latest.tar.bz2
    tar xvjf /mnt/gentoo/portage-latest.tar.bz2 -C /mnt/gentoo/usr

拷贝一些必要的文件，为了 USE flage 一致。

    cp -rf /etc/portage /mnt/gentoo/etc/
    cp -L /etc/resolv.conf /mnt/gentoo/etc/

挂载必要的目录，否则一些包可能无法顺利编译，譬如 **sys-libs/glibc**

    mount -t proc none /mnt/gentoo/proc
    mount --rbind /sys /mnt/gentoo/sys
    mount --rbind /dev /mnt/gentoo/dev
    chroot /mnt/gentoo /bin/bash

chroot 之后呢，可以将对应的版本升级到最新版。

    eselect profile list
    eselect profile set N
    env-update && source /etc/profile
    emerge --sync
    emerge -avu sys-devel/gcc
    gcc-config -l
    gcc-config N
    source /etc/profile

是的，按照全新系统的步骤去操作，可以更新整个 portage 到最新，编译你需要的工具链版本，打包二进制。当然，你可以一次性的在这里将其他的包也编译成二进制，譬如 **sys-apps/sandbox**，**sys-devel/libtool**，**sys-devel/binutils**，**sys-libs/glibc**等。

    emerge -1 sys-apps/sandbox sys-devel/libtool sys-devel/binutils sys-libs/glibc
    quickpkg sys-devel/gcc sys-apps/sandbox sys-devel/libtool sys-devel/binutils sys-libs/glibc

随后，推出 chroot 环境，将二进制包拷贝到原系统中，通过 `--usepkgonly` 安装到原系统中即可。
