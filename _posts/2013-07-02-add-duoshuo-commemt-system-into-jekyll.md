---
layout: post
title: "为 Jekyll 添加多说评论系统"
description: "为 Jekyll 添加多说评论系统"
category: Internet
tags: [Jekyll, duoshuo]
---
{% include JB/setup %}

在做以下步骤之前，先去 [duoshuo.com](http://duoshuo.com) 上注册一个帐号，获取 `short_name`

首先按照如下格式编辑 `_config.yml`

```yaml
  comments :
    provider : duoshuo
    duoshuo : 
      short_name : havanna
```

<!-- more -->

其次进入 `_includes/JB/comments-providers/` 目录创建文件 `duoshuo`

    $ cd _includes/JB/comments-providers && touch duoshuo

填充如下内容

{% raw %}
```html
<!-- Duoshuo Comment BEGIN -->
	<div class="ds-thread"></div>
<script type="text/javascript">
var duoshuoQuery = {short_name:'{{ site.JB.comments.duoshuo.short_name }}'};
	(function() {
		var ds = document.createElement('script');
		ds.type = 'text/javascript';ds.async = true;
		ds.src = 'http://static.duoshuo.com/embed.js';
		ds.charset = 'UTF-8';
		(document.getElementsByTagName('head')[0] 
		|| document.getElementsByTagName('body')[0]).appendChild(ds);
	})();
	</script>
<!-- Duoshuo Comment END -->
```
{% endraw %}

最后，编辑 `_includes/JB/comments` 文件

在

{% raw %}
```
{% if site.JB.comments.provider and page.comments != false %}

{% case site.JB.comments.provider %}
```
{% endraw %}

下添加

{% raw %}
```
{% when "duoshuo" %}
  {% include JB/comments-providers/duoshuo %}
```
{% endraw %}

OK，完工。