---
layout: post
title: "打包与解包安卓 img 镜像"
category: Linux
tags: [Android]
---

今天偶尔看到有人问起，如何解包打包安卓的各类 img 文件。下面介绍下

首先可以去 <https://android.googlesource.com/platform/system/extras> 下载工具源代码

    $ git clone https://android.googlesource.com/platform/system/extras /your/path

checkout 你需要的版本，譬如 android-5.1.1_r13

    $ cd /your/path/extras
    $ git checkout android-5.1.1_r13

<!-- more -->

编译 simg2img，这里你需要 gcc 工具，linux 用户基本都由现成的

    $ cd /your/path/extras/ext4_utils
    $ gcc -o simg2img -lz sparse_crc32.c simg2img.c
    $ gcc -o make_ext4fs -lz make_ext4fs_main.c make_ext4fs.c ext4fixup.c ext4_utils.c allocate.c backed_block.c output_file.c contents.c extent.c indirect.c uuid.c sha1.c sparse_crc32.c wipe.c

当前目录会生成 simg2img 与 make_ext4fs 两进制文件

下面解包开始，以 system.img 为例

    $ ./simg2img system.img system.ext4

随后，可以直接挂载，需要 root 权限

    # mkdir /your/path/system-data
    # mount -t ext4 -o loop system.ext4 /your/path/system-data

打包的话，则是如此操作

    $ ./make_ext4fs -s -l 512M -a system system-data.img /your/path/system-data

或者使用源代码中的脚本

    $ cd /your/path/extras
    $ PATH="$PATH:$(pwd)/ext4_utils/make_ext4fs" ./ext4_utils/mkuserimg.sh -s /your/path/system-data system-data.img ext4 /tmp 512M

最后转换下

    $ ./simg2img system-data.img system.img

OK，你可以刷入你的手机了，Good luck!
