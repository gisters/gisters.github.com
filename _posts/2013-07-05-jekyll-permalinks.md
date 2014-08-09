---
layout: post
title: "Jekyll 固定链接"
description: "Jekyll固定链接的官方说明文档，希望对大家有所帮助。"
keywords: "jekyll, permalinks"
category: Internet
tags: [Jekyll, Usage]
---

> 原文地址：[https://github.com/mojombo/jekyll/wiki/Permalinks](https://github.com/mojombo/jekyll/wiki/Permalinks)

**Jekyll**支持以一种灵活的方式来定制你站点的URL路径。你可以通过 [配置文件]({% post_url 2013-07-05-jekyll-configuration %}) 和 [YAML前置数据]({% post_url 2013-07-05-jekyll-yaml-front-matter %}) 来指定你站点的每个页面的固定链接。你可以使用内建的风格来创建你的链接，或者你也可以自己定制。默认的风格总是`date`。

**注意**:即使 `--auto` 选项被打开了，当你在 Jekyll 运行时修改其固定链接的风格，你需要重新启动 Jekyll 才能使新的风格生效。

<!-- more -->
### 模板变量

|变量|描述|
:---|:---
|year|从文件名中获取的年份|
|month|从文件名中获取的月份|
|day|从文件名中获取的日期|
|title|从文件名中获取的标题|
|categories|当前文章指定的分类。Jekyll 自动将它解析到 URL 的双斜杠之间。如果没有分类，那将会被忽略。|
|i_month|从文件名中获取没有前导零的月份|
|i_day|从文件名中获取没有前导零的日期|


### 内建风格

|变量|描述|
|:---|:---
|date|/:categories/:year/:month/:day/:title.html|
|pretty|/:categories/:year/:month/:day/:title/|
|none|/:categories/:title.html|

### 例子

假设文章的名字是：`/2009-4-29-slap-chop.textile`

|变量|描述|
|:---|:---
|none|/2009/04/29/slap-chop.html|
|permalink: pretty|/2009/04/29/slap-chop/index.html|
|permalink: /:month-:day-:year/:title.html|/04-29-2009/slap-chop.html|
|permalink: /blog/:year/:month/:day/:title|/blog/2009/04/29/slap-chop/index.html|

转自：[【译文】Jekyll固定链接](http://zhouyichu.com/%E7%BF%BB%E8%AF%91/Jekyll-Wiki-Permalinks.html)
