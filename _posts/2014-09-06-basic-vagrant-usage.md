---
layout: post
title: "Vagrant 使用"
description: "Vagrant 本质上来说，是对 virtualbox，vmware，kvm 等镜像的管理操作，是一个中间层技术。使用它的前提是你本机必须有 virtualbox，vmware，kvm 等虚拟机。"
keywords: "vagrant, 配置, 使用"
category: Other
tags: [Vagrant, Virtual]
---

Vagrant 本质上来说，是对 virtualbox，vmware，kvm 等镜像的管理操作，是一个中间层技术。使用它的前提是你本机必须有 virtualbox，vmware，kvm 等虚拟机。

Vagrant 的安装非常简单，各个 linux 发行版可以直接通过包管理安装，OS X 也可以通过 Homebrew Cask来安装。

    $ brew cask install vagrant

<!-- more -->
建立你自己的 Vagrant 也非常简单，

    $ mkdir /your/path/vagrant_name; cd /your/path/vagrant_name; vagrant init

此命令会在 `/your/path/vagrant_name` 目录下建立 `Vagrantfile` 基础配置文件，你可以通过 git 等方式来分享。下面在此目录下执行 `vagrant box add xxx` 去拉一个现成的镜像下来，鄙人还是习惯拉 Gentoo 的一个 vbox 镜像。当然下面例子中的 Gentoo 镜像存放在 dropbox 中，需要一些**你懂得**操作。

    $ vagrant box add gentoo https://dl.dropboxusercontent.com/s/xfl63k64zliixid/gentoo-20131029-i686.box
    ==> box: Adding box 'gentoo' (v0) for provider:
    box: Downloading: https://dl.dropboxusercontent.com/s/xfl63k64zliixid/gentoo-20131029-i686.box
    ==> box: Successfully added box 'gentoo' (v0) for 'virtualbox'!

镜像拉下来后，就可以启动了

    $ vagrant up
    Bringing machine 'default' up with 'virtualbox' provider...
    ==> default: Importing base box 'gentoo'...
    ==> default: Matching MAC address for NAT networking...
    ==> default: Setting the name of the VM: vagrant_default_1410100872394_79587
    ==> default: Clearing any previously set forwarded ports...
    ==> default: Clearing any previously set network interfaces...
    ==> default: Preparing network interfaces based on configuration...
        default: Adapter 1: nat
    ==> default: Forwarding ports...
        default: 22 => 2222 (adapter 1)
    ==> default: Booting VM...
    ==> default: Waiting for machine to boot. This may take a few minutes...
        default: SSH address: 127.0.0.1:2222
        default: SSH username: vagrant
        default: SSH auth method: private key
        default: Warning: Connection timeout. Retrying...
    ==> default: Machine booted and ready!
    ==> default: Checking for guest additions in VM...
    ==> default: Mounting shared folders...
        default: /vagrant => /Users/Havee/Documents/git/vagrant

嗯，你的虚拟机已经起来了，连接的话，直接 `vagrant ssh` 即可

    $ vagrant ssh
    vagrant@local ~ $
    vagrant@local ~ $ uname -a
    Linux local 3.10.7-gentoo-r1 #1 SMP Wed Oct 30 20:15:42 UTC 2013 i686 Intel(R) Core(TM) i5-3470 CPU @ 3.20GHz GenuineIntel GNU/Linux

随后，你就可以随意的根据你的习惯，去部署你的一切。当然如果你觉得 vbox 笨重的话，Vagrant + CoreOS + docker 是一个非常好的解决方案。

一些 Vagrant 常用命令

    $ vagrant init                  # 初始化
    $ vagrant up                    # 启动
    $ vagrant halt                  # 关闭
    $ vagrant reload                # 重启
    $ vagrant suspend
    $ vagrant resume
    $ vagrant ssh                   # ssh 连接到虚拟机
    $ vagrant status                # 虚拟机状态
    $ vagrant destroy               # 销毁
    $ vagrant package               # 打包
    $ vagrant box add name url      # 添加镜像
    $ vagrant box list              # 列表
    $ vagrant box remove name       # 移除镜像
    $ vagrant box repackage         # 重新打包

待续……

参考： [https://docs.vagrantup.com/v2/](https://docs.vagrantup.com/v2/)
