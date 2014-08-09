---
layout: post
title: "Vsftpd Chroot 错误"
description: "Vsftpd 2.3.5 Chroot 错误"
keywords: vsftpd, chroot, 错误
category: Linux
tags: [Vsftpd, Chroot,]
---

其实早已解决，只是记录下，让更多人知道如何处理吧。

在 vsftpd 2.3.5 开始，用户打开选项 `chroot_local_user=YES` 后，会出现一个错误：

    500 OOPS: could not read chroot() list file:/etc/vsftpd/vsftpd.chroot_list

vsftpd 2.3.5 的 changelog 如下：

> Add stronger checks for the configuration error of running with a writeable
> root directory inside a chroot(). This may bite people who carelessly turned
> on chroot_local_user but such is life.

<!-- more -->
解决方法有两种

#### 1. 如果你的 ftp 根目录必须要可写，那么：

    allow_writeable_chroot=YES

#### 2. 如果你对你的 ftp 根目录没有写的要求，那么就去掉写权限：

    chmod a-w /var/ftp

当然，本人的方法是使用 virtual user，通过 virtual user 的配置文件中打开 allow_writeable_chroot=YES 来解决的。
