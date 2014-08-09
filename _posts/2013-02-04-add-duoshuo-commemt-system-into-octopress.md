---
layout: post
title: "为 Octopress 添加多说评论系统"
description: "自带的 `disqus` 自定义选项很少，不能很好的融入到模板中，主要是加载速度实在是缓慢到无法忍受，于是找了个国内的 `duoshuo` 社会化评论插件来使用。"
keywords: "Octopress, 多说, 添加, 评论, disqus"
category: Internet
tags: [Octopress, duoshuo, Disqus]
---

自带的 `disqus` 自定义选项很少，不能很好的融入到模板中，主要是加载速度实在是缓慢到无法忍受，于是找了个国内的 `duoshuo` 社会化评论插件来使用。

### 添加评论

在 `_config.yml` 中添加

```yaml
# duoshuo comments
duoshuo_comments: true
duoshuo_short_name: yourname
```

在 `source/_layouts/post.html` 中的 `disqus`代码

<!-- more -->

{% raw %}
```html
{% if site.disqus_short_name and page.comments == true %}
  <section>
    <h1>Comments</h1>
    <div id="disqus_thread" aria-live="polite">{% include post/disqus_thread.html %}</div>
  </section>
{% endif %}
```
{% endraw %}

下方添加 `多说评论` 模块

{% raw %}
```html
{% if site.duoshuo_short_name and site.duoshuo_comments == true and page.comments == true %}
  <section>
    <h1>Comments</h1>
    <div id="comments" aria-live="polite">{% include post/duoshuo.html %}</div>
  </section>
{% endif %}
```
{% endraw %}

如果你希望一些单独的页面下方也放置评论功能，譬如 `rake new_page["xxx"]` 产生的页面也能评论，那么请在 `source/_layouts/page.html` 中也做如上的修改。

然后就按路径创建一个 `source/_includes/post/duoshuo.html`

{% raw %}
```html
<!-- Duoshuo Comment BEGIN -->
<div class="ds-thread" data-title="{% if site.titlecase %}{{ page.title | titlecase }}{% else %}{{ page.title }}{% endif %}"></div>
<script type="text/javascript">
  var duoshuoQuery = {short_name:"{{ site.duoshuo_short_name }}"};
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

随后，再修改 `_includes/article.html` 文件，在

{% raw %}
```html
        {% if site.disqus_short_name and page.comments != false and post.comments != false and site.disqus_show_comment_count == true %}
         | <a href="{% if index %}{{ root_url }}{{ post.url }}{% endif %}#disqus_thread">Comments</a>
        {% endif %}
```
{% endraw %}

下方添加如下代码

{% raw %}
```html
         {% if site.duoshuo_short_name and page.comments != false and post.comments != false and site.duoshuo_comments == true %}
          | <a href="{% if index %}{{ root_url }}{{ post.url }}{% endif %}#comments">Comments</a>
         {% endif %}
```
{% endraw %}

当做这些步骤前，要先去多说网注册个帐号，添加站点，获取站点 `short_name`。

### 首页侧边栏插入最新评论

首先在 `_config.yml` 中再插入如下代码

```yaml
duoshuo_asides_num: 10      # 侧边栏评论显示条目数
duoshuo_asides_avatars: 0   # 侧边栏评论是否显示头像
duoshuo_asides_time: 0      # 侧边栏评论是否显示时间
duoshuo_asides_title: 0     # 侧边栏评论是否显示标题
duoshuo_asides_admin: 0     # 侧边栏评论是否显示作者评论
duoshuo_asides_length: 18   # 侧边栏评论截取的长度
```

再创建 `_includes/custom/asides/recent_comments.html`

{% raw %}
```html
<section>
<h1>Recent Comments</h1>
<ul class="ds-recent-comments" data-num-items="{{ site.duoshuo_asides_num }}" data-show-avatars="{{ site.duoshuo_asides_avatars }}" data-show-time="{{ site.duoshuo_asides_time }}" data-show-title="{{ site.duoshuo_asides_title }}" data-show-admin="{{ site.duoshuo_asides_admin }}" data-excerpt-length="{{ site.duoshuo_asides_length }}"></ul>
{% if index %}
<!--多说js加载开始，一个页面只需要加载一次 -->
<script type="text/javascript">
  var duoshuoQuery = {short_name:"{{ site.duoshuo_short_name }}"};
  (function() {
    var ds = document.createElement('script');
    ds.type = 'text/javascript';ds.async = true;
    ds.src = 'http://static.duoshuo.com/embed.js';
    ds.charset = 'UTF-8';
    (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(ds);
  })();
</script>
<!--多说js加载结束，一个页面只需要加载一次 -->
{% endif %}
</section>
```
{% endraw %}

最后，`_config.yml` 中的 `blog_index_asides` 行或 `page_asides` 行或 `post_asides` 添加

    custom/asides/recent_comments.html

收工。

##### Update

多说评论似乎升级了系统，无法自动获取到页面文章标题，所以手动在评论页插入 **data-title**。--2013.09.10
