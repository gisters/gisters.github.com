---
layout: post
title: "OS X 解包 pkg"
category: Mac
tags: [Pkgutil]
---

刚用 OS X 的同学会发现，Mac OS X 的 App 安装包，有两种形式，一种就是 .app，可以直接运行，类似于 Windows 下的绿色版；另一种是 .pkg 安装包，需要一路 next 安装好后才能使用。

在 OS X 下，PKG 包的卸载非常不人性化，最讨厌的是，只要是个 pkg 安装包，基本都要请求 root 权限。

于是，网上溜达一圈，找到直接解包 pkg 文件的方法，获取有用部分来使用。

我们以招商银行的网银插件为例，下载获取 dmg 镜像文档：**CMBSecurityPlugin.dmg**，双击打开，将 **CMB Security Plugin.pkg** 文件拖出来。

<!-- more -->
在文章 [OS X 下卸载 pkg 包]({% post_url 2014-11-01-uninstall-apple-pkg-package %}) 中已经介绍过 pkgutil 的用法，现在我们来解包：

    $ pkgutil --expand CMB\ Security\ Plugin.pkg CMB\ Security\ Plugin
    $ cd CMB\ Security\ Plugin
    $ ls -l
    total 8
    -rw-r--r--  1 havee  staff  2794 12 25  2012 Distribution
    drwxr-xr-x  3 havee  staff   102  9  9 14:06 Resources
    drwxr-xr-x  5 havee  staff   170  9  9 14:06 cmbsecurityplugin.pkg

实际上，**CMB Security Plugin.pkg** 本身就是一个 xar 包：

    $ file CMB\ Security\ Plugin.pkg 
    CMB Security Plugin.pkg: xar archive - version 1

可以用如下命令直接解压：

    $ mkdir CMB\ Security\ Plugin; cd CMB\ Security\ Plugin
    $ xar -xf ../CMB\ Security\ Plugin.pkg
    $ ls -l
    total 8
    -rw-r--r--  1 havee  staff  2794 12 25  2012 Distribution
    drwxr-xr-x  3 havee  staff   102 12 25  2012 Resources
    drwxr-xr-x  5 havee  staff   170 12 25  2012 cmbsecurityplugin.pkg

可以看到，以上两种方式，都可以解压。

接下来，我们看到，又出现一个 pkg 文件，再看下文件类型：

    $ file cmbsecurityplugin.pkg
    cmbsecurityplugin.pkg: directory

是一个文件夹，进去：

    $ cd cmbsecurityplugin.pkg; ls -l
    total 304
    -rw-r--r--  1 havee  staff   36348 12 25  2012 Bom
    -rw-r--r--  1 havee  staff     216 12 25  2012 PackageInfo
    -rw-r--r--  1 havee  staff  114278 12 25  2012 Payload

对三个文件分别查看下类型：

    $ file Bom
    Bom: Mac OS X bill of materials (BOM) file
    $ file PackageInfo
    PackageInfo: ASCII text
    $ file Payload
    Payload: gzip compressed data, from Unix

可以看到，三个文件中 Payload 文件是一个 gzip 压缩包，解开看下：

    $ tar xvf Payload
    x .
    x ./._.DS_Store
    x ./CMBSecurity.plugin
    x ./CMBSecurity.plugin/Contents
    x ./CMBSecurity.plugin/Contents/embedded.provisionprofile
    x ./CMBSecurity.plugin/Contents/Info.plist
    x ./CMBSecurity.plugin/Contents/MacOS
    x ./CMBSecurity.plugin/Contents/MacOS/CMBSecurity
    x ./CMBSecurity.plugin/Contents/Resources
    x ./CMBSecurity.plugin/Contents/Resources/en.lproj
    x ./CMBSecurity.plugin/Contents/Resources/en.lproj/InfoPlist.strings
    x ./Contents
    x ./Contents/_CodeSignature
    x ./Contents/_CodeSignature/CodeResources
    x ./Contents/embedded.provisionprofile
    x ./Contents/Info.plist
    x ./Contents/MacOS
    x ./Contents/MacOS/CMBSecurity
    x ./Contents/Resources
    x ./Contents/Resources/en.lproj
    x ./Contents/Resources/en.lproj/InfoPlist.strings

OK，至此，我们得到了招商银行大众版网银插件 **CMBSecurity.plugin**，直接复制到 **~/Library/Internet Plug-Ins/** 目录下即可。

总结下，三步走：

    $ pkgutil --expand "name.pkg" "name"
    $ cd name/package.pkg/
    $ tar xvf Payload

当然，有些 pkg 会附带证书、启动 plist 文件等等，需要具体情况具体分析。
