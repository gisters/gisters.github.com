---
layout: post
title: "Jekyll 的使用"
description: "Jekyll使用方法的官方Wiki文档。希望对大家有所帮助。"
keywords: "jekyll, 方法"
category: Internet
tags: [Jekyll, Usage]
---

> 原文地址：[https://github.com/mojombo/jekyll/wiki/usage](https://github.com/mojombo/jekyll/wiki/usage)

### 应用

一旦 [安装]({% post_url 2013-07-06-jekyll-install %}) 好 **jekyll**，建立一个 **Jekyll** 网站通常有以下几个步骤：

1. 建立网站的基本结构
2. 创建几篇文章，或者从你以前的博客平台 [导入]({% post_url 2013-07-06-blog-migrations-to-jekyll %})
3. 在本地运行并查看你的网站效果
4. 部署你的网站

<!-- more -->
### 基础结构

**Jekyll** 的核心是一个文本转换引擎。它是基于这样的思路：把用你最喜欢的文本标记语言书写的文本提交给这个系统，在此处，文本标记语言可以是 **Markdown** , **Textile** 或者甚至是纯 **HTML** 语言，它将会把文本和一个或多个布局文件合并。在这个过程中，你可以修改你站点的URL路径，可以决定什么样的数据将出现在你的博客布局中，同时也可以完成一些其他事情。这是严格地按照正在编辑的文件中的设置完成的，最终的结果是面向网络的Web界面，即HTML文件。

一个基本的 **Jekyll** 站点结构通常如下所示：

```bash
.
|-- _config.yml
|-- _includes
|-- _layouts
|   |-- default.html
|   `-- post.html
|-- _posts
|   |-- 2007-10-29-why-every-programmer-should-play-nethack.textile
|   `-- 2009-04-26-barcamp-boston-4-roundup.textile
|-- _site
`-- index.html

```

每个文件的概述如下：

#### \_config.yml

这个文件用来存储 [配置]({% post_url 2013-07-05-jekyll-configuration %}) 文件的数据，其中的大多数选项都能通过命令行中的指令来执行，但是把它们写入配置文件中，你就可以不必去记忆它们了。

#### \_includes

这个目录存放能够被你的 **_layouts** 和 **_posts** 合并、匹配的文件，用来提高重用率。liquid 标签 `include file.ext` 能够用来引入局部模板文件 `_include/file.ext`。

#### \_layouts

该目录用来存放博客文章将会插入的网页布局模板，页面布局基于类似博客平台的“一个接一个”的原则，通过 [YAML 前置数据]({% post_url 2013-07-05-jekyll-yaml-front-matter %}) 来选择模板。liquid 标签 `content` 用于在布局页面中插入博客文章的内容。

#### \_posts

该目录下存放的可以说是你的动态内容，这些文件的格式很重要，这些文件的格式必须遵循 `YEAR-MONTH-DAY-title.MARKUP`。你的博客文章的永久链接地址能够自动适应你发布的文章，但是文章的发布时间和所使用的标记语言只能唯一的由文件名所决定。

#### \_site

这个目录下存放的是 **Jekyll** 已经生成好了的站点文件。将这个目录添加到你的`.gitignore`文件中应该是一个好主意。

#### index.html 页面文件和其他的 HTML/Markdown/Textile 文件

**Jekyll** 将会转换头部拥有 [YAML 前置数据]({% post_url 2013-07-05-jekyll-yaml-front-matter %}) 数据的所有文件。这个规则对于站点根目录和所有其他目录下的 `.html`，`markdown`，`.md` 文件都适用。

#### 其他文件/目录

除了以上列出的所有目录以外的文件和目录都将会被原模原样的转换。例如，你可以建立 `css` 文件夹，`favicon.ico` 文件等等。如果你对 **Jekyll** 如何实现静态布局感到好奇，这里有不少基于 **Jekyll** 的 [网站](https://github.com/mojombo/jekyll/wiki/Sites) 可供参考。

这些目录下的所有文件都会根据上面提到的针对根目录下的文件的转换规则进行转换。

#### 运行Jekyll

通常这是通过跟随gem一起安装的可执行文件 `jekyll` 来运行的。为了运行你的网站服务，你需要运行：

```bash
jekyll --server
```

如果你打算在前端开发中使用 **jekyll** 服务，你也会需要 `--auto` 选项（既可以通过命令行设置也可以将其写入 _config.yml 配置文件）来查看文件改动。

然后，在浏览器中访问 `http://0.0.0.0:4000`。[这里]({% post_url 2013-07-05-jekyll-configuration %}) 还有更多为你提供的参数选项。

在 **Debian** 或 **Ubuntu** 上，你需要将 `/var/lib/gems/1.8/bin/` 添加到你的 `path` 路径中。

#### base-url 选项

如果你像这样使用 base-url 选项：

```bash
jekyll --server --base-url '/blog'
```

确保你的访问地址是

`http://localhost:4000/blog/index.html`

直接访问

`http://localhost:4000/blog`

是不行的。

#### 部署

因为 **Jekyll** 只是生成包含 HTML 的目录，所以它能够在任何已知的网络服务器上运行。针对一些特殊的应用场景的部署说明，请查看 [部署](https://github.com/mojombo/jekyll/wiki/Deployment)页面。

转自：[【译文】Jekyll的使用](http://zhouyichu.com/%E7%BF%BB%E8%AF%91/Jekyll-Wiki-Usage.html)
