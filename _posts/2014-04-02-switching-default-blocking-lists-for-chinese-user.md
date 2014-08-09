---
layout: post
title: "Adblock Plus 官方切换 ChinaList"
description: "ChinaList 列表维护中止"
keywords: "adblock, chinalist"
category: Internet
tags: [Adblock, ChinaList]
---

今天整理 Firefox 浏览器扩展的时候，意外发现，[Adblock Plus](https://adblockplus.org) 的[ChinaList](https://github.com/chinalist/chinalist) 列表无法更新，过滤规则列表 404 错误。

![ChinaList 404]({{ site.qiniudn }}/images/2014/04/Chinalist-01.png)

点击主页进去后才发现，ChinaList 项目已经于 **2014-03-26** 起不再维护。这究竟只是一种对 Adblock Plus 官网的抗议行为，还是今后永不再维护，不得而知。

<!-- more -->
事件的起因很简单，Adblock Plus 商业化运作后，有个小选项，**允许一些非侵入式广告**，且默认是勾选状态。这对于一些用户来说，是难以忍受的。我们留在广告中的 cookies 信息，可以轻易的让广告商定位我们，进而是定向推送精准广告，亦或是直接买卖资料就不得而知。而用户是难以相信所谓的行业自律的。所以，很多用户去掉了**允许一些非侵入式广告** 的勾选。另外，另外，加上 ChinaList 民间组织的项目被 Adblock Plus 重定向，ChinaList 项目维护者由于气愤不过，停止了维护。

![ChinaList Announcements]({{ site.qiniudn }}/images/2014/04/Chinalist-03.png)

![ChinaList issues 44]({{ site.qiniudn }}/images/2014/04/Chinalist-02.png)

在[官方声明](https://adblockplus.org/blog/switching-default-blocking-lists-for-chinese-users)中只是公告了停止启用 ChinaList 为默认的中文用户列表，而没有说明重定向已经订阅用户的列表至官方维护列表 **[EasyList China](https://easylist-downloads.adblockplus.org/chinalist+easylist.txt)**（当然，今天4月2日，我这边没有被重定向，而是直接 404，究竟曾今有无重定向不得而知）。这是让人无法理解的，唯一的解释就是，商业上的利益了。至于官方博客中解释的浏览器不会出现白板页面以及更好的过滤视频广告，似乎是欺人之谈。且民间组织有2个作者（30分支）在做贡献，而官方只有3人。官方广告过滤列表的时效性完全没法保障。有多少人去 fork 官方维护的列表不清楚，但清楚的是如果向官方 push， 阻力是肯定存在的。

一句话，Adblock Plus 改变配置中的预设列表地址。当然作为用户来说，可以去订阅官方列表，用户从来都是被选择的。

如果是对此次事件不爽且有能力的用户，也可以直接去 fork chinalist。也可以启用 Adblock Plus 的民间分支 [Adblock Edge](https://bitbucket.org/adstomper/adblockedge/)，普通用户该干嘛干嘛吧。
