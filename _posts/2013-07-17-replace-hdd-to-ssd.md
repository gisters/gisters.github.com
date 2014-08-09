---
layout: post
title: "替换机械盘到固态盘"
description: "Linux 系统替换硬盘"
keywords: "SSD, 替换"
category: Linux
tags: [SSD]
---

又给机子加了 16G DDR3 2133 内存，同时购置了款 120GB 的三星 840 pro SSD。网上搜了一大圈，明确操作步骤后进行。

因为是双系统，所以对于 4K 对齐我是在 **Windows 7** 下进行的。拿出 **Windows 7** 安装光盘分区操作

```
/dev/sda1   100MB   # /boot
/dev/sda2   59GB    # windows
/dev/sda3   50GB    # /
/dev/sda4   10GB    # /home
```

其中 100MB 是 **Windows 7** 系统保留分区，创建 **C** 盘的时候自动创建了个 100MB 的小分区，正好拿来挂载 **/boot**。至于将 **/home** 独立出来，纯习惯使然，没有理由。

当然我的旧硬盘目录结构一样，只是 **/home** 有将近 900GB 的大小。

然后就简单了，**Windows 7** 安装完毕,这里有个小修改，默认 **Windows 7** 将 **Bootmgr** 等启动所需文件置入 100MB 的那个系统保留分区了（即 **/dev/sda1**），以管理员权限运行 **cmd**,进入 **C:\Windows\system32** 目录

<!-- more -->
```
bcdedit /set {bootmgr} device partition=C:
bcdedit /set {default} device partition=C:
bcdedit /set {default} osdevice partition=C:
```

将引导文件安装回 **Windows 7** 默认所在分区，接着打开磁盘管理工具，将 **C** 盘标注为活动分区，重启即可。

不作以上操作的话，**/dev/sda1** 文件系统改变后，**Windows 7** 将无法启动。

将旧硬盘装上，拿 **gentoo live usb** 启动后,格式化文件系统，挂载，拷贝：

```
mkfs.ext2 /dev/sda1 -L /boot
mkfs.ext4 /dev/sda3 -L /
mkfs.ext4 /dev/sda4 -L /home
mkdir /mnt/gentoo/sda1
mkdir /mnt/gentoo/sdb1
mount /dev/sda1 /mnt/gentoo/sda1
mount /dev/sdb1 /mnt/gentoo/sdb1
cp -rpf /mnt/gentoo/sdb1/* /mnt/gentoo/sda1
umount /mnt/gentoo/sdb1
umount /mnt/gentoo/sda1
```

`/dev/sda3`、`/dev/sda4` 同样拷贝，这里 cp 加了个 **-p** 参数，意即保留权限。最后 chroot 进新系统，重写下分区表即OK。

```
mount /dev/sda3 /mnt/gentoo
mount /dev/sda1 /mnt/gentoo/boot
chroot /mnt/gentoo /bin/bash
env-update && source /etc/profile
grub2-install --no-floppy /dev/sda
```

这里由于我的目录结构无变化，所以直接写分区表即 OK，如果你的分区表或文件系统有变化，那么调整下 `/etc/fstab` 即可。

一些小优化 [Gentoo wiki](http://wiki.gentoo.org/wiki/SSD) 上基本都有譬如挂载参数 `relatime`，譬如计划任务 `/sbin/fstrim -v`等等

期间也发生了些意外

譬如莫名其妙的将 **/boot** 下的 **boot**
软链接给删除了。导致开机启动找不到内核，最后重新加上就OK了：`mount /boot; cd /boot; ln -s . boot`。handbook 上写的是非独立 `/boot` 目录才需要这个软链接，而我是 `/boot` 挂载到 `/dev/sda1` 的，为什么也需要，不懂......

剩下的嘛就是 `/var/tmp/portage` 挂到 **tmpfs** 上，等等....

速度嘛，老实说，在 **Gentoo** 下感觉不出来，原来各种优化后就已经很快了- -!，不过启动 **Windows 7** 时，logo 还没闪现就已经启动到登录界面了，速度提升不少。
