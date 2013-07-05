---
layout: post
title: "Jekyll 模板数据"
description: "Jekyll模板数据的官方Wiki文档，希望对大家有帮助。"
keywords: "jekyll, 模板"
category: Internet
tags: [Jekyll, Usage]
---
{% include JB/setup %}

> 原文地址：[https://github.com/mojombo/jekyll/wiki/Template-Data](https://github.com/mojombo/jekyll/wiki/Template-Data)

Jekyll 会遍历你的站点，来寻找需要处理的文件。任何具有 [YAML前置数据](/internet/2013-07/jekyll-yaml-front-matter.html) 的文件都将会被处理，每一个这样的文件，**Jekyll** 都会通过 [Liquid模板系统](http://wiki.github.com/shopify/liquid/liquid-for-designers) 使用许多可用的页面变量。下面是一个可用变量的列表。

### 全局变量

<!-- more -->
<table class="table table-bordered">
  <thead>
    <tr>
      <th>变量</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>site</td>
      <td>全站的信息加 _config.yml 文件中的配置选项</td>
    </tr>
    <tr>
      <td>page</td>
      <td>这个变量中包含 YAML 前置数据,另外加上两个额外的变量值:url和content。</td>
    </tr>
    <tr>
      <td>content</td>
      <td>在布局模板文件中，这里变量包含了页面的子视图。这个变量将会把渲染后的内容插入到模板文件中。这个变量不能在文章和页面文件中使用。</td>
    </tr>
    <tr>
      <td>paginator</td>
      <td>一旦paginate配置选项被设置了，这个变量才能被使用。</td>
    </tr>
  </tbody>
</table>

### Site变量

<table class="table table-bordered">
  <thead>
    <tr>
      <th>变量</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>site.time</td>
      <td>当前的时间(当你运行 Jekyll 时的时间)</td>
    </tr>
    <tr>
      <td>site.posts</td>
      <td>一个按时间逆序的文章列表。</td>
    </tr>
    <tr>
      <td>site.related_posts</td>
      <td>如果当前被处理的页面是一个文章文件，那这个变量是一个包含了最多 10 篇相关文章的列表。默认来说，这些相关文章是低质量但计算快的。为了得到高质量但计算慢的结果，运行 Jekyll 命令时可以加上 --lsi 选项。(潜在语意索引)</td>
    </tr>
    <tr>
      <td>site.categories.CATEGORY</td>
      <td>所有在 CATEGORY 分类中的文章列表</td>
    </tr>
    <tr>
      <td>site.tags.TAG</td>
      <td>所有拥有 TAG 标签的文章的列表</td>
    </tr>
    <tr>
      <td>site.[CONFIGURATION_DATA</td>
      <td>截止 0.5.2 版本，所有在 _config.yml 中的数据都能够通过 site 变量调用。举例来说，如果你有一个这样的选项在你的配置文件中：url: http://mysite.com，那在文章和页面文件中可以这样调用{ { site.url } }。Jekyll 并不会自动解析修改过的 _config.yml 文件，你想要启用新的设置选项，你需要重启 Jekyll</td>
    </tr>
  </tbody>
</table>

### Page变量

<table class="table table-bordered">
  <thead>
    <tr>
      <th>变量</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>page.content</td>
      <td>页面中未渲染的内容</td>
    </tr>
    <tr>
      <td>page.title</td>
      <td>文章的标题</td>
    </tr>
    <tr>
      <td>page.url</td>
      <td>除去域名以外的URL，例子:/2008/12/14/my-post.html</td>
    </tr>
    <tr>
      <td>page.date</td>
      <td>指定每一篇文章的时间，这个选项能够覆盖一篇文章中前置数据设置的时间，它的格式是这样的:YYYY-MM-DD HH:MM:SS</td>
    </tr>
    <tr>
      <td>page.id</td>
      <td>每一篇文章的唯一标示符(在RSS中非常有用) 例子：/2008/12/14/my-post</td>
    </tr>
    <tr>
      <td>page.categories</td>
      <td>这篇文章隶属的分类的一个列表，分类是通过在_post目录中的目录结构推导而来的。举例来说，在路径/work/code/_posts/2008-12-24-closures.textile下的文件，这个变量将会是[work,code]。这个变量也能在YAML前置数据中被指定。</td>
    </tr>
    <tr>
      <td>page.tags</td>
      <td>这篇文章的标签的列表。这些数据能够在YAML前置数据中指定</td>
    </tr>
    <tr>
      <td>page.next</td>
      <td>按时间序的下一篇文章</td>
    </tr>
    <tr>
      <td>page.previous</td>
      <td>按时间序的上一篇文章</td>
    </tr>
    <tr>
      <td>page.path</td>
      <td>页面源码的地址，可以链接到托管服务器上的md文件地址</td>
    </tr>
  </tbody>
</table>

**注意**:任何你自己指定的自定义前置数据都能够通过`page`调用。举例来说，如果你在页面的前置数据中设置了`custom_css: true`，那这个值可以在模板可以这样调用:`page.custom_css`

### Paginator变量

**注意**:这个变量只能在索引文件中才能使用。

<table class="table table-bordered">
  <thead>
    <tr>
      <th>变量</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>paginator.per_page</td>
      <td>每一个页面上文章的数量</td>
    </tr>
    <tr>
      <td>paginator.posts</td>
      <td>当前页面上可用的文章</td>
    </tr>
    <tr>
      <td>paginator.total_posts</td>
      <td>所有文章的数量</td>
    </tr>
    <tr>
      <td>paginator.total_pages</td>
      <td>所有页面的数量</td>
    </tr>
    <tr>
      <td>paginator.page</td>
      <td>当前页面的数量</td>
    </tr>
    <tr>
      <td>paginator.previous_page</td>
      <td>前面的页面的数量</td>
    </tr>
    <tr>
      <td>paginator.next_page</td>
      <td>后面的页面的数量</td>
    </tr>
  </tbody>
</table>

转自：[【译文】Jekyll模板数据](http://zhouyichu.com/%E7%BF%BB%E8%AF%91/2012/12/05/Jekyll-Wiki-Template-Data.html)