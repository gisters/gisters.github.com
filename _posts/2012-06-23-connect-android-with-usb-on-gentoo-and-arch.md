---
layout: post
title: "Gentoo & Arch 下通过 usb 连接 Android"
description: "Gentoo & Arch 下通过 usb 连接 Android"
keywords: gentoo, arch, android, usb
category: Linux
tags: [Gentoo, Arch, Android]
---

作为叼丝的一员，手机当然用的 Android 系统了，Gentoo Portage 中已经提供了 Android 的开发工具

    $ sudo emerge -av dev-util/android-sdk-update-manager

注意，依赖包 `dev-java/swt` 需要启用 cairo USE flag

接下来将你的用户名加入 android 用户组

<!-- more -->

    $ sudo sh -c "gpasswd -a <user> android"

更新环境变量

    sudo sh -c "env-update && source /etc/profile"

查看下你用户的环境变量

    echo $PATH  && echo $ANDROID_SWT

查看下你用户的环境变量，有无 `/opt/android-sdk-update-manager/tools:/opt/android-sdk-update-manager` 以及 `/usr/share/swt-4.2/lib` 路径，如没有，则编辑 `/etc/env.d/80android-sdk-update-manager` 文件，添加变量路径

    PATH="/opt/android-sdk-update-manager/tools:/opt/android-sdk-update-manager/platform-tools"
    ANDROID_SWT="/usr/share/swt-4.2/lib"

再更新下环境变量

    sudo sh -c "env-update && source /etc/profile"

检查下变量路径是否正确，然后运行

    $ android
    or
    $ /opt/android-sdk-update-manager/tools/android

来下载最新的 platform-tools 工具包如下图所示

![Android SDK Platform Tools]({{ site.qiniudn }}/images/2012/06/android-sdk-platform-tools.png "Android SDK Platform Tools")

最后加入 udev 规则（不是必须，如果 lsusb 能认到你安卓设备的话）。ATTR{idVendor} 信息，即厂商的设备号，可以通过这个地址来查询：[http://developer.android.com/tools/device.html#VendorIds](http://developer.android.com/tools/device.html#VendorIds)

    SUBSYSTEM=="usb", SYSFS{idVendor}=="19D2", MODE="0666"

重载udev

    $ sudo udevadm control --reload-rules

或者重新插拔下连接你 android 手机的 usb 口也可以，然后

    $ adb devices

即可看到你的手机了

![adb shell]({{ site.qiniudn }}/images/2012/06/adb-shell.png "adb shell")

如果你有手机 root 权限，想要对手机进行一些操作

    $ adb shell

就可以进入手机系统了，然后你可以运行一些简单的 bash 指令，譬如

    busybox vi
    busybox rm
    busybox mv
    busybox cat
    ......

然后做啥？adb 常用命令

    $ adb kill-server                # 退出 adb deamon
    $ adb start-server               # 启动 adb deamon
    $ adb install package.apk        # 安装应用
    $ adb uninstall com.package      # 卸载应用
    $ adb shell command              # 执行 shell 指令
    $ adb pull remote local          # 将设备中数据复制到本地
    $ adb push local remote          # 将本地数据复制到设备中
    $ adb forward tcp:port tcp:port  # 转发主机端口到设备端口
    $ adb reboot                     # 重启设备
    $ adb reboot recovery            # 重启到 recovery 模式
    $ adb reboot bootloader          # 重启到 bootloader

关于卸载补充下，譬如安装 opera mobile，adb uninstall 后边的参数不应该是 .apk 文件，而是包名，即 AndroidMainifest.xml 文件中 <manifest> 节点下，package 元素所指定的名字。或者 adb shell 进设备看下

```
# cd .data/app
# ls
......
com.opera.browser-1.apk
......
```

那么卸载就是

    adb uninstall com.opera.browser

Archlinux 下配置也很简单，照着 wiki 跑就是，不过最后运行 android 时有个错误：

```
^_^ /opt/android-sdk/platform-tools $ android
Exception in thread "main" java.lang.UnsupportedClassVersionError: org/eclipse/swt/widgets/Display : Unsupported major.minor version 51.0
        at java.lang.ClassLoader.defineClass1(Native Method)
        at java.lang.ClassLoader.defineClass(ClassLoader.java:634)
        at java.security.SecureClassLoader.defineClass(SecureClassLoader.java:142)
        at java.net.URLClassLoader.defineClass(URLClassLoader.java:277)
        at java.net.URLClassLoader.access$000(URLClassLoader.java:73)
        at java.net.URLClassLoader$1.run(URLClassLoader.java:212)
        at java.security.AccessController.doPrivileged(Native Method)
        at java.net.URLClassLoader.findClass(URLClassLoader.java:205)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:321)
        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:294)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:266)
        at com.android.sdkmanager.Main.showSdkManagerWindow(Main.java:328)
        at com.android.sdkmanager.Main.doAction(Main.java:316)
        at com.android.sdkmanager.Main.run(Main.java:118)
        at com.android.sdkmanager.Main.main(Main.java:101)
```

两个方案：

- 降级 swt (3.7.2-1 => 3.7.1-1)
- jre7-openjdk 替换 openjdk6

参考资料：

- [http://www.startux.de/linux/75-using-the-android-sdk-on-gentoo](http://www.startux.de/linux/75-using-the-android-sdk-on-gentoo)
- [https://bugs.archlinux.org/task/30295](https://bugs.archlinux.org/task/30295)
