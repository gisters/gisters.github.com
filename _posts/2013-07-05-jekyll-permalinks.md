---
layout: post
title: "Jekyll 固定链接"
description: "Jekyll固定链接的官方说明文档，希望对大家有所帮助。"
keywords: "jekyll, permalinks"
category: Internet
tags: [Jekyll, Usage]
---
{% include JB/setup %}

> 原文地址：[https://github.com/mojombo/jekyll/wiki/Permalinks](https://github.com/mojombo/jekyll/wiki/Permalinks)

**Jekyll**支持以一种灵活的方式来定制你站点的URL路径。你可以通过 [配置文件](/internet/2013-07/jekyll-configuration.html) 和 [YAML前置数据](/internet/2013-07/jekyll-yaml-front-matter.html) 来指定你站点的每个页面的固定链接。你可以使用内建的风格来创建你的链接，或者你也可以自己定制。默认的风格总是`date`。

**注意**:即使 `--auto` 选项被打开了，当你在 Jekyll 运行时修改其固定链接的风格，你需要重新启动 Jekyll 才能使新的风格生效。

<!-- more -->
### 模板变量

<table class="table table-bordered">
  <thead>
    <tr>
      <th>变量</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>year</td>
      <td>从文件名中获取的年份</td>
    </tr>
    <tr>
      <td>month</td>
      <td>从文件名中获取的月份</td>
    </tr>
    <tr>
      <td>day</td>
      <td>从文件名中获取的日期</td>
    </tr>
    <tr>
      <td>title</td>
      <td>从文件名中获取的标题</td>
    </tr>
    <tr>
      <td>categories</td>
      <td>当前文章指定的分类。Jekyll自动将它解析到URL的双斜杠之间。如果没有分类，那将会被忽略。</td>
    </tr>
    <tr>
      <td>i_month</td>
      <td>从文件名中获取没有前导零的月份</td>
    </tr>
    <tr>
      <td>i_day</td>
      <td>从文件名中获取没有前导零的日期</td>
    </tr>
  </tbody>
</table>

### 内建风格

<table class="table table-bordered">
  <thead>
    <tr>
      <th>变量</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>date</td>
      <td>/:categories/:year/:month/:day/:title.html</td>
    </tr>
    <tr>
      <td>pretty</td>
      <td>/:categories/:year/:month/:day/:title/</td>
    </tr>
    <tr>
      <td>none</td>
      <td>/:categories/:title.html</td>
    </tr>
  </tbody>
</table>

### 例子

假设文章的名字是：`/2009-4-29-slap-chop.textile`

<table class="table table-bordered">
  <thead>
    <tr>
      <th>变量</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>none</td>
      <td>2009/04/29/slap-chop.html</td>
    </tr>
    <tr>
      <td>permalink: pretty</td>
      <td>2009/04/29/slap-chop/index.html</td>
    </tr>
    <tr>
      <td>permalink: /:month-:day-:year/:title.html</td>
      <td>04-29-2009/slap-chop.html</td>
    </tr>
    <tr>
      <td>permalink: /blog/:year/:month/:day/:title</td>
      <td>/blog/2009/04/29/slap-chop/index.html</td>
    </tr>
  </tbody>
</table>

转自：[【译文】Jekyll固定链接](http://zhouyichu.com/%E7%BF%BB%E8%AF%91/2012/12/05/Jekyll-Wiki-Permalinks.html)