---
layout: post
title: "Vagrant 简单使用"
description: "Vagrant 本质上来说，是对 virtualbox，vmware，kvm 等镜像的管理操作，是一个中间层技术。使用它的前提是你本机必须有 virtualbox，vmware，kvm 等虚拟机。"
keywords: "vagrant, 配置, 使用"
category: Other
tags: [Vagrant, Virtual]
---

#### 一. 安装

Vagrant 本质上来说，是对 virtualbox，vmware，kvm 等镜像的管理操作，是一个中间层技术。使用它的前提是你本机必须有 virtualbox，vmware，kvm 等虚拟机。

Vagrant 的安装非常简单，各个 linux 发行版可以直接通过包管理安装，OS X 也可以通过 Homebrew Cask来安装。

    $ brew cask install vagrant

<!-- more -->
建立你自己的 Vagrant 也非常简单，

    $ mkdir /your/path/vagrant_name; cd /your/path/vagrant_name; vagrant init gentoo

此命令会在 `/your/path/vagrant_name` 目录下建立 **Vagrantfile** 基础配置文件，你可以通过 git 等方式来分享。下面在此目录下执行 `vagrant box add xxx` 去拉一个现成的镜像下来，鄙人还是习惯拉 Gentoo 的一个 vbox 镜像。当然下面例子中的 Gentoo 镜像存放在 dropbox 中，内地的网络环境 **你懂得**，故需要一些 **你懂得** 操作，这里不做介绍了。

```console
$ vagrant box add gentoo https://dl.dropboxusercontent.com/s/xfl63k64zliixid/gentoo-20131029-i686.box
==> box: Adding box 'gentoo' (v0) for provider:
box: Downloading: https://dl.dropboxusercontent.com/s/xfl63k64zliixid/gentoo-20131029-i686.box
==> box: Successfully added box 'gentoo' (v0) for 'virtualbox'!
```

除了直接去网址下载，也可以直接下载 **Vagrant Cloud** 中的镜像

    $ vagrant box add d11wtq/gentoo

具体需要什么镜像可以去如下网址：

- <http://www.vagrantbox.es>
- <https://vagrantcloud.com/discover/featured>

#### 二. 运行

镜像拉下来后，就可以启动了

```console
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
```

嗯，你的虚拟机已经起来了，连接的话，直接 `vagrant ssh` 即可

    $ vagrant ssh
    vagrant@local ~ $

```console
vagrant@local ~ $ uname -a
Linux local 3.10.7-gentoo-r1 #1 SMP Wed Oct 30 20:15:42 UTC 2013 i686 Intel(R) Core(TM) i5-3470 CPU @ 3.20GHz GenuineIntel GNU/Linux
```

随后，你就可以随意的根据你的习惯，去部署你的一切。当然如果你觉得 vbox 笨重的话，Vagrant + CoreOS + docker 是一个非常好的解决方案。

#### 三. 配置

**Vagrantfile** 文件就是基础配置文件下面列出一些常用的配置项

##### a. 基础设定

```ruby
VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.vm.box = "gentoo"
    config.vm.box_check_update = false
    config.vm.network "forwarded_port", guest: 80, host: 8080   # 端口转发
    config.vm.network "private_network", ip: "192.168.33.10"    # 或 config.vm.network "public_network"，顾名思义
    config.ssh.forward_agent = true
    config.vm.synced_folder "./", "/vagrant"
end
```

一目了然的配置，名称，更新，端口转发，网络，ssh 以及共享目录。

##### b. Provider

由于 VirtualBox 免费，且跨平台，安装方便，很多人都使用 VirtualBox。以 VirtualBox 为例：

```ruby
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    ......
    config.vm.provider "virtualbox" do |vb|
        vb.gui = true
        vb.name = "gentoo"
        vb.memory = 1024
        vb.cpus = 2
        vb.customize ["modifyvm", :id, "--cpuexecutioncap", "80"]
    end
    ......
end
```

以上配置也是一目了然，VMWare Fusion 呢

```ruby
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    ......
    config.vm.provider "vmware_fusion" do |vb|
        vb.gui = true
        vb.vmx["memsize"] = "1024"
        vb.vmx["numvcpus"] = "2"
    end
    ......
end
```

其他的虚拟机，请阅读文档： <http://docs.vagrantup.com/v2/providers/index.html>

##### c. 多机环境

尽管我觉得多个 box 运行一个环境，非常的奢侈，与其跑多个虚拟机，倒不如用 docker 来管理。但谁知道大家的 cpu, ram 是否叼炸天呢。各有所好嘛......

```ruby
config.vm.define :app1 do |app1|
    app1.vm.box = "app1"
    app1.vm.network "private_network", ip: "192.168.33.10"
    app1.memory = 512
    app1.cpus = 2
    app1.vm.customize [ "modifyvm", :id, "--name", "app1", "--cpuexecutioncap", "50" ]
end
config.vm.define :app2 do |app2|
    app2.vm.box = "app2"
    app2.vm.network "private_network", ip: "192.168.33.11"
    app2.memory = 512
    app2.cpus = 1
    app1.vm.customize [ "modifyvm", :id, "--name", "app2", "--cpuexecutioncap", "40" ]
end
......
```

启动

    $ vagrant up app1
    $ vagrant up app2

##### d. 常用命令

    $ vagrant init                  # 初始化
    $ vagrant up                    # 启动
    $ vagrant halt                  # 关闭
    $ vagrant reload                # 重启
    $ vagrant suspend
    $ vagrant resume
    $ vagrant ssh                   # ssh 连接到虚拟机
    $ vagrant status                # 虚拟机状态
    $ vagrant destroy
    $ vagrant package               # 打包
    $ vagrant box add name url      # 添加镜像
    $ vagrant box list              # 列表
    $ vagrant box remove name       # 移除镜像
    $ vagrant box repackage         # 重新打包

#### 四. 打包环境

万事俱备，公司内部要统一开发环境，打包出来让同事在统一环境下开发吧，以后不会出现 **我机子上没这个问题呀** 这种说不清道不明的状况了。

打包，当然是退出你的虚拟机后再操作的。

```console
$ vagrant package
==> gentoo: Attempting graceful shutdown of VM...
==> gentoo: Clearing any previously set forwarded ports...
==> gentoo: Exporting VM...
==> gentoo: Compressing package to: /Users/Havee/Documents/git/vagrant/package.box
```

将 package.box 拿给同事吧，让他们加入该 box，你们的开发环境就完全一致了

    $ vagrant box add gentoo /path/package.box

参考： <https://docs.vagrantup.com/v2/>
