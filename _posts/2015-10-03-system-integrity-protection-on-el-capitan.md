---
layout: post
title: "El Capitan 中 SIP 介绍"
category: Mac
tags: [SIP, Clover]
---

这两天大家纷纷将 OS X 系统升级到了 El Capitan，然后发现，一些注入的工具无法使用了，某些系统目录无法使用了，第三方未签名的 kext 无法加载了，问题一堆堆的。这是因为，Mac OS X 在 10.11 中全面启用了 System Integrity Protection (SIP) —— 系统完整性保护技术。

SIP 技术主要是用来限制 root 用户的权限，以提升系统的健壮性。

具体哪些目录受到保护，可以查看文件

    /System/Library/Sandbox/rootless.conf

<!-- more -->
不被保护的列表存储在

    /System/Library/Sandbox/Compatibility.bundle/Contents/Resources/paths

Mac 提供了内置 `csrutil` 配置来进行一些 SIP 的配置。在默认情况下，SIP 是开启状态，你可以用一下指令查看

    $ csrutil status
    System Integrity Protection status: enabled

可配置项如下，字面意思：

    Apple Internal
    Kext Signing
    Filesystem Protections
    Debugging Restrictions
    DTrace Restrictions
    NVRAM Protections

#### 一. 白苹果用户

上面已经说过，Mac 提供了内置的 `csrutil` 工具来让用户进行一些配置，不过，你需要重启进入到 Recovery mode (Cmd + R on boot) 下进行操作。

csrutil 的一些常用命令

    csrutil clear           # 清除 SIP 用户配置，即开启默认的 SIP
    csrutil enable          # 开启 SIP
    csrutil disable         # 禁用 SIP
    csrutil status          # 查看当前 SIP 配置

关于 `csrutil enable` 可用参数为

    csrutil enable --no-internal --without kext --without fs --without fs --without debug --without dtrace --without nvram

譬如说，如果你需要某系统目录的读写权限，譬如 homebrew 全新安装的时候，需要创建 `/usr/local` 目录，那么你需要

1. 重启进入 Recovery mode
2. 打开 Terminal.app，输入 `csrutil enable --without fs`
3. 重启至正常系统下，打开 Terminal.app，安装 homebrew
4. 再次重启至 Recovery mode
5. 打开 Terminal.app，输入 `csrutil enable`
6. 重启

如果想安装第三方的 kext，那么建议装在目录 `/Library/Extensions/` 下。

#### 二. 黑苹果 Clover 用户

这里只说 Clover 用户，你需要配置你的 Clover ，加入如下代码

```xml
<key>RtVariables</key>
<dict>
    <key>CsrActiveConfig</key>
    <string>0x11</string>
</dict>
```

`<key>RtVariables</key>` 跟 `<key>SMBIOS</key>` 同级，如下图

![Clover config.plist](/cdn/images/2015/10/clover-config.png)

其中 **CsrActiveConfig** 的值 0x 后跟的是十六进制，Clover 中我们完全开启的值是 **0x77**，其中 77 转化为两进制为 **01110111**。

对于 **01110111**，每一位开启 SIP 的特定功能，从右至左 8 个位置我们以 B0-B7 表示：

- B0: 允许加载不受信任的 kext
- B1: 解锁文件系统限制
- B2: 允许 task_for_pid()调用
- B3: 允许内核调试
- B4: Apple 内部保留位，值为 1 等效于 `csrutil enable`
- B5: 解锁 DTrace
- B6: 解锁 NVRAM
- B7: 允许设备配置

如此，我们很清楚了，如果我们只想加载修改的或第三方的 kext，只需 B0 于 B4 位置值为 1，则整个两进制值为 **00010001**，用 bc 命令行工具进行任意进制转换

    $ echo "obase=16; ibase=2; 00010001"|bc
    11

转化为十六进制为 **11**，则 **CsrActiveConfig** 值为 **0x11**。

如果要同时解锁 kext 与 fs 呢，**00010011** --> ?

    $ echo "obase=16; ibase=2; 00010011"|bc
    13

十六进制值为 **13**，**CsrActiveConfig** 值为 **0x13**。

为了有助于我们的理解，看下表所示：

|Configration|N/A|NVRAM|Dtrace|internal|Debug|PID|FS|Kext|HEX|Clover|
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|csrutil enable --no-internal|0|0|0|0|0|0|0|0|00|0x00|
|csrutil enable|0|0|0|1|0|0|0|0|10|0x10|
|csrutil enable --without kext|0|0|0|1|0|0|0|1|11|0x11|
|csrutil enable --without fs|0|0|0|1|0|0|1|0|12|0x12|
|csrutil enable --without debug|0|0|0|1|0|1|0|0|14|0x14|
|csrutil enable --without dtrace|0|0|1|1|0|0|0|0|30|0x30|
|csrutil enable --without nvram|0|1|0|1|0|0|0|0|50|0x50|
|csrutil disable|0|1|1|1|0|1|1|1|77|0x77|
|csrutil disable (no internal)|0|1|1|0|0|1|1|1|67|0x67|

参考：

- <http://osxarena.com/2015/10/guide-details-apples-system-integrity-protection-sip-for-hackintosh/>
- <http://bbs.pcbeta.com/viewthread-1605186-1-1.html>
