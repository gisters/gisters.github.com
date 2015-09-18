---
layout: post
title: "压缩 KVM 的 qcow2 镜像文件"
category: Linux
tags: [KVM, Virtual]
---

首先，需要对虚拟机系统对剩余空间写零操作：

    # dd if=/dev/zero of=/zero.dat

删除 zero.dat：

    # rm /zero.dat

关闭虚拟机，进入虚拟机镜像文件的目录，通过 qemu-img 的 convert 来操作：

    $ qemu-img convert -O qcow2 /path/old.img.qcow2 /path/new.img.qcow2

<!-- more -->
随后改变 VM 的配置测试 new.img.qcow2，正常的话，删除 old.img.qcow2 即可。

附带说一下 raw 格式的压缩。

之前也进虚拟机系统对剩余空间写零操作，随后删除 zero.dat，关闭虚拟机。最后通过 cp 的稀疏复制，把 零 全部释放

    $ cp --sparse=always /path/old.raw /path/new.raw

raw 镜像比 qcow2 镜像速度略快，但是 qcow2 镜像有增量功能，一半情况下，我们都采用 qcow2 镜像格式，qemu-img 的 convert 也能转化 raw 成 qcow2：

    $ qemu-img convert -c -f raw -O qcow2 /path/old.raw /path/new.qcow2
