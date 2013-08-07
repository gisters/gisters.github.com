---
layout: post
title: "CSS 的预处理器 SASS"
description: "sass 的一些介绍"
keywords: "scss, css"
category: Internet
tags: [style]
---
{% include JB/setup %}

最近经常接触 [Jekyll](http://jekyllrb.com/)，进而又认识到 [Sass](http://sass-lang.com/)（从 Sass 3 开始的新语法规则被称为 SCSS，之前的语法规则为 Syntaxes） 这个 CSS 的处理器，同时 [Compass](http://compass-style.org/) 又是一个高效的开发 SASS 的利器。

闲话少说，安装

    $ gem install sass
    $ gem install compass

<!-- more -->
创建一个新项目

    $ compass create .

其中 config.rb 文件可以对之进行一些修改

实时监控 Sass 目录，使之修改保存后，即可编译成对应目录的 css 

    $ compass watch .

余下的晚上补充
