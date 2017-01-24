---
layout: post
title: "ThinkPad X230 安装黑苹果"
category: Mac
tags: [ThinkPad]
---

用 [ThinkPad X220]({% post_url 2015-11-22-os-x-el-capitan-installation-guide-for-thinkpad-x220 %}) 跟人家替换了 ThinkPad X230，也有一段时间了，这不，快年底了，终于闲下来，然而耐不住骚动的心，又开始入了黑苹果的坑。

首先要说明的是，X230 以后，就不存在 BIOS 白名单了，所以，要么上烧录夹硬刷 BIOS 芯片，要么就换个白名单中在黑果上免驱的无线网卡。我采用的是后者，淘宝上淘了块 ar9285 无线网卡。

#### 一. BIOS 设置

首先，直接去官网下载最新版的 BIOS 来升级：[BIOS Update Utility V2.67](http://support.lenovo.com/us/zh/products/Laptops-and-netbooks/ThinkPad-X-Series-laptops/ThinkPad-X230/downloads/DS029187)

然后按照如下 BIOS 设置：

<!-- more -->

```
Config
    Network
        Wake On Lan: Disabled
    USB
        USB UEFI BIOS Support: Enabled
        Always On USB: Disabled
        USB 3.0 Mode: Enabled
    Serial ATA (SATA)
        Serial SATA Controller Mode Option: AHCI
    Power
        Intel (R) Rapid Start Technology: Disabled
Security
    Fingerprint
        Predesktop Authentication: Disabled
    Security Chip
        Security Chip: Disabled
    Memory Protection
        Execution Prevention: Enabled
    Virtualization
        Intel (R) Virtualization Technology: Disabled
        Intel (R) VT-d Feature: Disabled
    I/O Port Access
        Fingerprint Reader: Disabled
    Anti-Theft
        Current Setting: Disabled
        Computrace
            Current Setting: Disabled
    Secure Boot
        Secure Boot: Disabled
Startup
    Network Boot: PCI LAN
    UEFI/Legacy Boot: UEFI Only
    CSM Support: no
    Boot Mode: Quick
```

#### 二. CLOVER

同样，usb安装盘的制作以及如何安装就不说了，太多的文章。Clover 相关的配置，可以直接从这里提取：[EFI for macOS Sierra on ThinkPad X230](https://pan.baidu.com/s/1pLLZGRD)（2017-11-10 更新），解压开，将EFI 目录直接拷贝至你的 U 盘安装盘的 EFI 分区，或者拷贝至你系统盘的 EFI 分区。

##### EFI

我的 EFI 目录树，所有驱动都通过 dsdt 与 clover 来解决。

```
EFI
├── BOOT
│   └── BOOTX64.efi
└── CLOVER
    ├── ACPI
    │   ├── WINDOWS
    │   ├── origin
    │   │   ├── Clean DSDT.dsl
    │   │   └── ssdt.dsl
    │   └── patched
    │       ├── DSDT.aml
    │       └── SSDT.aml
    ├── CLOVERX64.efi
    ├── config.plist
    ├── drivers64UEFI
    ├── kexts
    │   └── Other
    │       ├── ACPIBatteryManager.kext
    │       ├── ACPISensors.kext
    │       ├── AppleALC.kext
    │       ├── AppleIntelE1000e.kext
    │       ├── BrcmFirmwareRepo.kext
    │       ├── BrcmPatchRAM2.kext
    │       ├── CPUSensors.kext
    │       ├── FakeSMC.kext
    │       ├── GPUSensors.kext
    │       ├── HibernationFixup.kext
    │       ├── IntelGraphicsFixup.kext
    │       ├── LPCSensors.kext
    │       ├── Lilu.kext
    │       └── VoodooPS2Controller.kext
    ├── themes
    │   └── embedded
    └── tools
```

暂停更新...

参考：<https://github.com/Bizzaro/x230-osx/>
