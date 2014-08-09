---
layout: post
title: "给 Jekyll 添加 FancyBox"
description: "这是一篇介绍无 jekyll 插件的形式来使用 FancyBox。"
keywords: "Jekyll, fancybox, jquery, plugins"
category: Internet 
tags: [Jekyll, FancyBox, jQuery]
---

这是一篇介绍无 jekyll 插件的形式来使用 **FancyBox**。

因为既然是博客是以 [**Jekyll**]({% post_url 2013-07-06-jekyll-install %}) 来驱动的，那么就尽量不要在本地生成静态页面再推送到服务端，也就是说，尽量不用插件。否则，大可以用 Octopress即可。在这个前提下如何给图片一些特殊的效果呢？这里我们使用 [**jQuery**](http://jquery.com/) 来实现， 譬如 **FancyBox**、[**Lightbox2**](http://lokeshdhakar.com/projects/lightbox2/)。

![FancyBox]({{ site.qiniudn }}/images/2013/10/fancybox.png "FancyBox")

<!-- more -->
其中有个问题，[**Markdown**]({% post_url 2013-07-02-markdown-syntax %}) 语法中的图片我们一般是如此写法

```
![tag](url "name")
```

生成的 html 为

```html
<image title="name" alt="name" src="url">
```

我们如果要使用（譬如）FancyBox 的话，则需要如下的链接才可

```html
<a href="url" id="id">
  <img src="url">
</a>
```

我们当然可以直接在 markdown 文本写 html，但是实在不想这么做（当时从 Wordpress 转到 markdown 时吃尽苦头），还是用 jquery 来替换的好。既然目的明确，那么动手

首先[**下载**](https://github.com/fancyapps/fancyBox/zipball/v2.1.5) fancybox，解压到你的主题文件夹，譬如我的是 `assets/themes/havee` 下，编辑模板，在 head 区域添加

{% raw %}
```html
<link href="{{ ASSET_PATH }}/fancybox/jquery.fancybox.css?v=2.1.5" rel="stylesheet" media="all" />
```
{% endraw %}

在 body 区域最下方添加

{% raw %}
```html
<script src="//libs.baidu.com/jquery/1.8.3/jquery.min.js"></script>
<script type="text/javascript" src="{{ ASSET_PATH }}/fancybox/jquery.fancybox.pack.js?v=2.1.5"></script>
<script>
// 给图片添加链接
$(document).ready(function() {
  $("p img").each(function() {
    var strA = "<a id='yourid' href='" + this.src + "'></a>";
    $(this).wrapAll(strA);
  });
});

// fancybox
$("#yourid").fancybox({
  openEffect	: 'elastic',
  closeEffect	: 'elastic',
});
</script>
```
{% endraw %}

如果你模板本身就引用 jquery，则不必再次引用

```html
<script src="//libs.baidu.com/jquery/1.8.3/jquery.min.js"></script>
```

FancyBox 具体用法：

- [http://fancyapps.com/fancybox](http://fancyapps.com/fancybox)

