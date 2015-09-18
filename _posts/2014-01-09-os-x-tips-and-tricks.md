---
layout: post
title: "OS X 的一些技巧汇总"
category: "Mac"
tags: [Tips]
---

刚上手 OS X，习惯正在慢慢培养中，下面记录一些使用过程中的小技巧，不断补充

#### 一. 显示和隐藏

##### 1. 显示和隐藏系统文件

10.8 版本 OS X

    $ defaults write com.apple.finder AppleShowAllFiles YES/NO

10.9 版本 OS X

    $ defaults write ~/Library/Preferences/com.apple.finder.plist AppleShowAllFiles -bool true/false

<!-- more -->
##### 2. 显示和隐藏普通文件

    $ chflags hidden/nohidden file or directory

#### 二. 文件和目录的扩展属性问题

在 Mac OS X 下，文件经常会被附加上 OS X 特有的扩展属性 ( extend attributes )，具体表现是用 `ls -l` 查看时会有 `@` 的标记，譬如

    $ ls -l os.pdf
    -rw-r--r--@  1 cnhavee  staff  1518  1 9 14:13 os.pdf

这个 `@` 属性是用户在 Finder 中对文件进行任意操作后就会被附带上，特别讨厌这个属性，这直接导致在 OS X 下打包后放到 Linux 系统分享文件的时候，会出现莫名其妙的错误，兼因 tar 命令本身并不能区分 **extend attributes**

还好，Mac OS X 下的 cp 有个选项 `-X` 能忽略这个扩展属性

    cp: -X      do not copy extended attributes (eas) or resource forks.

处理这个问题，譬如打包，在打包前，请用 `cp -Xr` 拷贝出一份没有扩展属性的文件或目录，然后再 tar 打包处理。

另有个命令 `xattr` 也能做到

    $ xattr -l filename
    com.apple.ResourceFork:
    00000000  00 00 01 00 00 00 05 08 00 00 04 08 00 00 00 32  |...............2|
    ……

    $ xattr -d com.apple.ResourceFork filename

你可以一次性清除一个文件的所有 extend attributes

    $ xattr -c filename

你也可以对一个目录及其下的所有文件做清除操作

    $ xattr -rc directory

#### 三. 鼠标移动加速

默认的 Mac OS X 下鼠标的加速，实在是不习惯，俺用 OS X 不是来作图的，纯粹是图它的稳定。所以，还是想办法关闭鼠标的移动加速吧。

