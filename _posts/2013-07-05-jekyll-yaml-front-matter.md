---
layout: post
title: "Jekyll 的 YAML 前置数据"
description: "这是Jekyll官方Wiki上关于YAML前置数据的说明，希望对大家有所帮助。"
keywords: "jekyll, yml, 数据"
category: Internet
tags: [Jekyll, Usage]
---

> 原文地址：[https://github.com/mojombo/jekyll/wiki/YAML-Front-Matter](https://github.com/mojombo/jekyll/wiki/YAML-Front-Matter)

所有包含 [YAML](http://yaml.org/) 前置数据块的文件都会被 **Jekyll** 当做特殊文件来处理。这些前置数据必须存在于文件的首部，他们的格式是这样的：

<!-- more -->
```yaml
---
layout: post
title: Blogging Like a Hacker
---
```

在三条虚线之间，你可以设置一些预定义的变量（可以查看下面的参考说明）或者是你自己定义的变量。

注意：这很**重要**（尤其是对 Windows 用户）

当你使用 `UTF-8` 编码来编辑你的文件时，请确保没有 `BOM` 的头部字符在你的文件中，否则整个都会奔溃。

### 预定义的全局变量

|变量|描述
|:---|:---
|layout|这个变量是用来设置选用的模板文件。使用模板文件的文件名，但不包括扩展名，模板文件必须存在于 `_layout` 目录中。
|permalink|如果你需要你的 URL 地址和默认的 `/年份/月份/日期/标题.html` 不同，那你就需要设置这个选项，它将会当做最终的 URL 地址。

### 自定义数据

在转换过程中，所有在前置数据中的非预定义变量都会被一起提交给 **Liquid** 模板引擎。举例来说，如果你设置了变量：title，你可以在你的布局模板文件中使用这个变量来设置页面的标题：

{% raw %}
```html
<title> {{ page.title }} </title>
```
{% endraw %}

### 为文章预定义的前置数据

下面是文章可用的前置数据变量：

|变量|描述
|:---|:---
|date|这个日期变量将会覆盖文件名中的日期，这个变量能够用来确保文章的正确排序

转自：[Jekyll的YAML前置数据](http://zhouyichu.com/%E7%BF%BB%E8%AF%91/Jekyll-Wiki-YAML-Front-Matter.html)
