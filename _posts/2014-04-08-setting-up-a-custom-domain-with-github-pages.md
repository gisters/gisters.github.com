---
layout: post
title: "GitHub Pages 配置域名"
description: "User Pages 与 Project Pages 域名域名配置的区别"
keywords: "github, user pages, project pages, domain, 域名"
category: Internet
tags: [GitHub, Domain]
---

最近为 [zhlist](http://github.com/zhlist/zhlist) 的 `gh-pages`  分支配置了个域名。仔细的阅读了[这篇文章](https://help.github.com/articles/setting-up-a-custom-domain-with-pages)，终于搞清了 **User Pages** 与 **Project Pages** 的区别。

- **User Pages** 下，`CNAME` 是放置在 `master` 分支下。
- **Project Pages** 下，`CNAME` 是放置在 `gh-pages` 分支下。

在同时拥有 **User Pages**、**Project Pages** 情况下，`//username.github.io/projectname` 将不会转向到 **Project Pages** 中 `gh-pages` 分支下设置的域名。而会以 **User Pages** 中 `master` 分支下设置的域名，加上 `/projectname` 路径来体现。

<!-- more -->
譬如：

1. `zhlist/zhlist.github.io` 的 `master` 分支配置域名 `zhlist.org`；
2. `zhlist/project` 的 `gh-pages` 分支同时配置域名 `www.zhlist.org`。

则 `zhlist/project` 的访问 URL 则为 `//zhlist.org/project`，而不会是 `//www.zhlist.org`。

也就是说，在没有 `username.github.io` 这个 **User Pages** 情况下，你可以为你的 **Project Pages 1**，**Project Pages 2**，**Project Pages 3**...... 配置不同的域名。

一旦建立 `username.github.io`，则其他所有的 **Project Pages** 只能通过 `//username.github.io/projectname` 来访问了。当然所有的前提是，`username.github.io` 存在 `master` 分支，`Project` 存在 `gh-pages` 分支。
