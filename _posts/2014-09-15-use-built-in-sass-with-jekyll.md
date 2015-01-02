---
layout: post
title: "使用 Jekyll 内置的 Sass 预处理器"
description: "介绍使用 Jekyll 内置的 Sass 预处理器。"
keywords: "jekyll, sass"
category: Internet
tags: [Jekyll, Usage, Sass]
---

之前有介绍过 [CSS 的预处理器 Sass]({% post_url 2013-08-07-convert-scss-to-css %})，今天翻阅 Jekyll 官网发现，Jekyll 本身就内置 Sass 的编译，一些小修改，就剔除了 compass 工具，完美的使用，顺便记录下，希望本文能帮到大家。

如果你也想尝试的话，要确定以下几点

- 你有一个 Jekyll 博客在运行
- 你本地的 Jekyll 版本高于或等于 2.4.0
- 对 Sass 有一些了解

<!-- more -->
一般大家都喜欢将 Jekyll push 到 GitHub Pages 上，那么首先对比下 GitHub Pages 上对版本的要求吧：<https://pages.github.com/versions/>。

一切准备就绪，下面开始安装 Sass：

    $ gem install sass

在你的 `css` 目录下创建 `style.scss`：

    ---
    ---
    // your scss here

那么，在 `$ jekyll build` 编译后，会自动将 `css/style.scss` 转化成 `_site/css/style.css`。

最后只需在你的模版文件的 `<head>` 区域加入以下代码：

```html
<link rel="stylesheet" href="/css/style.css">
```

某些同学，喜欢将 Scss 分割开，最后在 `style.scss` 中使用 `@import` 来引入，当然可以，你可以在 `_sass` 目录下写入你的 Scss，譬如：

    _sass/_one.scss
    _sass/_two.scss

在你的 `_config.yml` 中的适当地方添加如下：

    sass:
        sass_dir: _sass

最后在 `css/style.scss` 中引入：

    ---
    ---

    //Imports
    @import "one";
    @import "two";

注意，两行的 `---` 是必须的，以便让 Jekyll 知晓该前置数据块的文件需要处理。可以详细了解下 [Jekyll 的 YAML 前置数据]({% post_url 2013-07-05-jekyll-yaml-front-matter %})。

`_sass` 是 Jekyll 默认的目录，你可以设置到其他地方，只需 `_config.yml` 中声明好即可：

    sass:
        sass_dir: path/_lib

顺便提一下，内置的 Sass 编译器支持压缩：

    sass:
        sass_dir: path/_lib
        style: :compressed

参考：<http://jekyllrb.com/docs/assets/>
