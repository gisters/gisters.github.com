---
layout: post
title: "Jekyll 的配置"
description: "Jekyll配置方法的官方Wiki文档，希望对大家有帮助。"
keywords: "jekyll, 配置"
category: Internet
tags: [Jekyll, Configuration, Usage]
---

> 原文地址：[https://github.com/mojombo/jekyll/wiki/configuration](https://github.com/mojombo/jekyll/wiki/configuration)

Jekyll 能够让你用你能想象出的任何方式来构造你的网站。下面是一个现在所支持的配置选项的列表。这些设置均能够通过在根目录下的 `_config.yml` 文件指定。**Jekyll** 同样可以在命令行中指定这些选项。当配置出现矛盾时，它们的优先关系是：

<!-- more -->
1. 命令行中的参数
2. 配置文件中的设置
3. 默认选项

### 配置选项

|设置|配置选项|命令行参数|描述|
|:---|:---|:---|:---
|安全设置|safe:[boolean]|--safe|禁用定制插件|
|重新生成|auto:[boolean]|--no-auto --auto|当文件发生更改时，禁用或启用Jekyll的重新生成功能|
|本地服务器|server:[boolean]|--server|启动一个用于托管你_site目录的服务器|
|本地服务器端口|server_port:[integer]|--server:[port]|改变Jekyll服务的端口|
|基准网址|baseurl:[BASE_URL]|--base-url [url]|使用指定的基准网址来运行网站|
|网址|url:[URL]|--url [url]|设置站点的网址，这对环境改变时很有用|
|站点路径|destination:[dir]|jekyll [dest]|更改Jekyll的写入目录|
|站点源路径|source:[dir]|jekyll [source] [dest]|更改Jekyll处理文件的目录|
|Markdown渲染引擎|markdown:[engine]|--rdiscount或--kramdown或--redcarpet|使用RDiscout或者[engine]指定的渲染引擎代替Markdown默认引擎|
|Pygments|pygments:[boolean]|--pygments|启用Pygments来处理代码高亮|
|提前发布|future:[boolean]|--no-future或--future|在发布文章时使用一个未来的日期|
|LSI|lis:[boolean]|--lsi|产生相关页面的索引|
|固定链接|permalink:[style]|--permalink=[style]|控制文章的固定链接地址|
|分页|paginate:[per_page]|--paginate [per_page]|将你的博客文章分成多个子目录:”page2”,”page3”,…“pageN”|
|排除|exlcude:[dir1,file1,dir2]||不需要进行转换的文件列表|
|包括|include:[dir1,file1,dir2]||指定需要转换的特殊文件和目录列表。因为所有以”.”开始的文件默认都不会进行转换，就像.htaccess文件|
|文章限制|limit_posts:[max_posts]|--limit_posts:[max_posts]|限制博客文章发布的数量|

### 默认选项

注意：在配置文件中你不能使用tabs键，这将会要么导致解析错误，要么将会使用默认设置。

```yaml
safe:false
auto:false
server:false
server_port:4000
baseurl:/jekyll_demo
url:http://localhost:4000

source:.
destination:./_site
plugins:./_plugins

future:true
lsi:false
pygments:false
markdown:maruku
permalink:date

maruku:
	use_tex:false
	use_divs:false
	use_engine:blathtex
	png_dir:images/latex
	png_url:/images/latex

rediscount:
	extension:[]

kramdown:
	auto_ids:treu,
	footnote_nr:1
	entity_output:as_char
	toc_levels:1..6
	use_coderay:false

coderat:
	caderay_wrap:div
	caderay_line_numbers:inline
	caderay_line_numbers_start:1
	caderay_tab_width:4
	caderay_bold_every:10
	caderay_css:style
```

转自：[【译文】Jekyll的配置](http://zhouyichu.com/%E7%BF%BB%E8%AF%91/Jekyll-Wiki-Configuration.html)
