---
layout: post
title: "再次尝试 freebsd"
description: "自从 06 年初试 FreeBSD 后，随即转向 Gentoo 阵营。对 FreeBSD 只有一点点的印象，只知道 Gentoo 的 po   rtage 系统是由 BSD 家族的 ports 演变来的，最近升级了家用台式机，遂拿起 FreeBSD 出来折腾折腾。"
keywords: "freebsd"
category: "BSD"
tags: [freebsd]
---
{% include JB/setup %}

自从 06 年初试 FreeBSD 后，随即转向 Gentoo 阵营。对 FreeBSD 只有一点点的印象，只知道 Gentoo 的 portage 系统是由 BSD 家族的 ports 演变来的，最近升级了家用台式机，遂拿起 FreeBSD 出来折腾折腾。

安装过程就不多说了，不论 baidu 亦或 google 都能找到大量的教程。有点英文基础的，不用教程也能一步步安装下去。这里就记录下安装完成后的一些技巧。

启动时出现错误

    sm_meta[xxx]: My unqualified host name (hostname) unknown; sleeping for retry

按 `ctrl_c` 后继续，这个只需在 `/etc/hosts` 中 alias 下即可，譬如

    127.0.0.1   localhost   localhost.my.domain     hostname

加速 ports 源，编辑 `/etc/portsnap.conf`

    SERVERNAME=portsnap.cn.freebsd.org

第一次更新 ports

    portsnap fetch extract

以后

    portsnap fetch update

通过 ports 安装是类似与 Gentoo 那样的源代码安装方式，譬如安装 sudo

    cd /usr/ports/security/sudo
    make install clean

升级的话

    cd /usr/ports/security/sudo
    make deinstall reinstall clean

如果不清楚软件的具体目录可以搜索

    cd /usr/ports
    make search name=package
    or
    make search key=package

其中下载 distfiles 时速度可能很慢，那么加速下 package 源，编辑 `/etc/make.conf`，没有的话创建个 `touch /etc/make.conf`

    MASTER_SITE_OVERRIDE?=\
    ftp://ftp.cn.freebsd.org/pub/FreeBSD/ports/distfiles/${DIST_SUBDIR}\
    http://mirrors.utsc.edu.cn/freebsd/distfiles/${DIST_SUBDIR}\
    ftp://ftp.freebsdchina.org/pub/FreeBSD/ports/distfiles/${DIST_SUBDIR}

`make.conf` 配置可以参考示例文件 `/usr/share/examples/etc/make.conf`，Gentoo 用户用起来应该得心应手吧。

当然，也可以通过 `pkg_add -r package` 安装两进制包，嗯，速度一直是国内用户头痛的问题

    setenv PACKAGESITE ftp://ftp.cn.freebsd.org/pub/FreeBSD/releases/i386/9.2-RELEASE/packages/Latest/
    pkg_add -r sudo

为了便于以后安装二进制包方便，你可以编辑 `/etc/profile` 文件，最后加上

    PACKAGESITE=ftp://ftp.cn.freebsd.org/pub/FreeBSD/releases/i386/9.2-RELEASE/packages/Latest/

然后 `. /etc/profile` 更新下环境变量

普通用户 sudo pkg_add -r 安装，编辑 sudo 配置文件

    # visudo

下面一行前的 `#`去掉

    Defaults env_keep += "PKG_PATH PKG_DBDIR PKG_TMPDIR TMPDIR PACKAGEROOT PACKAGESITE PKGDIR FTP_PASSIVE_MODE"

待续......
