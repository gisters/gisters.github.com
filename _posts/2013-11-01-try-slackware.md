---
layout: post
title: "初试 Slackware"
description: "又是周五了，享受没有饭局的时光，不过也着实无聊了点，遂拿起 Slackware 尝试了下，做下记录。"
keywords: "slackware, sbopkg"
category: "Linux"
tags: [Slackware]
---

又是周五了，享受没有饭局的时光，不过也着实无聊了点，遂拿起 [Slackware](http://www.slackware.com/) 尝试了下，做下记录。

安装就略过了，安装完毕后遇到的一些问题。

Slackware 官方源的软件包数量实在是太少了点，作为中文用户，最基本的 [Fcitx](http://fcitx-im.org/) 也没有，放 [G](https://www.google.com) 搜索了下，都是自己下源码编译安装教程（这也要教程？！），难道古老的 Slackware 到现在都没有一个完善的管理器？再搜，得知，[sbopkg](http://www.sbopkg.org/)，解决了自动下载源码并编译安装的问题，不过弊端是无法自动解决依赖问题，- -!

<!-- more -->
方法：

    $ wget http://sbopkg.googlecode.com/files/sbopkg-0.36.0-noarch-1_cng.tgz
    $ su -l -c "installpkg sbopkg-0.36.0-noarch-1_cng.tgz"
    or
    # installpkg sbopkg-version-noarch-1_cng.tgz

随后运行 `sbopkg` 又有个问题，同步 [源](http://slackbuilds.org/repository/14.0/) 所耗费的时间也实在的太恐怖了点，还时不时的同步失败，无语。

继续放 G 搜，原来 github 上有[镜像](https://github.com/Ponce/slackbuilds)，还好，我本地到 github 的速度不算太慢，方法也很简单

编辑 `/etc/sbopkg/sbopkg.conf`，添加如下

    REPO_BRANCH=${REPO_BRANCH:-14.0}
    REPO_NAME=${REPO_NAME:-SBo}

再同步下

    $ su -l -c "sbopkg -r"

随后安装软件，譬如 Fcitx

    $ su -l -c "sbopkg -i fcitx"

当然，之前说过，sbopkg 无法自动解决依赖问题，所以安装 Fcitx 之前确保你已安装 `cmake`

具体用法参见： [sbopkg wiki](https://github.com/Ponce/slackbuilds/wiki/configuring-the-current-repository-with-sbopkg)

各 Linux 发行版不同的也就是包管理器，可以这么说，包管理器就是发行版的哲学，就是发行版的灵魂，认识一个发行版的本质就是认识该发行版的包管理器。至此，`slackpkg` 加上 `sbopkg`，Slackware 也就完全呈现在大家面前了

locale

    # localedef -i en_US -f UTF-8 en_US.UTF-8

edit `/etc/profile.d/lang.sh`

    LANG=en_US.UTF-8
    LC_CTYPE=zh_CN.UTF-8    # fcitx 在英文locale下需要

edit `/etc/slackpkg/slackpkg.conf`

    http://mirrors.ustc.edu.cn/slackware/slackware-14.0/

#### slackpkg 常用命令

    slackpkg update             #更新软件包数据库
    slackpkg check-update       #检查是否有更新
    slackpkg upgrade package    #升级package软件包
    slackpkg upgrade-all        #升级所有软件包
    slackpkg install package    #安装单个包
    slackpkg reinstall package  #重新安装
    slackpkg remove package     #移除
    slackpkg clean-system       #清理非官方正式的tgz包，慎用
    slackpkg install-new        #安装新包，版本升级
    slackpkg blacklist
    slackpkg download
    slackpkg info package
    slackpkg search file
    slackpkg new-config

#### sbopkg 常用命令

    sbopkg -r                   #更新repo
    sbopkg -i package           #安装package
    sbopkg -g name              #搜索是否包含name的包
    sbopkg -c                   #检查已安装包是否有更新
    sbopkg -d package           #只下载package的源码
    sbopkg -V repo/branch
    sbopkg -l                   #显示repo的更新日志

#### prompt:

配置自己常用的 prompt，单独拿出来说，主要是 Slackware 默认的 git 居然不包含 git-promt.sh。需要单独下载并 source 它

    wget https://raw.github.com/git/git/master/conrtib/completion/git-promt.sh -O .bash_git
    echo ". ~/.bash_git" >> ~/.bashrc

edit `~/.bashrc`

    export GIT_PS_SHOWDIRTYSTATE=1
    export PS1='\[\e[1;36m\]→\[\e[m\] \[\e[0;32m\]\w\[\e[0;35m\]$(__git_ps1)\[\e[1;32m\] \$\[\e[m\] '

最后 `source ~/.bashrc` 即可

PS：最近弃用海外邮箱，转用163、qq邮箱，再也受不了那鸟速度，放着现成的资源不用，非要去折腾去彰显自己的自由意识？哥过了那年纪了。至于安全问题，这是社会化工程问题，有安全意识更重要。

PS2：什么时候才能将自己的台机转成UEFI + LVM ( Windows8 + Gentoo ) 方案呢，最近在考虑方案中。主要是当前 Gentoo 的N年的数据问题。
