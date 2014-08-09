---
layout: post
title: "为 Jekyll 添加多说评论系统"
description: "为 Jekyll 添加多说评论系统"
keywords: jekyll, 添加, 多说, 评论, duoshuo
category: Internet
tags: [Jekyll, duoshuo]
---

在做以下步骤之前，先去 [duoshuo.com](http://duoshuo.com) 上注册一个帐号，获取 `short_name`

首先按照如下格式编辑 `_config.yml`

```yaml
  comments :
    provider : duoshuo
    duoshuo : 
      short_name : havee
```

<!-- more -->

其次进入 `_includes/` 目录创建目录 `custom` 以及在刚创建的 `custom` 目录下创建文件 `duoshuo`

    $ cd _includes; mkdir custom; cd custom ; touch duoshuo

填充如下内容

{% raw %}
```html
<!-- Duoshuo Comment BEGIN -->
    <div id="comments">
        <div class="ds-thread" {% if page.id %}data-thread-key="{{ page.id }}"{% endif %}  data-title="{% if page.title %}{{ page.title }} - {% endif %}{{ site.title }}"></div>
    </div>
<!-- Duoshuo Comment END -->
```
{% endraw %}

由于同一页面调用多个多说系统的数据，其 js 只需加载一次即可。所以相关的 javascript 我们放到默认的 default 中，编辑 `_includes/themes/havee/default.html` 文件，在

{% raw %}
```
    {% include JB/analytics %}
```
{% endraw %}

上面添加多说的 js 代码

{% raw %}
```html
    <!--多说js加载开始，一个页面只需要加载一次 -->
    <script type="text/javascript">
      var duoshuoQuery = {short_name:"{{ site.JB.comments.duoshuo.name }}"};
      (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = 'http://static.duoshuo.com/embed.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(ds);
      })();
    </script>
    <!--多说js加载结束，一个页面只需要加载一次 -->
```
{% endraw %}

OK，完成手工。

- - -

如果想侧边栏添加最新的评论呢？

由于默认所有页面都加载了多说的相关 js，所以现在只需在相关模板位置添加如下代码

```html
      <section>
        <h3>Latest Comments</h3>
        <ul class="ds-recent-comments" data-num-items="10" data-show-avatars="0" data-show-time="0" data-show-title="0" data-show-admin="0" data-excerpt-length="18"></ul>
      </section>

```

最新的访客呢？

```html
      <section style="width:250px;">
        <h3>Recently Visitors</h3>
          <ul class="ds-recent-visitors" data-num-items="4" data-avatar-size="45" style="margin-top:10px;"></ul>
      </section>
```

##### Update:

- 多说评论似乎升级了系统，无法自动获取到页面文章标题，所以手动在评论页插入 **data-title**。--2013.09.10
- 首页调用多说评论数的话，需要插入 **data-thread-key**。--2014.04.01
