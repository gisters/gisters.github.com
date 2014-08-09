---
layout: post
title: "Linux 中配置安装 rtorrent + rutorrent"
description: "Linux 中配置安装 rtorrent + rutorrent"
keywords: linux, rtorrent, rutorrent, pt, 盒子
category: Linux
tags: [Rtorrent, Rutorrent, PT]
---

因为是 PT 爱好者，除了自家 NAS 运行外，偶尔购买海外的盒子用来分享视频资源。

自从上次购买 ovh 的盒子，人肉配置 rtorrent + rutorrent 外，又因为昨天手贱的升级了盒子里的 archlinux 导致系统无法启动后，昨晚偶尔看到了一个开源项目 [autodl-setup](http://sourceforge.net/projects/autodl-irssi/)，于是大胆的尝试了下，非常完美的运行。

<!-- more -->

![rutorrent]({{ site.qiniudn }}/images/2013/06/rutorrent.png "rutorrent")

下面是以 root 权限来安装的步骤：

```
cd ~
wget --no-check-certificate -O autodl-setup http://sourceforge.net/projects/autodl-irssi/files/autodl-setup/download
sh autodl-setup
```

脚本运行期间会提示你输入安装的组件、用户、密码、rutorrent 运行用户等。整个安装过程非常的简单，除了在添加完一个用户后就 `Enter` 结束添加用户这一点需要注意外，否则就会无限制的循环添加用户。

![Install 1]({{ site.qiniudn }}/images/2013/06/install1.png "Install 1")

需要注意的是添加完用户后，脚本会循环问你是否添加用户，这里直接按回车键

![Install 2]({{ site.qiniudn }}/images/2013/06/install2.png "Install 2")

最后，按回车键开始安装

整个脚本总共安装一下服务

- 安装一个 web 服务器 (选择安装 lighttpd、nginx、apache)
- 安装 ruTorrent 和多用户支持 (http、https)
- 安装 ruTorrent 插件 autodl-irssi (选择是否安装该插件)
- 安装一个FTP 服务器 (选择是否安装vsftpd)
- 安装 xmlrpc-c 支持
- 安装 Webmin (选择是否安装)
- 为 rtorrent 和 Irssi 安装了启动脚本 （开机启动）

以下是目前支持的linux发行版（排名不分先后）

- Arch Linux 2010.05
- ArchBang 2010.10
- CentOS 4.8
- CentOS 5.5
- ClearOS 5.2
- Debian 4.0r7
- Debian 5.0.3
- Debian 5.0.7
- Fedora 13
- Fedora 14
- Frugalware 1.3
- Gentoo 1.12.14
- Linux Mint 8
- Linux Mint 9
- Linux Mint 10
- Mandriva Linux release 2008.0
- Mandriva Linux release 2010.1
- MEPIS 8.5
- openSUSE 11.1
- openSUSE 11.3
- Pardus 2009.2
- Pardus 2011
- PCLinuxOS 2010
- Peppermint OS
- RHEL 5.5
- RHEL 6.0
- Sabayon 5
- Scientific Linux 5.5
- Ubuntu Server 6.06.2 LTS
- Ubuntu Server 8.04.4 LTS
- Ubuntu Server 10.04.1 LTS
- Ubuntu 10.10
- Unity release 2010

注意：安装完成后，如果你启动了防火墙，请开放对应的端口

下图是本人安装后正常运行的一些截图

![RSS 订阅]({{ site.qiniudn }}/images/2013/06/rss.png)
![Setting 1](/assets//images/2013/06/setting 1.png)
![Setting 2]({{ site.qiniudn }}/images/2013/06/setting 2.png)

参考：http://whattheserver.me/billing/knowledgebase.php?action=displayarticle&id=38
