---
layout: post
title: "Linux 解决文件名乱码"
description: "在 Linux 下解决文件名乱码"
keywords: Linux, 乱码, 文件名
category: Linux
tags: [Garbled]
---


在 Linux 下学习工作，不可避免的遭遇到文件名乱码（不是内容乱码）的问题以下有几个解决方案

### 1. 删除

如果你仅仅是删除乱码文件：通过 `ls -li` 来查询乱码文件的索引号再删除

    $ ls -li *.mp3
    2428 -rw-r--r-- 1 havanna users 0 Jun 26 11:24 ????.mp3
    $ find . -inum 2428 -exec rm {} +

<!-- more -->
or

    $ rm -- ????.mp3

### 2. 转码

一般情况下，我们碰到的乱码基本原来都是 GBK 编码的，所以这里我们使用个工具 convmv 来解决

    $ ls -l
    total 0
    -rw-r--r-- 1 havanna users 0 Jun 26 11:30 ????.mp3
    $ convmv -f GBK -t UTF-8 --notest *.mp3
    mv "./����.mp3"	"./您好.mp3"
    Ready!
    $ ls -l
    total 0
    -rw-r--r-- 1 havanna users 0 Jun 26 11:30 您好.mp3

### 3. 改名

对不知道原来文件名编码的处理：改成其他文件名

    $ find . -inum 2428 -exec mv {} file.mp3 \;
    $ ls
    file.mp3

如果你有更好的解决方法，请告知
