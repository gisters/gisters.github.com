---
layout: post
title: "ThinkPad X220 安装黑苹果 OS X El Capitan"
category: Mac
tags: [ThinkPad, OS X]
---

手上的 ThinkPad X220 黑苹果已经用了一段时间了，博客中关于黑苹果介绍的文章并不多，一直想写一篇，今天周末，正好有空，就开写。

![X220 El Capitan](http://cdn.09hd.com/images/2015/11/x220_El_Capitan.jpg)

<!-- more -->
#### 一. BIOS 相关

1. 升级官方 Lenovo BIOS 版本至 1.40，可以从如下官方地址下载：[Windows 1.40 Update Utility](http://support.lenovo.com/us/en/products/laptops-and-netbooks/thinkpad-x-series-laptops/thinkpad-x220/downloads/DS018805) 或 [Bootable 1.40 CD Image](http://support.lenovo.com/us/en/products/laptops-and-netbooks/thinkpad-x-series-laptops/thinkpad-x220/downloads/DS018807)
2. BIOS 白名单，肯定是必须的，因为要换 WIFI 模块，可以用这个工具 [patched BIOS version 1.40](http://pan.baidu.com/s/1eQ38Dbk) 去刷白名单
3. BIOS 设置
    - Config > Serial ATA(SATA) > **AHCI**
    - Security > Security Chip > **Disabled**
    - Security > Memory protection > Execution Prevention > **Enabled**
    - Security > Virtualization > Intel VT-d Feature > **Disabled**
    - Startup > UEFI/Legacy Boot > **Both**

#### 二. CLOVER

关于 USB 安装盘的制作与安装就不多说了， CLOVER 的相关配置可以从这里获取 [ThinkPad x220 OS X El Capitan EFI](http://pan.baidu.com/s/1o72sOkE)，解压开，将 EFI 目录拷贝至 U 盘的 EFI 分区。

如果不确认 U 盘的 EFI 分区为哪个，可以通过 `diskutil list` 来查询，随后通过 `diskutil mount` 来挂载。譬如：

```
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
```

显然，一看就了解，disk0 为系统磁盘，disk1 为 U 盘，那么，挂载 U 盘的 EFI 分区

```
$ diskutil mount /dev/disk1s1
```

系统安装完后，需要将 U 盘 EFI 的配置拷贝至系统磁盘的 EFI 分区，上面的命令来查询和挂载。

##### 1. EFI

我提供的EFI的配置含如下

```
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
```

注意：`EFI/CLOVER/kexts/LE` 下的文件，请移动至 `/Library/Extensions/` 目录下。

##### 2. ssdt.aml

最后，自己生成一个 CPU 的 ssdt.aml 文件：

```
$ curl -o ~/ssdtPRGen.sh https://raw.githubusercontent.com/Piker-Alpha/ssdtPRGen.sh/master/ssdtPRGen.sh
$ chmod +x ~/ssdtPRGen.sh
$ ./ssdtPRGen.sh
```

回答 N 至最后，ssdt.aml 自动保存至 `/Users/yourname/Library/ssdtPRGen` 目录下，将之移动至 `EFI/CLOVER/ACPI/patched/` 下，你的 CPU 就自动变频了。

##### 3. AppleHDA_20672

在文件拷贝至 `/Library/Extensions/` 后，针对 **AppleHDA_20672** 需要做如下操作

```
$ cd /Library/Extensions/AppleHDA_20672.kext/Contents/MacOS
$ sudo rm AppleHDA
$ sudo ln -sf /System/Library/Extensions/AppleHDA.kext/Contents/MacOS/AppleHDA
```

##### 4. 文件权限与重建内核扩展缓存

```
$ sudo chown -R root:wheel /Library/Extensions/
$ sudo touch /System/Library/Extensions
$ sudo kextcache -f -u /
```

参考：<http://x220.mcdonnelltech.com>
