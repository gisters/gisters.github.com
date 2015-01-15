---
layout: post
title: "CSS 的预处理器 Sass"
category: Internet
tags: [Sass]
---

最近经常接触 [Jekyll](http://jekyllrb.com/)，进而又认识到 [Sass](http://sass-lang.com/)（从 Sass 3 开始的新语法规则被称为 SCSS，即 Sassy CSS） 这个 CSS 的处理器，同时 [Compass](http://compass-style.org/) 又是一个高效的开发 Sass 的利器。

闲话少说，[RubyGems](http://rubygems.org/) 下的安装

```sh
$ gem install sass
$ gem install compass
```

<!-- more -->
创建一个新项目

```sh
$ cd /your/jekyll/path
$ compass create .
```

其中 config.rb 文件可以对之进行一些修改，譬如输出格式

```ruby
:nested		# 默认值，嵌套缩进
:expanded	# 无缩进、扩展
:compact	# 简洁格式
:compressed	# 压缩后
```

以下是我的配置

```ruby
# Require any additional compass plugins here.

# Set this to the root of your project when deployed:
http_path = "/"
css_dir = "style/css"
sass_dir = "style/_sass"
images_dir = "style/images"
javascripts_dir = "style/js"

# You can select your preferred output style here (can be overridden via the command line):
# output_style = :expanded or :nested or :compact or :compressed
output_style = :compressed
```

当然也可以不运行 `compass create .` 来创建目录，只需 `touch config.rb`，且填充以上内容即可

实时监控 Sass 目录，使之修改保存后，即可编译成对应目录的 CSS 

```sh
$ compass watch .
```

由于此过程会生成 **.sass-cache**目录，提交到代码库时，将 **.sass-cache** 写入 **.gitignore**

```sh
$ cat .gitignore 
_site/*
_theme_packages/*

Thumbs.db
.DS_Store

!.gitkeep

.rbenv-version
.rvmrc
.sass-cache

```

不过，Jekyll 2.4.0 开始内置 Sass 预处理器，可以直接生成页面需要的样式。GitHub Pages 服务已经支持 Jekyll 2.4.0 版本。详情访问：[使用 Jekyll 内置的 Sass 预处理器]({% post_url 2014-09-15-use-built-in-sass-with-jekyll  %})

以下是我的目录结构

```
assets
├── _sass
│   ├── _bootstrap.scss
│   ├── _custom.scss
│   ├── _font-awesome.scss
│   ├── _havee.scss
│   ├── _pygments.scss
│   ├── bootstrap
│   │   ├── _alerts.scss
│   │   ├── _badges.scss
│   │   ├── _breadcrumbs.scss
│   │   ├── _button-groups.scss
│   │   ├── _buttons.scss
│   │   ├── _carousel.scss
│   │   ├── _close.scss
│   │   ├── _code.scss
│   │   ├── _component-animations.scss
│   │   ├── _dropdowns.scss
│   │   ├── _forms.scss
│   │   ├── _glyphicons.scss
│   │   ├── _grid.scss
│   │   ├── _input-groups.scss
│   │   ├── _jumbotron.scss
│   │   ├── _labels.scss
│   │   ├── _list-group.scss
│   │   ├── _media.scss
│   │   ├── _mixins.scss
│   │   ├── _modals.scss
│   │   ├── _navbar.scss
│   │   ├── _navs.scss
│   │   ├── _normalize.scss
│   │   ├── _pager.scss
│   │   ├── _pagination.scss
│   │   ├── _panels.scss
│   │   ├── _popovers.scss
│   │   ├── _print.scss
│   │   ├── _progress-bars.scss
│   │   ├── _responsive-embed.scss
│   │   ├── _responsive-utilities.scss
│   │   ├── _scaffolding.scss
│   │   ├── _tables.scss
│   │   ├── _theme.scss
│   │   ├── _thumbnails.scss
│   │   ├── _tooltip.scss
│   │   ├── _type.scss
│   │   ├── _utilities.scss
│   │   ├── _variables.scss
│   │   ├── _wells.scss
│   │   └── mixins
│   │       ├── _alerts.scss
│   │       ├── _background-variant.scss
│   │       ├── _border-radius.scss
│   │       ├── _buttons.scss
│   │       ├── _center-block.scss
│   │       ├── _clearfix.scss
│   │       ├── _forms.scss
│   │       ├── _gradients.scss
│   │       ├── _grid-framework.scss
│   │       ├── _grid.scss
│   │       ├── _hide-text.scss
│   │       ├── _image.scss
│   │       ├── _labels.scss
│   │       ├── _list-group.scss
│   │       ├── _nav-divider.scss
│   │       ├── _nav-vertical-align.scss
│   │       ├── _opacity.scss
│   │       ├── _pagination.scss
│   │       ├── _panels.scss
│   │       ├── _progress-bar.scss
│   │       ├── _reset-filter.scss
│   │       ├── _resize.scss
│   │       ├── _responsive-visibility.scss
│   │       ├── _size.scss
│   │       ├── _tab-focus.scss
│   │       ├── _table-row.scss
│   │       ├── _text-emphasis.scss
│   │       ├── _text-overflow.scss
│   │       └── _vendor-prefixes.scss
│   └── font-awesome
│       ├── _bordered-pulled.scss
│       ├── _core.scss
│       ├── _fixed-width.scss
│       ├── _icons.scss
│       ├── _larger.scss
│       ├── _list.scss
│       ├── _mixins.scss
│       ├── _path.scss
│       ├── _rotated-flipped.scss
│       ├── _spinning.scss
│       ├── _stacked.scss
│       └── _variables.scss
├── css
│   └── style.scss
├── fonts
│   ├── FontAwesome.otf
│   ├── fontawesome-webfont.eot
│   ├── fontawesome-webfont.svg
│   ├── fontawesome-webfont.ttf
│   ├── fontawesome-webfont.woff
│   ├── glyphicons-halflings-regular.eot
│   ├── glyphicons-halflings-regular.svg
│   ├── glyphicons-halflings-regular.ttf
│   └── glyphicons-halflings-regular.woff
├── img
│   ├── loading.gif
│   └── url.png
└── js
    ├── bootstrap.min.js
    ├── jquery.min.js
    └── webcore.js
```
这里是本人博客的 SCSS：[assets/_sass](https://github.com/Ihavee/ihavee.github.io/tree/master/assets/_sass)

关于 Sass 的具体用法，可以查看：[SASS用法指南 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2012/06/sass.html)
