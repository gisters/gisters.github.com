---
layout: post
title: "Import and Display 常用方法"
description: "Import and Display 常用方法"
keywords: import, display, imagemagick
category: Linux
tags: [Import, Display, Usage, Media]
---

Import & Display 也是 Imagemagick 中的两个常用的组件。

#### Import 通常用于截屏，譬如

鼠标选取范围截图：

    $ import /path/output.png

延迟 n 秒截图：

    $ import -pause n /path/output.png

<!-- more -->
输出缩略图：

    $ import -resize 50% /path/output.png
    $ import -thumbnail 50% /path/output.png

#### Display 是用于显示图片，譬如

最简单的显示图片

    $ display /path/input.png

显示一堆图片

    $ display /path/*

只看一个 RGB 图片的红色通道

    $ display -gamma 1.0,0.0,0.0 image.miff

完整的显示 PostScript 图片

    $ display -page letter image.ps

详细用法，均可以用-help参数来查询
