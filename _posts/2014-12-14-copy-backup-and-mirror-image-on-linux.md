---
layout: post
title: "Linux 上的复制、备份与镜像"
category: Linux
tags: [CLI]
---

今天逛社区，偶尔发现有人询问大量小文件的移动复制与备份的问题，第一反应就是 `cpio`。因为以前备份 Gentoo 的时候，都是直接 `cpio` 来处理的，当然，tar、cp、dd 也可。至于 dump & restore 则由于文件系统适应性基本被放弃。

#### cp

`cp` 是常见的复制文件或目录的命令，是 **copy** 的缩写。譬如：

<!-- more -->
复制文件

    cp /path/a /path/b

复制文件夹

    cp -r /path/dir-a /path/dir-b

`cp` 的一些常见的参数：

- -a: 相当于 -pd --preserve=all
- -b: 类似 --backup，但不接受任何参数
- -d: 若存在软链接档案，则复制链接文件的属性，而非文件本身
- -f: 意为 force，强制覆盖
- -i: 在覆盖相同档案时询问
- -p: --preserve=mode,ownership,timestamps 复制时包括档案的属性
- -r: 递归参数，一般用于复制目录

详细的可以使用 `man cp` 来查看

#### tar

在 Linux 下发布产品时，最常使用的就是 `tar` 命令。她可以对档案进行打包备份操作。

一些常见的参数：

- -c: create，建立一个 tar 包
- -x: 解开一个压缩包
- -t: 查看 tar 里的文件
- -z: 使用 gzip 来压缩
- -j: 使用 bzip2 来压缩
- -J: 若存在 xz lzip lzma，则可以直接打包成 xz lzip lzma
- -v: 列出打包进程
- -f: tar 包名，-f 参数后需紧跟 tar 包名
- -p: 保留文件权限

常见的打包命令

    tar cvf /path/name.tar /path/name
    tar cvjf /path/name.tar.bz2 /path/name
    tar cvzf /path/name.tar.gz /path/name
    tar cvJf /path/name.tar.xz /path/name
    tar cvJf /path/name.tar.lzma /path/name

常见的解包命令

    tar xvf /path/name.tar
    tar xvjf /path/name.tar.bz2
    tar xvzf /path/name.tar.gz
    tar xvJf /path/name.tar.xz
    tar xvJf /path/name.tar.lzma

详细命令可以使用 `man tar` 来查看

#### dd

`dd` 在 Linux 环境下，也是非常有用的一个命令，她可以拷贝一个文件时进行指定的转换，尽管我只是用她来制作 u 盘镜像和备份 mbr 信息。

一些常见的参数：

- if=input: 读取输入文件
- of=output: 输出到文件
- bs=n: 设置输入输出的块大小为b bytes，譬如 bs=512、bs=1024M、bs=1G
- count=n: 拷贝 n 个输入的块

常见的命令

    dd if=/dev/sdx of=/path/to/mbr bs=512 count=1       # 备份 mbr
    dd if=/path/to/mbr of=/dev/sdx bs=512 count=1       # 恢复 mbr
    dd if=/dev/zero of=/path/swap bs=1M count=1024      # 创建个 1024MB 的文件 swap
    dd if=/path/to/image of=/dev/sdx bs=1M              # 制作 u 盘启动盘
    dd if=/dev/cdrom of=/path/to/iso                    # 制作 cd 的 iso 镜像

详细使用方法可以 `man dd`。

#### cpio

`cpio` 可以从 cpio、tar、pax、zip、jar、iso 等等格式的文件中存取档案，她是为备份而生的。

一些常用参数：

- -i: input 模式，还原备份档
- -o: output 模式，建立备份档
- -p: Pass-through 模式，直接将文件复制到目的目录
    - -t: 查看档案文件，仅使用在 i 模式下
    - -c: 使用旧的 POSIX portable 方式存储，o 模式专用
    - -v: 将存取过程打印到屏幕上
    - -d: 自动建立目录，i & p 模式专用
    - -A: 追加文件到备份档，o 模式专用
    - -B: 存取块大小设置成 5120 byte，o 模式专用
    - -C: 存取块大小，单位是 byte，O 模式专用
    - -L: 若存在软链接档案，则链接档案的属性，而非链接本身，o & p 模式专用
    - -m: 创建文件时，保留以前文件的修改时间，i & p 模式专用
    - -u: 置换所有文件，不论日期时间的新旧与否，皆不予询问而直接覆盖

常见的使用场景

    find /path/to/dir | cpio -ocvB > /path/to/name.cpio     # o 模式，打包目录成 cpio 档案
    find /. | cpio -ocvB > /path/to/system.cpio             # o 模式，备份整个系统
    cpio -ivt < /path/to/name.cpio                          # i 模式，列出 name.cpio 的文件
    cpio -idmv < /path/to/name.cpio                         # i 模式，还原 cpio 档案
    find /path/to/dir1 | cpio -pvd > /path/to/dir2          # p 模式，直接复制文件到新目录
