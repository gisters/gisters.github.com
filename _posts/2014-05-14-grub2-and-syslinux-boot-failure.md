---
layout: post
title: "grub2 或 syslinux 启动失败后"
description: ""
keywords: "grub2, syslinux, 失败"
category: Linux
tags: [Grub, Syslinux]
---

经常升级内核的情况下，偶尔还是会出现一些问题的。

譬如 grub2-mkconfig 的时候，`/boot` 是单独的分区，且忘记挂载了。

譬如 Gentoo 下 copy 新内核到 `/boot` 目录的时候，忘记修改配置了，且手快的删除了旧内核。

更甚者，直接忘记 copy 新内核到 `/boot` 下，就兴冲冲的去修改 Grub2 or Syslinux 的配置去了。- -!

等等。

<!-- more -->
此时就需要手动在 bootloader 界面去输入了

#### Grub2

```
grub> set root=hd0,0
grub> linux /boot/3.14.4-gentoo root=/dev/sda2
grub> boot
```

`hd0,0` 修改为自己的 grub2 所在分区。`sda2` 则为根分区所在的目录，忘记路径的时候可以按 `tab` 来提示。

#### Syslinux

syslinux 就相对简单了，直接

    boot: LINUX /boot/3.14.4-gento root=/dev/sda2

当然，也要善用 `tab`。

如果你连 Grub2 或 Syslinux的界面也进不去的话，那么恭喜，通过 liveusb 进去 chroot 后，重装这两者之一吧。

Goodlucky!
