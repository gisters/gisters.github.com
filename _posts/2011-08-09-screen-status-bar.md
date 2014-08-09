---
layout: post
title: "Screen 状态栏配置"
description: "GNU 的s creen 是一个很好的工具。如果需要经常或者大量的登录 linux 服务器进行操作，screen 或许还是一个不可或缺的好工具"
keywords: "screen, linux,状态栏, 配置"
category: Linux
tags: [Screen, Configuration]
---

GNU 的 screen 是一个很好的工具。如果需要经常或者大量的登录 linux 服务器进行操作，screen 或许还是一个不可或缺的好工具。

本文主要想探讨的主要问题是 screen 的配置脚本中如何自由定制状态栏的内容，也即 `hardstatus` 的内容定制。

本文所讨论的问题,对于 screen 工具的使用来说，基本没有什么指导作用，甚至可以认为是一个很无聊的问题，所以如果你是想找如何入门使用 screen 的话，那么，没有必要继续往下看了，可以参考网上很多同志的其他的介绍文档。

此 status bar 能够用来显示很多的东西，内容可以自己定制。本文主要讨论如何自己来定制下面的这样一个 `hardstatus` 栏或者 `caption` 栏。

<!-- more -->

![Screen]({{ site.qiniudn }}/images/2011/08/screen.png "screen")

如果我们在 screen 下工作的时候,有一个地方提醒一下“你现在是在screen下”，那改多好呢？这就是我研究 hardstatus 栏的写法的目的，或者说是出发点也行。到底怎么写呢？主要是原来我自己也不是很清楚应该怎么弄，然后中文的各种介绍就是估计只是复制粘贴了一些现成的示例，所以导致认为设置 `hardstatus` 栏很难。

但是看了英文的原文的参考资料，就发现，其实没什么的。也蛮简单的，只是稍稍有一些麻烦。

比如说网上比较流行的一个配置是这样的：

```ini
hardstatus on
hardstatus alwayslastline
caption always "%?%F%{-b 4w}%:%{-b bb}%? %H | %l | %m-%d %c |%?%F%{-b 4w}%?%L=%-Lw%45>%{-b w4}%n%f* %t%{-}%+Lw%-0<"
```

下面的一个表是关于属性的设置的，其实也就是文字的颜色相关的一些设置

    `{+}`    增加此属性
    `{-}`    减少此属性，单独用时，表示回复到上次属性改变之前的设置值
    `{!}`    反转现在的属性
    `{=}`    改变当前属性,应用此处指定的属性
    `d`    昏暗效果
    `u`    下划线
    `b`    黑体
    `r`    反转
    `s`    突出显示
    `B`    闪烁效果
    `k`    黑色
    `r`    红色
    `g`    绿色
    `y`    黄色
    `b`    蓝色
    `m`    品红
    `c`    蓝绿
    `w`    白色
    `d`    默认颜色
    `.`    不改变颜色

注意，颜色的设置有前景色和背景色之分，也就是字体的颜色和背景的颜色。`%{= bm}`，这样的表达式表达的是背景色被改为 `b`，前景色被改为 `m`。(就是这样的顺序)，如果只指定一个颜色而非两个颜色的话，那么改变的是前景色，如果使用了 `!`，那么改变的将是背景色。也可以使用 `.` 来表示不改变颜色，如 `%{= b.}` 表示改变背景色而不改变前景色

几个例子:

- `%{+b r}` 表示设置背景色为黑体的红色.就是加重的红色
- `%{= yd}` 表示设置背景色为黄色,前景色为默认色
- `%{= y.}` 背景色设置为黄色,前景色不变

下面的就是我的配置

```ini
hardstatus on
hardstatus alwayslastline
hardstatus string "%{= G}%-Lw%{= .Y}%50> %n*%f %t%{= G}%+Lw%< %{= G}%-=%D %c:%s %m/%d/%Y"
startup_message off
vbell off
autodetach on
msgwait 1
shell bash
termcapinfo xterm|xterms|xs|rxvt|urxvt|tila ti@:te@
bindkey -k F1 prev
bindkey -k F2 next
```

参考文档: [http://www.gnu.org/software/screen/manual/screen.html#Message-Line](http://www.gnu.org/software/screen/manual/screen.html#Message-Line)
