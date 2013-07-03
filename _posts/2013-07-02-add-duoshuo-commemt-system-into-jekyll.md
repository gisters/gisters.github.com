---
layout: post
title: "为 Jekyll 添加多说评论系统"
description: "为 Jekyll 添加多说评论系统"
keywords: jekyll, 添加, 多说, 评论
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

其次进入 `_includes/` 目录创建目录 `custom` 以及在刚创建的 `custom` 目录下创建文件 `duoshuo`

    $ cd _includes; mkdir custom; cd custom ; touch duoshuo

填充如下内容

```html
<!-- Duoshuo Comment BEGIN -->
    <div id="comments" class="ds-thread"></div>
<!-- Duoshuo Comment END -->
```

由于同一页面调用多个多说系统的数据，其 js 只需加载一次即可。所以相关的 javascript 我们放到默认的 default 中，编辑 `_includes/themes/hooligan/default` 文件，在


```
    {% include JB/analytics %}
```

上面添加多说的 js 代码

{% raw %}
```
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
