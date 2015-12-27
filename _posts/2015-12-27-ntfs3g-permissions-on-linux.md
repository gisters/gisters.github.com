---
layout: post
title: "Linux 系统的 NTFS-3G 权限"
category: Linux
tags: [NTFS]
---

这个知识点居然没有记录，果然时间长久以后就会遗忘，遂记录。

一般情况下，我们在 Linux 下挂载 ntfs，是安装 ntfs-3g 后进行的，即：

    # mount -t ntfs-3g /dev/sdb1 /mnt/data

这样临时解决下可以，但是会涉及到权限问题，就是说，/mnt/data 下的所有文件都必须 root 权限。于是我们加入一些参数让当前用户也可以访问修改：

    # mount -t ntfs-3g -o uid=username,gid=users /dev/sdb1 /mnt/data      # uid 为当前用户名，gid 为users 用户组

<!-- more -->
一般人到此为止，临时需求嘛，不过强迫症表示，看到挂载的分区，所有文件都是 777 权限表示非常不爽，于是：

    # mount -t ntfs-3g -o uid=username,gid=users,umask=022 /dev/sdb1 /mnt/data

umask 表示，去掉的权限。

#### SUGO

说到这里，补充下 Linux 的权限表示为 **SUGO**，第一位 s 表示 `suid`、`sgid`、`sticky`,一般用不到，后面附录顺带提一下，这里说下 **UGO**，三个位置，每个都用 `rwx` 来表示所拥有的权限，`rwx` 分别表示，读、写、执行（访问），分别用数字 4、2、1来表示，对应关系如下：

|拥有者(U)|群组(G)|其他(O)|相应数字权限|
|:---:|:---:|:---:|:---:|
|r  w  x|r  w  x|r  w  x|权限|
|4  2  1|4  2  1|4  2  1|777|
|4  2  1|4  0  1|4  0  1|755|
|4  2  1|4  2  1|4  0  0|774|
|4  2  1|0  0  0|0  0  0|700|
|4  2  0|4  0  0|4  0  0|644|

如此就明了，`644` 表示 u 拥有读写权限，g 拥有只读权限，o拥有只读权限。

#### umask、fmask、dmask

- umask -- 过滤目录与文件的权限
- fmask -- 过滤文件的权限
- dmask -- 过滤目录的权限

描述很清楚了，如果你想拥有 755 权限，那么 权限 = 777 - 022，每一位相减，得到 755，就是说，umask = 022 后，实际权限为 755，fmask、dmask 同理，如果想拥有 644 呢， 644 = 777- 133......

现在清楚了，上文我们设置的 umask=022，实际上，所有文件文件夹的权限都被设置为 755 了，这对于拥有 wine 的用户来说，不是一个好消息，那么我们就分别来设置 fmask 与 dmask。

    # mount -t ntfs-3g -o uid=username,gid=users,fmask=133,dmask=022 /dev/sdb1 /mnt/data

于是，我们最终得到的文件权限为 644，文件夹权限为 755，以上写入 fstab 中一开机就挂载的话，就是如此：

    /dev/sdb1 /mnt/data ntfs-3g uid=username,gid=users,fmask=133,dmask=022 0 0

#### 附录

SUGO 中的 S，表示 suid、guid、sticky 之和，而 suid、guid、sticky 分别用 4、2、1 来表示相关权限。

即：

|suid+guid+sticky|之和|表示|
|:---:|:---:|---|
|4 + 2 + 1|7|suid + guid + sticky|
|4 + 2 + 0|6|suid + guid|
|4 + 0 + 1|5|suid + sticky|
|4 + 0 + 0|4|suid|
|0 + 2 + 1|3|guid + sticky|
|0 + 2 + 0|2|guid|
|0 + 0 + 1|1|sticky|
|0 + 0 + 0|0|无|

应用场景很多人都提过的，/etc/shadow 为 root 读写，普通用户也可以使用 `passwd` 来更改自己密码的原因。

- suid: 只能作用在可执行文件上，当其他用户运行改执行文件时，会临时拥有该文件所有者的权限
- sgid: 可以作用在目录或可执行文件上，也同样，临时拥有该文件或文件夹所有者的权限
- sticky: 只能作用在目录，可以用来防删除，一旦拥有 sticky 属性是，除了目录所有者与 root，其他任何人都无法删除该目录下文件或子目录。

参考：

- <https://wiki.archlinux.org/index.php/NTFS-3G#Basic_NTFS-3G_options>
- <http://thegeekdiary.com/what-is-suid-sgid-and-sticky-bit/>

