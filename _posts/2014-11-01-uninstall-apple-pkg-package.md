---
layout: post
title: "OS X 下卸载 pkg 包"
description: "介绍 OS X 下使用 pkgutil 来卸载 pkg 软件包"
keywords: "OS X, Mac, pkg, 卸载"
category: Mac
tags: [Pkgutil]
---

好久没写记录了，昨晚在 OS X 下卸载一堆 pkg 安装的软件包，特意放狗搜了下网络，遂做下记录，免得遗忘。以下所说的只适用于 OS X Yosemite 下，之前的版本不做考虑。

### 一、准备

首先，安装的 pkg 软件包，都记录在以下

    /Library/Receipts/InstallHistory.plist
    /private/var/db/receipts

<!-- more -->
下面介绍的是一个命令工具：**pkgutil**。

```console
$ pkgutil -h
Usage: pkgutil [OPTIONS] [COMMANDS] ...

Options:
  --help                 Show this usage guide
  --verbose, -v          Show contextual information and format for easy reading
  --force, -f            Perform all operations without asking for confirmation
  --volume PATH          Perform all operations on the specified volume
  --edit-pkg PKGID       Adjust properties of package PKGID using --learn PATH
  --only-files           List only files (not directories) in --files listing
  --only-dirs            List only directories (not files) in --files listing
  --regexp               Try all PKGID arguments as regular expressions

Receipt Database Commands:
  --pkgs, --packages     List all currently installed package IDs on --volume
  --pkgs-plist           List all package IDs on --volume in plist format
  --pkgs=REGEXP          List package IDs on --volume that match REGEXP
  --groups               List all GROUPIDs on --volume
  --groups-plist         List all GROUPIDs on --volume in plist format
  --group-pkgs GROUPID   List all PKGIDs in GROUPID
  --files PKGID          List files installed by the specified package
  --lsbom PKGID          List files in the same format as 'lsbom -s'
  --pkg-groups PKGID     List all GROUPIDs that PKGID is a member of
  --export-plist PKGID   Print all info about PKGID in plist format
  --verify PKGID         Verify file permissions of the specified package
  --repair PKGID         Repair file permissions of the specified package
  --pkg-info PKGID       Show metadata about PKGID
  --pkg-info-plist PKGID Show metadata about PKGID in plist format
  --file-info PATH       Show metadata known about PATH
  --file-info-plist PATH Show metadata known about PATH in plist format
  --forget PKGID         Discard receipt data for the specified package
  --learn PATH           Update --edit-pkg PKGID with actual metadata from PATH

File Commands:
  --expand PKG DIR       Expand the flat package PKG to DIR
  --flatten DIR PKG      Flatten the files at DIR as PKG
  --bom PATH             Extract any Bom files from the pkg at PATH into /tmp
  --payload-files PATH   List the paths archived within the (m)pkg at PATH
```

### 二、方法

pkgutil 的帮助文件已经说明的很清楚了，步骤：

#### 1. 查找下你需要卸载的软件包ID

```console
$ pkgutil --pkgs
```

#### 2.列出该 pkg 包含的文件列表

```console
$ pkgutil --files PKGID
```

#### 3. 检查下软件包信息，路径

```console
$ pkgutil --pkg-info PKGID
```

譬如招行的插件信息

```console
$ pkgutil --pkg-info com.cmbchina.CMBSecurityPlugin.pkg
package-id: com.cmbchina.CMBSecurityPlugin.pkg
version: 1.0
volume: /
location: Library/Internet Plug-Ins
install-time: 1401513557
```

从以上我们要获取的信息是，PKGID 为 com.cmbchina.CMBSecurityPlugin.pkg，在根目录 `/` 下的 `Library/Internet Plug-Ins` 目录，也就是 `/Library/Internet Plug-Ins` 目录下，这个下面用得到。

#### 4. 执行删除操作

你当然可以通过 `pkgutil --files PKGID` 得到的文件列表，手动的去删除，我们还是读软件包的 bom 文件去删除，仍然以招行插件为例：

```console
$ lsbom -fls  /private/var/db/receipts/com.cmbchina.CMBSecurityPlugin.pkg.bom | (cd /Library/Internet\ Plug-Ins; sudo xargs rm)
$ lsbom -dls  /private/var/db/receipts/com.cmbchina.CMBSecurityPlugin.pkg.bom | (cd /Library/Internet\ Plug-Ins; sudo xargs rm -r)
```

cd 路径，即第 3 步中的目录。

lsbom 的具体用法可以参考 `lsbom --help`。

#### 4. 最后清除包管理数据库中的pkg包信息

```console
$ sudo pkgutil --forget PKGID
```

### 三、App

上面的都是一些基础的介绍，当然如今已经有现成的 app 可以使用，譬如 <del>[PackageUninstaller](https://github.com/hewigovens/PackageUninstaller)</del>、[UninstallPKG](http://www.corecode.at/uninstallpkg/)，前者免费，后者收费。

参考：

- [Uninstalling packages (.pkg files) on Mac OS X](https://wincent.com/wiki/Uninstalling_packages_\(.pkg_files\)_on_Mac_OS_X)
- [Mac OS X Hints 翻译计划:卸载通过.pkg安装的应用程序](http://www.macfans.com.cn/forum.php?mod=viewthread&tid=216065)