下载一个 app，[CursorSense](http://plentycom.jp/en/cursorsense/download.php "CursorSense")

![CursorSense](//cdn.09hd.com/images/2014/01/CursorSense.png)

#### 四. GoAgent

关于 Proxy，有很多开源的解决方案，譬如 Goagent

- [Homebrew 脚本 GoAgent ]({% post_url 2014-05-23-homebrew-formula-for-goagent %})
- [OS X 上使用 GoAgent]({% post_url 2013-12-20-use-goagent-on-os-x %})
- [CentOS 下打包 shadowsocks-libev]({% post_url 2014-08-20-package-shadowsocks-libev-on-centos %})

#### 五. 开源软件

可以通过 [MacPorts](http://www.macports.org)、[HomeBrew]({% post_url 2013-12-21-how-to-install-and-use-homebrew %}) 等包管理工具来使用开源软件。

譬如[《OS X 上安装 Nginx + PHP-FPM + MariaDB》]({% post_url 2014-01-06-install-nginx-php-fpm-mysql-on-os-x %})

#### 六. 重启 Finder

    $ killall Finder

或者可以按下 Option 键的同事，右键 Dock 栏上的 Finder 选择重启即可。

Option 键，顾名思义，具体选项的键，譬如按住 Option 键后，点击无线图标，则可以显示具体的无线网络状况。

#### 七. 提示声

按住 Shift 键再调节音量，即可临时静音调节

开启充电提示音

    $ defaults write com.apple.PowerChime ChimeOnAllHardware -bool true; open /System/Library/CoreServices/PowerChime.app

关闭充电提示音

    $ defaults write com.apple.PowerChime ChimeOnAllHardware -bool false; killall PowerChime

#### 八. 刷新 DNS 缓存

针对 OS X 10.4、10.5

    $ sudo lookupd -flushcache


针对 OS X 10.6

    $ sudo dscacheutil -flushcache

针对 OS X 10.7、10.8、10.9

    $ sudo killall -HUP mDNSResponder

针对 OS X 10.10

    $ sudo dscacheutil -flushcache

#### 九. 视频播放

尝试过 N 个播放器，包括收费的射手播放器在内都不太好用，最后确定以下几款视频播放器

##### 1. [MPV](//mpv.io)

原汁原味的 Linux 下 Mplayer 的使用体验，强烈推荐，fork 自 mplayer2 与 Mplayer。

![MPV screenshot](//cdn.09hd.com/images/2014/01/mpv.png)

##### 2. [MPlayer OSX Extended](//mplayerosx.ch) 

Mplayer OSX Extended 是个非常不错的视频播放器，使用过一段时间。

![MPlayer OSX Extended](//cdn.09hd.com/images/2014/01/mplayer-osx.png)

![MPlayer OSX Preferences](//cdn.09hd.com/images/2014/01/mplayer-osx-preferences.png)

##### 3. [MplayerX](//mplayerx.org)

另一个很好的选择。

##### 4. 蓝光播放

###### a. 收费的 [Mac Blu-ray Player](//www.macblurayplayer.com)

![Mac Blu-ray Player](//cdn.09hd.com/images/2014/01/mac-blu-ray-player.png)

###### b. 免费的 [XBMC](//kodi.tv/)

![XBMC](//cdn.09hd.com/images/2014/01/xbmc.png)

#### 十. 生僻的快捷键

- Ctrl + a/e：移动至行首/尾
- Ctrl + l：清除屏幕
- Ctrl + u：清除当前行
- Ctrl + k：清除至当前行尾
- Ctrl + w：向前删除一个单词
- Ctrl + y：粘贴用 Ctrl + w/k/u 删除的单词
- Option + right/left：向右/左移动一个单词
- Fn + up/down：相当于 Page up/down
- Ctrl + Command + f：全屏，仅支持右上角双箭头的窗口
- Ctrl + Command + d：取词翻译

#### 十一.  特殊符号

- 版权符号：Option + G = ©
- 人名币符号：Option + Y = ￥
- 商标符号：Option + 2 = ™
- 已注册的商标：Option + R = ®

#### 十二. 清理右键菜单

用了段时间后，右键菜单就开始臃肿不堪，有时会冒出重复项，打开终端，输入

    $ /System/Library/Frameworks/CoreServices.framework/Versions/A/Frameworks/LaunchServices.framework/Versions/A/Support/lsregister -kill -seed -r -f -v -domain local -domain user -domain system; killall Finder

#### 十三. 重建 Launchpad

    $ rm ~/Library/Application Support/Dock/*.db; killall Dock

#### 十四. app 归类

用了段时间会发现，app目录存在两个地方，一个是用户家目录 `~/Applications`，一个是根目录 `/Applications`。

我的处理是这样的，app store的软件默认就在根目录下的 `/Applications`，自己下载的 app，我则是放到家目录 `~/Applications`，而不会随便放到 `/Applications` 而默认给于 root 权限。

同样，能直接在 app store 安装的就直接安装，其次使用 HomeBrew 去安装病自动 ln 到 `~/Applications` 目录。

#### 十五. 截屏

系统默认的快捷键 Shift + Command + 3/4 截屏会带上阴影，部分人觉得不爽，要去掉阴影。

    $ defaults write com.apple.screencapture disable-shadow -bool true
    $ killall SystemUIServer

自带的截屏操作可以修改默认存放的位置

    $ defaults write com.apple.screencapture location /your/path

默认的截屏后保存的格式为 png，当然你可以改为其他格式

    $ defaults write com.apple.screencapture type jpg
    $ defaults write com.apple.screencapture type gif
    $ defaults write com.apple.screencapture type png
    $ defaults write com.apple.screencapture type pdf

#### 十六. Mail.app 撰写邮件附件显示

    $ defaults write com.apple.mail DisableInlineAttachmentViewing -bool yes/no
    $ defaults write com.apple.mail DisableInlineAttachmentViewing -bool true/false

#### 十七. 查看应用沙盒状态

譬如查看 Mac App Store 安装的 QQ

    $ codesign --display --entitlements - /Applications/QQ.app

#### 十八. 恢复 Spotlight 窗口位置

OS X 10.11 中，Spotlight 可以移动位置，那么恢复默认位置的方法为

    $ defaults delete com.apple.Spotlight userHasMovedWindow

#### 十九. 黑果忽略一些硬件更新补丁

黑苹果用的是模拟白苹果的方式，故一些针对白果硬件布丁，黑果可能一直在不断更新，那么忽略它吧，譬如上次的 Thunderbolt Firmware Update

    $ sudo softwareupdate --ignore ThunderboltFirmwareUpdate1.2

待更新……
