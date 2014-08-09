---
layout: post
title: "Convert 常用方法"
description: "Convert 是 ImageMagick 中的一个常用组件"
keywords: convert, imagemagick
category: Linux
tags: [Convert, Media, Usage]
---

Convert 是 ImageMagick 中的一个常用组件。

下面列出 Convert 的常用命令

    $ convert input.png output.jgp #转换图像格式
    $ convert -thumbnail 60% input.png output.jgp #创建缩略图
    $ convert -sample 60% input.png output.jgp #创建缩略图
    $ convert -negate input.png output.jgp #单色把图片变为黑白颜色
    $ convert -flip input.png output.jgp #左右翻转
    $ convert -flop input.png output.jgp #反色形成底片的样子
    $ convert -monochrome input.png output.jgp #加噪声
    $ convert -noise 3 input.png output.jgp #油画效果
    $ convert -paint 4 input.png output.jgp #旋转把一张图片，旋转一定的角度：
    $ convert -rotate 30 input.png output.jgp #上面的30，表示向右旋转30度，反之是负数
    $ convert -charcoal 2 input.png output.jgp #形成炭笔或者说是铅笔画的效果
    $ convert -fill color -font font -pointsize 20 -draw "text 10,20 'hello'" input.png output.png

<!-- more -->
给图片增加注释

    $ convert -font monospace -fill red -pointsize 36 -draw 'text 10,50 "ihavanna.org"' Shanghai.jpg comment.jpg

特殊效果

    $ convert -charcoal 2 input.jpg output.jpg #炭笔
    $ convert -colorize 255 input.jpg output.jpg #着色 可以指定三种颜色 red/green/blue
    $ convert -implode 4 input.jpg output.jpg #内爆效果
    $ convert -solarize 42 input.jpg output.jpg #曝光，模拟胶片曝光
    $ convert -spread 5 input.jpg output.jpg #随机移动，参数是位移大小

对图片的边缘的处理

首先创建一个固定宽高比，白色背景的图片，并写上 “ihavanna.org”

    $ convert -size 97×19 null:white ihavanna.png
    $ convert -font dejavu -fill black -pointsize 16 -draw “text 4,14 ‘ihavanna.org’” ihavanna.png ihavanna.png

![Ihavanna]({{ site.qiniudn }}/images/2011/12/ihavanna.png "ihavanna")

增加色变

    $ convert -bordercolor red -border 2×2 image.jpg image.gif

![ihavanna-border-color]({{ site.qiniudn }}/images/2011/12/ihavanna-bordercolor.png "ihavanna-bordercolor")

加亮或变暗图片边缘，已增强 3d 效果

    $ convert -raise 9 image.jpg image.gif

![ihavanna-raise]({{ site.qiniudn }}/images/2011/12/ihavanna-raise.png "ihavanna-raise")

在图片周围增加装饰性框架

    $ convert -mattecolor gray -frame 2×2 image.jpg image.gif

![ihavanna-matte-color]({{ site.qiniudn }}/images/2011/12/ihavanna-mattecolor.png "ihavanna-mattecolor")

图片边缘增加升降斜角

    $ convert -mattecolor gray -frame 2×2+0+2 image.jpg image.gif
    $ convert -mattecolor gray -frame 2×2+2+0 image.jpg image.gif

![ihavanna-0+2]({{ site.qiniudn }}/images/2011/12/ihavanna-0+2.png "ihavanna-0+2")
![ihavanna-2+0]({{ site.qiniudn }}/images/2011/12/ihavanna-2+0.png "ihavanna-2+0")

将gif动画分拆成一系列静态图片

    $ convert animation.gif frame%02d.gif

生成 png 格式的 email 图片

    $ convert -size 185x19 null:white email.png
    $ convert -font monospace -fill black -pointsize 16 -draw 'text 4,14 "youremail@yourdomain"' email.png email.png
    $ convert -font monospace -fill red -pointsize 16 -draw \'text 3,13 \"youremail@yourdomain\"\' email.png email.png

注意：文中的乘号可以用小写x替代
