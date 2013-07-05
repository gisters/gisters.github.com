---
layout: post
title: "Jekyll 的配置"
description: "Jekyll配置方法的官方Wiki文档，希望对大家有帮助。"
keywords: "jekyll, 配置"
category: Internet
tags: [Jekyll, Configuration, Usage]
---
{% include JB/setup %}

> 原文地址：[https://github.com/mojombo/jekyll/wiki/configuration](https://github.com/mojombo/jekyll/wiki/configuration)

Jekyll 能够让你用你能想象出的任何方式来构造你的网站。下面是一个现在所支持的配置选项的列表。这些设置均能够通过在根目录下的 `_config.yml` 文件指定。**Jekyll** 同样可以在命令行中指定这些选项。当配置出现矛盾时，它们的优先关系是：

<!-- more -->
1. 命令行中的参数
2. 配置文件中的设置
3. 默认选项

### 配置选项

<table class="table table-bordered">
  <thead>
    <tr>
      <th>设置</th>
      <th>配置选项</th>
      <th>命令行参数</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>安全设置</td>
      <td>safe:[boolean]</td>
      <td>--safe</td>
      <td>禁用定制插件</td>
    </tr>
    <tr>
      <td>重新生成</td>
      <td>auto:[boolean]</td>
      <td>--no-auto --auto</td>
      <td>当文件发生更改时，禁用或启用Jekyll的重新生成功能</td>
    </tr>
    <tr>
      <td>本地服务器</td>
      <td>server:[boolean]</td>
      <td>--server</td>
      <td>启动一个用于托管你_site目录的服务器</td>
    </tr>
    <tr>
      <td>本地服务器端口</td>
      <td>server_port:[integer]</td>
      <td>--server[port]</td>
      <td>改变Jekyll服务的端口</td>
    </tr>
    <tr>
      <td>基准网址</td>
      <td>baseurl:[BASE_URL]</td>
      <td>--base-url[url]</td>
      <td>使用指定的基准网址来运行网站</td>
    </tr>
    <tr>
      <td>网址</td>
      <td>url:[URL]</td>
      <td>--url[url]</td>
      <td>设置站点的网址，这对环境改变时很有用</td>
    </tr>
    <tr>
      <td>站点路径</td>
      <td>destination:[dir]/td>
      <td>jekyll[dest]</td>
      <td>更改Jekyll的写入目录</td>
    </tr>
    <tr>
      <td>站点源路径</td>
      <td>source:[dir]</td>
      <td>jekyll[source][dest]</td>
      <td>更改Jekyll处理文件的目录</td>
    </tr>
    <tr>
      <td>Markdown渲染引擎</td>
      <td>markdown:[engine]</td>
      <td>--rdiscount或--kramdown或--redcarpet</td>
      <td>使用RDiscout或者[engine]指定的渲染引擎代替Markdown默认引擎</td>
    </tr>
    <tr>
      <td>Pygments</td>
      <td>pygments:[boolean]</td>
      <td>--pygments</td>
      <td>启用Pygments来处理代码高亮</td>
    </tr>
    <tr>
      <td>提前发布</td>
      <td>future:[boolean]</td>
      <td>--no-future --future</td>
      <td>在发布文章时使用一个未来的日期</td>
    </tr>
    <tr>
      <td>LSI</td>
      <td>lis:[boolean</td>
      <td>--lsi</td>
      <td>产生相关页面的索引</td>
    </tr>
    <tr>
      <td>固定链接</td>
      <td>permalink:[style]</td>
      <td>--permalink=[style]</td>
      <td>控制文章的固定链接地址</td>
    </tr>
    <tr>
      <td>分页</td>
      <td>paginate:[per_page]</td>
      <td>--paginate[per_page]</td>
      <td>将你的博客文章分成多个子目录:”page2”,”page3”,…“pageN”</td>
    </tr>
    <tr>
      <td>排除</td>
      <td>exlcude:[dir1,file1,dir2]</td>
      <td></td>
      <td>不需要进行转换的文件列表</td>
    </tr>
    <tr>
      <td>包括</td>
      <td>include:[dir1,file1,dir2]</td>
      <td></td>
      <td>指定需要转换的特殊文件和目录列表。因为所有以”.”开始的文件默认都不会进行转换，就像.htaccess文件</td>
    </tr>
    <tr>
      <td>文章限制</td>
      <td>limit_posts:[max_posts]</td>
      <td>--limit_posts:[max_posts]</td>
      <td>限制博客文章发布的数量</td>
    </tr>
  </tbody>
</table>

### 默认选项

注意：在配置文件中你不能使用tabs键，这将会要么导致解析错误，要么将会使用默认设置。

```ruby
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

转自：[【译文】Jekyll的配置](http://zhouyichu.com/%E7%BF%BB%E8%AF%91/2012/11/28/Jekyll-Wiki-Configuration.html)