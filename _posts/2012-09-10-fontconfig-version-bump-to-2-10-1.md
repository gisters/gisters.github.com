---
layout: post
title: "Fontconfig 升级到 2.10.1 后"
description: "Fontconfig 升级到 2.10.1 后"
keywords: fontconfig, archlinux
category: Linux
tags: [Fontconfig]
---

首先，按照 [https://www.archlinux.org/news/fontconfig-2101-update-manual-intervention-required/](https://www.archlinux.org/news/fontconfig-2101-update-manual-intervention-required/) 提示处理，重新创建一些你使用的字体配置的软链接。

然后，终端启动一些程序会出现一些警告提示：

>Fontconfig warning: "/etc/fonts/conf.d/50-user.conf", line 9: reading configurations from ~/.fonts.conf is deprecated.

解决方法：

    mkdir $XDG_CONFIG_HOME/fontconfig
    mv .fonts.conf $XDG_CONFIG_HOME/fontconfig/fonts.conf

<!-- more -->

神马意思呢，fontconfig2 告诉你，它的配置文件路径是符合 XDG 标准的，`$HOME` 下的 `.fonts.conf` 快快移到 `$XDG_CONFIG_HOME` 下吧。

另外，有些 `fontconfig` 的个人配置会出现一些警告，类似与

>Having multiple values in <test> isn't supported and may not works as expected

配置文件中 `test` 标签不能包含多个 `string` 值，类似于

```xml
<test target="pattern" compare="contains" name="lang">
  <string>zh</string>
  <string>ja</string>
  <string>ko</string>
</test>
```

修改为

```xml
<test target="pattern" compare="contains" name="lang">
  <string>zh</string>
</test>
<test target="pattern" compare="contains" name="lang">
  <string>ja</string>
</test>
<test target="pattern" compare="contains" name="lang">
  <string>ko</string>
</test>
```

参考资料：[https://bbs.archlinux.org/viewtopic.php?pid=1157957#p1157957](https://bbs.archlinux.org/viewtopic.php?pid=1157957#p1157957)
