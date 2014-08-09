---
layout: post
title: "OS X 下本地化目录"
description: "很多 Mac 中文用户应该有这样的感受，Finder 中很多中文目录，在 Terminal 下其实是英文名称。创建自己的目录时，不清楚究竟创建中文还是英文目录，导致在 Terminal 下 cd 进目录的时候总是在不听的切换输入法，非常繁琐。"
keywords: "mac, osx, 目录,多语言"
category: Mac
tags: [Localized]
---

很多 Mac 中文用户应该有这样的感受，Finder 中很多中文目录，在 Terminal 下其实是英文名称。创建自己的目录时，不清楚究竟创建中文还是英文目录，导致在 Terminal 下 cd 进目录的时候总是在不停的切换输入法，非常繁琐。

实际上 OS X 本身就内建了多语言结构，有两个地方可以去自定义

譬如创建个英文目录 "Virtual Machines", 想以中文 "虚拟机" 来显示。

<!-- more -->
#### 一. 系统级

在目录 `/System/Library/CoreServices/SystemFolderLocalizations/zh_CN.lproj` 的文件 `SystemFolderLocalizations.strings` 定义了常用的中文目录名称的显示，内容如下：

```
{   Applications = "应用程序";
    Compositions = "Compositions";
    "Deleted Users" = "已删除的用户";
    Desktop = "桌面";
    Documents = "文稿";
    Downloads = "下载";
    ……
    Sites = "站点";
    System =Sites "系统";
    Users = "用户";
    Utilities = "实用工具";
    "Web Receipts" = "Web Receipts";
}
```

规则很清楚，含空格或特殊字符的目录，请用双引号，如果要中文化 **Virtial Machines** 目录，只需在其中添加如下一行即可：

    "Virtual Machines" = "虚拟机";

随后，在 `Virtual Machines` 目录下创建空文件 `.localized`，告诉 Finder 该目录是 localizing 目录。

最后 `killall Finder` 即可生效。

#### 二. 用户级

在非必要的情况下，我是非常抵触修改系统目录或文件的，一切在用户目录下解决。

以 **Virtual Machines** 目录为例，三步走：

1. 创建 Virtual Machines.localized 目录
2. 在该目录下创建 .localized 目录
3. 在 .localized 目录下创建 zh-Hans.strings 文件

简单来说就是

```bash
    $ mkdir -p Virtual\ Machines.localized/.localized
    $ cd Virtual\ Machines.localized/.localized
    $ touch zh-Hans.strings; vim zh-Hans.strings
```

添加如下行即可：

    "Virtual Machines" = "虚拟机";

同样 `killall Finder` 即可生效。

参考：[https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPInternational/Articles/LocalizingPathnames.html](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPInternational/Articles/LocalizingPathnames.html)

参考文章介绍了汉化 OS X 的一些基础知识点，对汉化 OS X 的 app 非常有帮助。
