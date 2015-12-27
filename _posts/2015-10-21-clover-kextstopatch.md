---
layout: post
title: "Clover KextsToPatch 使用方法"
category: Mac
tags: [Clover]
---

前些天，因为 Thinkpad X230 BIOS 白名单限制，给她换了一块 ar9285 无线网卡，只是因为这块网卡正好可以被 Mac OS X 驱动，也正好在 Thinkpad X230 BIOS 白名单之中。给 Clover 配置的时候，为了防止忘记，便记录下来。

因为灵活使用 Clover 成为黑苹果用户的必修课，否则你就只能漫天寻找 kext，寄希望与运气能正确驱动你的硬件了。Clover 中有一个强大的功能 **KextsToPatch**，属于 **KernelAndKextPatches** 的子项。

之所以用到 **KextsToPatch**，是因为不想每次升级的时候，都去更改系统 Kext，同时也为了防止升级时出现意外，也保持系统原汁原味，干净整洁。故一般情况下，我很少直接去动系统的内核扩展文件。

<!-- more -->

>Apart from the built-in patches you can create your own ones providing following data: the binary file name, the data to find and the data to replace with - both in hexadecimal. The data length must be equal. A smaller replacement data line can be filled with zeroes.

按照官方说法，是给内核扩展打补丁开启一些被限制的功能。譬如 OS X 本身已经支持 ar9285。只是提供的 Info.plist 不够完善，没有开启。用 ar9285 的同学都知道，只需要修改 `AirPortAtheros40.kext` 中的一个 ID 即可，即 `pci168c,2a` --> `pci168c,2b`。

前面已经说过，**KextsToPatch** 是 **KernelAndKextPatches** 的一个子项，故修改的时候注意，他的大体格式如下

```xml
<key>KernelAndKextPatches</key>
<dict>
    ......
    <key>KextsToPatch</key>
    <array>
        <dict>
            <key>Comment</key>
            <string>your comment</string>
            <key>Find</key>
            <data>
            data string
            </data>
            <key>InfoPlistPatch</key>
            <true/>
            <key>Name</key>
            <string>kexts name</string>
            <key>Replace</key>
            <data>
            new data string
            </data>
        </dict>
    </array>
</dict>
```

- Name：是你用于修改的 kext 的文件名
- Comment：值是一些便于你识别的信息
- Find Data：顾名思义查找你用于替换的信息的 base64 编码
- Replace Data：替换你的新信息的 base64 编码
- InfoPlistPatch：给 Info.plist 打补丁

一切清楚明了，修改的是 AirPortAtheros40，Info.plist 位于 `/System/Library/Extensions/IO80211Family.kext/Contents/PlugIns/AirPortAtheros40.kext/Contents` 下，打开后可以看到如下的一些信息

```xml
<key>IONameMatch</key>
<array>
    <string>pci168c,30</string>
    <string>pci168c,2a</string>
    <string>pci106b,0086</string>
    <string>pci168c,1c</string>
    <string>pci168c,23</string>
    <string>pci168c,24</string>
</array>
```

所要做的就是将 `pci168c,2a` 替换成 `pci168c,2b`，粗暴的做法，就是直接修改这个文件，下面加一行 `<string>pci168c,2b</string>`，重建缓存或重启两次，ar9285 就被驱动了，而我要做的是在 Clover 中进行替换。

将 `pci168c,2a`、`pci168c,2b` 都进行 base64 编码，打开终端：

    → ~ $ echo -n 'pci168c,2a'|base64
    cGNpMTY4YywyYQ==
    → ~ $ echo -n 'pci168c,2b'|base64
    cGNpMTY4YywyYg==

注意，echo 需要加 -n 参数，防止编码时被加入回车字符。

将之写入 Clover 的 config.plist 的 `KernelAndKextPatches` 子项之中

```xml
<key>KernelAndKextPatches</key>
<dict>
    ......
    <key>KextsToPatch</key>
    <array>
        <dict>
            <key>Comment</key>
            <string>AR9285_NoNeedToEditPlist</string>
            <key>Find</key>
            <data>
            cGNpMTY4YywyYQ==
            </data>
            <key>InfoPlistPatch</key>
            <true/>
            <key>Name</key>
            <string>AirPortAtheros40</string>
            <key>Replace</key>
            <data>
            cGNpMTY4YywyYg==
            </data>
        </dict>
    </array>
</dict>
```

同时 Thinkpad X230 的蓝牙，默认也可以驱动，只是蓝牙版本是 3.0 的，同理，我们修改的是 BroadcomBluetoothHostControllerUSBTransport，Info.plist 位于 `/System/Library/Extensions/IOBluetoothFamily.kext/Contents/PlugIns/BroadcomBluetoothHostControllerUSBTransport.kext/Contents` 目录下，找到 idVendor 为 **2652**的设备，并随便找一个 BT V3.0 的型号，譬如 `PID 8600 0x2198 VID 2652 0xA5C`，我们就拿它来开刀

    → ~ $ echo -n '8600' |base64
    ODYwMA==
    → ~ $ echo -n '8678' |base64
    ODY3OA==

得到 base64 码后，写入 config.plist

```xml
<key>KernelAndKextPatches</key>
<dict>
    <!--......-->
    <key>KextsToPatch</key>
    <array>
        ...
        <dict>
            <key>Comment</key>
            <string>inject_BCM20702A0</string>
            <key>Find</key>
            <data>
            ODYwMA==
            </data>
            <key>InfoPlistPatch</key>
            <true/>
            <key>Name</key>
            <string>BroadcomBluetoothHostControllerUSBTransport</string>
            <key>Replace</key>
            <data>
            ODY3OA==
            </data>
        </dict>
    </array>
</dict>
```

参考：<http://clover-wiki.zetam.org/Configuration/KernelAndKextPatches#kernelandkextpatches_kextstopatch>
