---
layout: post
title: "解决 Windows Thin PC 中无法更新的问题"
category: Windows
tags: [Windows]
---

虚拟机几乎已经变成必备工具，只不过，开启虚拟客户机的次数越来越少，到最后，唯有在网银支付的时候才会开启虚拟机应付一下。之前我的虚拟客户机 Windows XP 切换到 Windows 10，看着这个笨重的家伙，固态盘上占用的恐怖的体积，促使我寻找一个轻量级的虚拟客户机。

不得不承认，老了就跟不上时代了，通过搜索才发现，原来 Microsoft 早就提供了一款专门面向虚拟桌面基础架构消费者的操作系统 **Windows Thin PC**，该系统是 Windows 7 的超轻量级版本，主要面向企业客户。

闲话少说，MSDN 版下载：

    ed2k://|file|en_windows_thin_pc_x86_697681.iso|1576980480|2D0E6A048EB3F314F556B4F0834A95E2|/

<!-- more -->

我使用的就是英文版，不过如果想使用中文版的话，就需要单独安装简体中文包： [Chinese (Simplified) Language Pack - Windows Embedded Standard 7 SP1](http://download.microsoft.com/download/8/6/1/8616D57C-1163-45FB-832A-15FA60571002/SP1/Chinese (Simplified) Language Pack - Windows Embedded Standard 7 SP1/lp.cab)。

假设我们下载的语言包就放置在 C 盘目录下，那么以管理员身份运行 CMD：

    dism /online /add-package /packagepath:C:\lp.cab
    bcdedit /set {current} locale zh-cn
    bcdboot %WinDir% /l zh-cn

重启后去控制面板配置显示中文

    Control Panel -> Change display language -> Keyboards and Languages -> Choose a display language -> 简体中文 -> Apply

激活的话，可以采用证书方式激活，下载证书： [WinTPC 证书.zip](http://pan.baidu.com/s/1hrSsdrI)，解压开的两个文件复制到 `C:\Windows\System32\spp\tokens\skus\Security-SPP-Component-SKU-Embedded`，最后管理员权限运行 `slmgr.vbs /rilc`

以上内容，百度搜索一大堆，这里仅做一个备份，下面内容是解决 Windows Thin PC 无法更新的问题。

即点击查找更新后进度条一直跑，无法完成，且 svchost.exe 占有 100% 的 CPU，今天早上直接去微软知识库关键字搜索后才发现，必须先安装一个补丁才可以顺利更新：<https://support.microsoft.com/en-us/kb/3102810>。

微软官方补丁地址：

- x86: <https://www.microsoft.com/zh-CN/download/details.aspx?id=49545>
- x64: <https://www.microsoft.com/en-us/download/details.aspx?id=49547>

安装以后，就可以顺利的进行一些安全更新的操作。
