---
layout: post
title: "iPhone5 电信国行插移动卡解决来电显示通讯录名"
category: Other
tags: [iPhone, iOS]
---

家中的 iPhone 5 国行电信机，由于销号了，一直在吃灰状态中。今天喜闻盘古越狱发布，准备将该机子升级到最新版 iOS 9.0.2，插移动卡与卡贴给老妈用。遂拿起机子越了狱，可是，来电不显示通讯录名令老人很苦恼，研究了下，解决来电通讯录问题。

这里我们用到一个 OpenSSH 工具，手机及 PC 端都需要，由于已经越狱，手机端很方便的通过 **Cydia** 来搜索安装 OpenSSH，PC 端由于我现在使用的 OS X 系统，默认就自带的。这里要说明的是，手机端安装 OpenSSH 后，默认的 root 密码为 `alpine`，另外还有个用户 mobile 的密码也为 `alpine`，建议将这两个用户的密码都修改下，防止接入他人的 WiFi 下后被她们干坏事。

<!-- more -->
#### 解决 +86 问题，来电不显示通讯录名

下载附件： [Default.phoneformat.zip](http://cdn.09hd.com/images/2015/10/Default.phoneformat.zip)，解压缩后替换掉 `/System/Library/PrivateFrameworks/AppSupport.framework/Default.phoneformat`。

    ssh root@ip
    cd /System/Library/PrivateFrameworks/AppSupport.framework
    mv Default.phoneformat ./Default.phoneformat.1

PC 端另开一终端：

    scp /pc-path/Default.phoneformat root@ip:/System/Library/PrivateFrameworks/AppSupport.framework/

#### 解决 iMessage & FaceTime 激活

    ssh root@ip
    cd /System/Library/Carrier\ Bundles/iPhone

`ls` 可以看到很多运营商的配置，由于我现有插入卡贴后显示的运营商为 **vf nl 21.1**，对应目录下的 **Vodafone_nl.bundle**，ssh 进去备份下，同时将移动的配置复制过来。

    mv Vodafone_nl.bundle ./Vodafone_nl.bundle.1
    cp -r CMCC_cn.bundle ./Vodafone_nl.bundle
    cd Vodafone_nl.bundle
    ls
    Info.plist  carrier.plist  overrides_N41_N42.plist  overrides_N41_N42.pri  signatures  version.plist

需要将 Info.plist 移动到 PC 中，通过 vim 来编辑：

    scp root@ip:"/System/Library/Carrier\ Bundles/iPhone/Vodafone_nl.bundle/Info.plist" ~/Desktop

这里注意，scp 下带空格的路径，需要转义符与双引号。

由于 Info.plist 是二进制文件，直接用 vim 打开是乱码，可以通过 OS X 自带的 plutil 来转换，其用法

    plutil -convert xml1 file.plist    # 转换到 xml
    plutil -convert binary1 file.plist # 转换到两进制文件

在这里便是：

    plutil -convert xml1 ~/Desktop/Info.plist

修改第十行的 `com.apple.CMCC_cn` 为 `com.apple.Vodafone_nl`。

最后转换成两进制文件以后导入到原路径下：

    plutil -convert binary1 ~/Desktop/Info.plist
    scp Desktop/Info.plist root@ip:"/System/Library/Carrier\ Bundles/iPhone/Vodafone_nl.bundle/"

随后注销或重启即可：

    killall SpringBoard
    or
    reboot

至此，完成，老人家再也不用担心来电不显示通讯录了，再也不用担心在通讯录不加 86 直接拨出去电话为空号了。

#### 题外话

其实越狱后有很多玩法，很多人装很多插件，可是插件良莠不齐，不如直接命令行吧。OpenSSH 本身的安全性可以，可以使用目前通行的方法，禁用密码远程登录，禁用 root 的远程登录，使用用户 mobile 远程登录，采用 key 远程登录，然后安装 sudo，切换到 root 后，`visudo` 添加一行以每次 sudo 都需使用密码来防止一些应用偷偷的请求 sudo 权限：

    Defaults    env_reset,timestamp_timeout=0

同时，将 mobile 加入 wheel 用户组，visudo 开启 wheel 用户组的 sudo 权限：

    %wheel  ALL=(ALL) ALL       #去掉前面的 `#` 符号

作为一个资深的 Linux 小白，越狱后，当然安全是第一位的，随后才能愉快的玩耍了。
