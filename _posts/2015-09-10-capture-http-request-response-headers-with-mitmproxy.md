---
layout: post
title: "使用 mitmproxy 进行 HTTP 分析"
category: Internet
tags: [Proxy]
---

之前使用的是 Wireshark 进行 HTTP 的一些分析，功能很全面，不过有些大材小用。同类型的简化工具其实很多，甚至 Firefox & Google Chrome 本身就自带，只是浏览器自带的工具又无法满足我们的需求，下面介绍下 [mitmproxy](http://mitmproxy.org) 工具，简单又不失强大。

![mitmproxy 1](http://cdn.09hd.com/images/2015/09/mitmproxy-1.png)

<!-- more -->
mitmproxy 是一款基于 Python，支持 ssl 的中间人代理框架，他提供了一个控制台用于动态拦截和编辑 HTTP/HTTPS 数据包。官方介绍：

>An interactive console program that allows traffic flows to be intercepted, inspected, modified and replayed.

mitmproxy 包含 3 款工具：

- mitmproxy: 带交互的终端界面
- mitmdump: mitmproxy 的命令行版本，类似于 tcpdump
- libmproxy: 用于 mitmproxy/mitmdump 的库

安装他非常简单，Linux 用户直接通过包管理命令来安装即可，OS X 用户通过 Homebrew 工具进行安装

	brew install mitmproxy

当然，你也可以通过 pip 进行安装

	pip install mitmproxy

使用上非常简单，移动方向类似 vi，使用 `h`、`j`、`k`、`l` 来进行操作向左、向上、向下、向右，以 `q` 键来返回，回车键进入。

在 Request/Response 界面按 `e` 键进入编辑，`m` 键进入显示模式选择，`tab` 键切换 Request/Response/Detail。

![mitmproxy 1](http://cdn.09hd.com/images/2015/09/mitmproxy-2.png)

![mitmproxy 1](http://cdn.09hd.com/images/2015/09/mitmproxy-3.png)

![mitmproxy 1](http://cdn.09hd.com/images/2015/09/mitmproxy-4.png)

详情请参考：<http://mitmproxy.org/doc/index.html>
