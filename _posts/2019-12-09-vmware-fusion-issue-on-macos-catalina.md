---
layout: post
title: "VMWare Fusion 的一些新问题"
category: Mac
tags: [VMWare, MacOS]
---

在 MacOS 系统下，总是有一定的频率需要用到虚拟机，所以，VMWare Fusion 或类似的软件是必不可少的。我也安装有 VMWare Fusion，用于解决偶尔需要的网银问题。只不过，自从上次将 VMWare Fusion 以及 MacOS 升级到最新版以后，就开始出现一些奇怪的问题。

#### USB 3.0 移动硬盘(UAS)

可以确认，我的外接 usb 固态硬盘无法工作，已经连接到客户机。我无法知道其他人是否也有同样的情况，但是 VMWare 社区确实有人也遇到同样的情况。

<!-- more -->

解决方法是关闭 VMWare Fusion，编辑客户机的 vmx 文件，在文件末尾添加一行

    usb.generic.keepStreamsEnabled = "FALSE"

何为 vmx 文件，以及如何打开，查看如下文档

<https://kb.vmware.com/s/article/1014782>

#### 打开客户机的时候，必两个弹出对话框

每次打开客户机，都弹出两个警告对话框，哪怕每次都允许，但是下次还是会弹出。此问题，通过 brew cask 安装 VMWare Fusion 的用户，是否都会遇到，我不太清楚。但是我遇到了，哪怕我在 System Preferences --> Security & Privacy --> Privacy 下将所有 VMWare 相关的勾选上，也无法解决。

此问题的根源是 macOS 的文件扩展属性问题，譬如你通过 Safari 下载的某个文件，通过 `xattr -l ~/Downloads/file` 查看会看到类似如下的扩展属性

    com.apple.quarantine: 0002;57e0f873;Safari.app;FF994637-322C-449F-8F20-3579EE99C399

本质上这个 **com.apple.quarantine** 属性，是 MacOS 的一种文件校验的保护措施，防止类似木马病毒修改文件。

通过 `brew cask` 下载的 app，同样也会带有 **com.apple.quarantine** 属性。这导致在运行 VMWare Fusion 的客户机的时候出现了不和谐。

解决方法是，首先卸载 VMWare Fusion。然后通过如下方式清除掉 VMWare Fusion 原始安装包的扩展属性。

    xattr -dr com.apple.quarantine /path/vmware-fusion.dmg

再进行安装，另外，需要对客户机的目录也进行一次扩展属性的清理。你客户机安装到哪儿的，就针对该目录进行一次清理，譬如我的

    xattr -dr com.apple.quarantine ~/Documents/Virtual\ Machines/

然后重新安装。

如果通过 `brew cask` 来安装 VMWare Fusion 的，则需要这样

    brew cask fetch vmware-fusion
    xattr -dr com.apple.quarantine ~/Library/Caches/Homebrew/Cask/vmware-fusion*
    brew cask install vmware-fusion
    xattr -dr com.apple.quarantine ~/Documents/Virtual\ Machines/

没啥技巧，就是将 VMWare Fusion 相关的文件，都清除掉 **com.apple.quarantine** 属性，然后第一次打开客户机的时候仍然会弹出两个对话框，后面就不会再弹出了。
