---
layout: post
title: "阿里云主机挂载数据盘"
description: "在阿里云主机中挂载数据盘"
keywords: 阿里云, 主机, 挂载, 数据盘
category: Linux
tags: [aliyun, Server]
---

取得了一台阿里云主机用于放置 `gentoo.org.cn` 的信息，之前试用过，效果不错，不过之前没有申请数据盘的经验。这次发现，阿里云主机默认是不会帮你挂载数据盘的，数据盘命名也与传统不太一样。具体的可以用 `fdisk -l` 来查看：

```
# fdisk -l

Disk /dev/hda: 21.4 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes

   Device Boot      Start         End      Blocks   Id  System
/dev/hda1   *           1        2610    20964793+  83  Linux

Disk /dev/xvdb: 21.4 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes

Disk /dev/xvdb doesn't contain a valid partition table
```

<!-- more -->

看到了吧，数据盘在 `/dev/xvdb` 上，且没有文件系统，`/dev/hda1` 为系统盘。千万不要将重要数据安装到 `/dev/hda1` 中去哦，否则出现一些重大事故，哭都来不及。一般用阿里云主机的基本都是建立网站的，那么如果安装 centos 的话，就将 `/dev/xvdb1` 挂载到 `/var/www` 目录中去吧，具体操作如下：

1. `ssh name@aliyunhost`
2. 切换到 `root`
3. 运行 `fdisk /dev/xvdb` 命令，按提示进行分区操作
4. 格式化分区：`mkfs.ext3 /dev/xvdb1`
5. 挂载目录：`mount /dev/xvdb1 /var/www`
6. 修改fstab使得开机即挂载：`/dev/xvdb1    /var/www    ext3         defaults     0 0`

当然你也可以使用最新的 `ext4` 文件系统。挂载目录根据你的用途自行决定。
