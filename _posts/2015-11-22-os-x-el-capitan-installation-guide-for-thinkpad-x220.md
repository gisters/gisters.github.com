---
layout: post
title: "ThinkPad X220 安装黑苹果 macOS"
category: Mac
tags: [ThinkPad]
---

手上的 ThinkPad X220 黑苹果已经用了一段时间了，博客中关于黑苹果介绍的文章并不多，一直想写一篇，今天周末，正好有空，就开写。

![X220 El Capitan](/cdn/images/2015/11/x220_El_Capitan.jpg)

<!-- more -->

#### 一. BIOS 相关

1. 升级官方 Lenovo BIOS 版本至 1.40，可以从如下官方地址下载：[Windows 1.40 Update Utility](http://support.lenovo.com/us/en/products/laptops-and-netbooks/thinkpad-x-series-laptops/thinkpad-x220/downloads/DS018805) 或 [Bootable 1.40 CD Image](http://support.lenovo.com/us/en/products/laptops-and-netbooks/thinkpad-x-series-laptops/thinkpad-x220/downloads/DS018807)
2. BIOS 白名单，肯定是必须的，因为要换 WIFI 模块，可以用这个工具 [patched BIOS version 1.40](http://pan.baidu.com/s/1dEyLJCP) 去刷白名单
3. BIOS 设置
    - Config > Serial ATA(SATA) > **AHCI**
    - Security > Security Chip > **Disabled**
    - Security > Memory protection > Execution Prevention > **Enabled**
    - Security > Virtualization > Intel VT-d Feature > **Disabled**
    - Startup > UEFI/Legacy Boot > **Both**

#### 二. CLOVER

关于 USB 安装盘的制作与安装就不多说了， CLOVER 的相关配置可以从这里获取 [ThinkPad x220 OS X El Capitan EFI](http://pan.baidu.com/s/1o72sOkE)，解压开，将 EFI 目录拷贝至 U 盘的 EFI 分区。

如果不确认 U 盘的 EFI 分区为哪个，可以通过 `diskutil list` 来查询，随后通过 `diskutil mount` 来挂载。譬如：

    $ diskutil list
    /dev/disk0 (internal, physical):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:      GUID_partition_scheme                        *256.1 GB   disk0
       1:                        EFI EFI                     209.7 MB   disk0s1
       2:                  Apple_HFS Macintosh HD            255.2 GB   disk0s2
       3:                 Apple_Boot Recovery HD             650.0 MB   disk0s3
    /dev/disk1 (external, physical):
       #:                       TYPE NAME                    SIZE       IDENTIFIER
       0:      GUID_partition_scheme                        *7.8 GB     disk1
       1:                        EFI EFI                     209.7 MB   disk1s1
       2:                  Apple_HFS Install OS X El Capitan 7.4 GB     disk1s2

显然，一看就了解，disk0 为系统磁盘，disk1 为 U 盘，那么，挂载 U 盘的 EFI 分区

    $ diskutil mount /dev/disk1s1

系统安装完后，需要将 U 盘 EFI 的配置拷贝至系统磁盘的 EFI 分区，上面的命令来查询和挂载。

##### 1. EFI

我提供的EFI的配置含如下

    EFI
    ├── BOOT
    └── CLOVER
        ├── ACPI
        │   └── patched
        │       ├── dsdt.aml
        ├── drivers64UEFI
        ├── kexts
        │   ├── LE
        │   │   ├── AppleHDA_20672.kext
        │   │   ├── BroadcomBluetooth.kext
        │   │   └── IOGraphicsFamily.kext
        │   └── Other
        │       ├── ACPIBatteryManager.kext
        │       ├── FakeSMC.kext
        │       ├── IOAHCIBlockStorageInjector.kext
        │       ├── IntelBacklight.kext
        │       ├── IntelMausiEthernet.kext
        │       └── VoodooPS2Controller.kext
        ├── themes
        │   └── ThinkPad
        └── tools

注意：`EFI/CLOVER/kexts/LE` 下的文件，请移动至 `/Library/Extensions/` 目录下。

##### 2. ssdt.aml

最后，自己生成一个 CPU 的 ssdt.aml 文件：

    $ curl -o ~/ssdtPRGen.sh https://raw.githubusercontent.com/Piker-Alpha/ssdtPRGen.sh/master/ssdtPRGen.sh
    $ chmod +x ~/ssdtPRGen.sh
    $ ./ssdtPRGen.sh

回答 N 至最后，ssdt.aml 自动保存至 `/Users/yourname/Library/ssdtPRGen` 目录下，将之移动至 `EFI/CLOVER/ACPI/patched/` 下，你的 CPU 就自动变频了。

##### 3. AppleHDA_20672

在文件拷贝至 `/Library/Extensions/` 后，针对 **AppleHDA_20672** 需要做如下操作

    $ cd /Library/Extensions/AppleHDA_20672.kext/Contents/MacOS
    $ sudo rm AppleHDA
    $ sudo ln -sf /System/Library/Extensions/AppleHDA.kext/Contents/MacOS/AppleHDA

##### 4. 文件权限与重建内核扩展缓存

    $ sudo chown -R root:wheel /Library/Extensions/
    $ sudo touch /System/Library/Extensions
    $ sudo kextcache -f -u /

##### 5. 白果三码

以下方法我没有验证过，不过很多同学依照此方法正常使用 iMessage & FaceTime。

1. 打开 Clover Configurator，挂载你的 EFI
2. 在 Clover Configurator 中打开你的 config.plist
3. 定位到 Rt Variables 段，清除该段所有信息
4. 定位到 SMBIOS 段，点击魔术棒随机生成信息，多点击几次
5. 去 <https://selfsolve.apple.com>，填入刚才生成的 serial number 来验证，如果得到错误提示，恭喜你，可以使用，如果正确，则回到上一步重新生成信息
6. 打开终端，运行 `uuidgen`
7. 复制刚才终端生成的 UUID 信息粘贴到 SMBIOS 段中的 SmUUID 中
8. 在 Board SerialNumber 项中，填上上面魔术棒生成的序列号，自己再添加一个5位的随机数，最终是 17 位。
9. 保存 config.plist
10. 打开终端，运行如下命令
    - `$ defaults write com.apple.finder AppleShowAllFiles TRUE`
    - `$ killall Finder`
11. 进入目录 **/Users/[Username]/Library/Caches**，删除所有以下字眼开头的文件或文件夹，如果没有就不用管
    - com.apple.Messages
    - com.apple.imfoundation.IMRemoteURLConnectionAgent
12. 进入目录 **/Users/[Username]/Library/Preferences**，删除所有以下字眼开头的文件或文件夹，如果没有就不用管
    - com.apple.iChat
    - com.apple.imagent
    - com.apple.imessage
    - com.apple.imservice
13. 进入目录 **/Users/[Username]/Library**，删除文件夹 **Messages**，如果没有则不用管
14. 清空垃圾篓，如果提示文件仍在被使用，请重启再清空垃圾篓
15. 重建缓存，完成后重启（PS：个人感觉，这一步不是必须的）
    - `$ sudo touch /System/Library/Extensions`
    - `$ sudo kextcache -f -u /`
16. 如果 iMessage 与 FaceTime 登陆 appid 后正常了，则可以重新隐藏默认隐藏的文件或文件夹了
    - `$ defaults write com.apple.finder AppleShowAllFiles`
    - `killall Finder`

三. 已知问题

- 关机变重启

参考：

- <http://x220.mcdonnelltech.com>
- [https://www.reddit.com/](https://www.reddit.com/r/hackintosh/comments/2wohwn/getting_imessage_working_on_10102_generating/)
