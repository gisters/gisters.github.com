---
layout: post
title: "Wordpress 的 Widget Logic 插件"
description: "Widget Logic 插件给每个 widget 一个扩展控制 Widget logic"
keywords: wordpress, widget
category: Internet
tags: [Wordpress, Widget]
---

#### 插件简介

Widget Logic 插件给每个 widget 一个扩展控制 Widget logic，你可以通过它根据不同页面自定义你的侧边栏内容。

#### 插件使用方法

<!-- more -->
1. 首先在本页下方链接下载 Widget Logic 插件，安装到您的博客上并激活。
2. 激活后进入 外观>小工具 里面发现在可用小工具的最下方多出了一项：Widget Logic options ，勾选 Widget Logic options 这个后面的复选框点击保存。
3. 这时候您就可以根据自己的需要去更改您的侧边栏显示内容了。比如您要修改侧边栏里面的友情链接只在首页显示，那么您只需要在 Widget logic 后面的文本框里面输入：is_home() 就可以了

怎么样？是不是非常简单呢？这样简单的插件对于不太熟悉代码的朋友提供了很大的帮助，赶快试试吧！

#### 插件设置一些常用的标记

    is_home() 主页
    is_single() 文章页
    is_page() 页面
    is_category() 文章分类页
    is_tag() 文章标签页
    is_archive() 归档页
    is_404() 404页
    is_search() 搜索结果页
    is_feed() 订阅页

#### 插件语法

`||` 表示或，`&&` 表示和，`!` 表示非，示例语言如下：

    is_home() 仅主页显示
    !is_home() 除主页以外的页面显示
    !is_category(5) 仅在ID非5的分类显示
    is_home() || is_category(’baked-goods’) 在主页或名称为baked-goods的分类显示
    is_page(’about’) 仅在关于页显示

来自：[http://www.yzznl.cn/archives/236.html](http://www.yzznl.cn/archives/236.html)，转贴过来，好记性不如烂笔头
