---
layout: post
title: "Gentoo overlay 的简单介绍"
category: Linux
tags: [Gentoo]
---

由于接触 Gentoo 的时间比自建博客的时间早，故对 Gentoo 知识点的记录非常少，以下记录下 layman 的一些使用。

layman 是 Gentoo 下的一款管理第三方 overlay 工具，通过 layman 可以方便的安装各社区创建的 overlay。

安装很方便：

    USE="git" emerge -av app-portage/layman

具体需要开启哪些 use flag，完全是根据各 overlay 用哪种工具维护的，譬如 git、cvs、subversion、mercurial 等等。 

<!-- more -->
layman 的配置非常方便，几乎不用介入修改。譬如作为中文区用户，安装 layman 以后，都会增加一个 overlay：

	layman -f -a gentoo-zh

一些常用的参数如下，更多参数可以通过 `layman -h` 来查看：

```
-a <name>       # 添加一个的 overlay
-d <name>       # 删除一个的 overlay
-D <name>       # 关闭一个的 overlay
-E <name>       # 开启一个的 overlay
-f <name>       # 获取远程的 overlay 列表
-i <name>       # 显示一个 overlay 信息
-L <name>       # 显示远程 overlay 列表
-l              # 显示本地 overlay 列表
-s <name>       # 同步一个本地 overlay
-S              # 同步所有本地 overlay
```

在 app-portage/layman-2.3.0 以及以后的版本，在通过 `layman -f -a <name>` 后会自动在 **/etc/portage/repos.conf/** 下生成一文件，譬如前面我们已经添加了 gentoo-zh overlay，则内容为：

```
[gentoo-zh]
priority = 50
location = /var/lib/layman/gentoo-zh
auto-sync = No
layman-type = git
```

如果你需要在更新 portage 树的同时也更新第三方的 overlay，那么只需将以上内容修改为如下即可：

```
[gentoo-zh]
priority = 50
location = /var/lib/layman/gentoo-zh
auto-sync = Yes
sync-type = git
sync-uri = git://github.com/microcai/gentoo-zh.git
```

对于 app-portage/layman-2.3.0 之前升级上来的用户而言，如果之前就添加了各个 overlay，那么通过 `layman-updater -R` 重建一下即可。

很多 Gentoo user 喜欢自己维护一个个人的 overlay，添加进去也很方便，譬如我的：

    layman -o https://raw.githubusercontent.com/Ihavee/overlay/master/repositories.xml -f -a Havee

当然 repositories.xml 必须包含如下的基本信息：

```
<?xml version="1.0" ?>

<repositories version="1.0">
    <repo priority="50" quality="experimental" status="unofficial">
	<name>Havee</name>
	<description>Havee's gentoo overlay</description>
	<homepage>http://github.com/Ihavee/overlay</homepage>
	<owner>
	    <email>registerdedicated@yeah.net</email>
	</owner>
	<source type="git">git://github.com/Ihavee/overlay.git</source>
    </repo>
</repositories>
```

创建一个自己维护的 overlay，可以通过 **layman-overlay-maker** 命令，然后根据提示一步一步的去创建，最后通过 `layman -a <name>` 加进去。

更详细的说明可以参考：<https://wiki.gentoo.org/wiki/Layman>
