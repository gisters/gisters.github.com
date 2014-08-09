---
layout: post
title: "Octopress 的 SEO 优化"
description: "Octopress 的 SEO 在默认情况下已经表现不错了，不过有个问题，`new_post` 不生成 meta description 和 keywords。"
keywords: octopress, seo, keywords, description
category: Internet
tags: [Octopress, SEO, Usage]
---

Octopress 的 SEO 在默认情况下已经表现不错了，不过有个问题，`new_post` 不生成 meta description 和 keywords。

SEO 的问题已经在这篇文章 [SEO for Octopress,Heroku](http://www.yatishmehta.in/seo-for-octopress) 被 Yatish Mehta 修复了。

octopress 默认已经提供了关键字和描述，问题在于 `new_post` 不生成，所以我们应该手动添加它。

### Every Page

每次添加一篇文章的时候都要添加 keywords & description，如下

<!-- more -->

```
---
layout: post
title: "Octopress SEO"
date: 2013-01-23 22:13
comments: true
categories: Internet
keywords: octopress, seo, keywords, description
description: Octopress 的 SEO 在默认情况下已经表现不错了，不过有个问题，`new_post` 不生成 meta description 和 keywords。
---
```

### Home Page

octopress 默认的在首页的描述是，最后一篇文章截取一定的长度作为首页的描述，这有点不符我们的习惯，我们可以这样来修改

#### _config.yml

编辑 `_config.yml`，添加 keywords 和 description 变量，如下

    keywords: linux, gentoo, httpd, browser, internet, media, sql, news, havanna
    description: Havanna's Hobbies, Linux Tips Record。

#### head.html

修改 `.themes/classic/source/_includes/head.html` 文件，在 author tag 后面，用如下替换 description/keywords 代码

{% raw %}
```html
<meta name="author" content="{{ site.author }}">
{% capture description %}{% if page.description %}{{ page.description }}{% elsif site.description %}{{ site.description }}{%else%}{{ content | raw_content }}{% endif %}{% endcapture %}
<meta name="description" content="{{ description | strip_html | condense_spaces | truncate:150 }}">
{% if page.keywords %}<meta name="keywords" content="{{ page.keywords }}">{%else%}<meta name="keywords" content="{{ site.keywords }}">{% endif %}
```
{% endraw %}

#### keywords & description

有个问题，在 `rake new_post[]` 或者 `rake new_page[]` 时，模板生成的 md 文件，没有 keywords & description

看了下 `Rakefile` 文件，很简单嘛：

```ruby
  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/&/,'&amp;')}\""
    post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M')}"
    post.puts "comments: true"
    post.puts "categories: "
    post.puts "keywords: "
    post.puts "description: "
    post.puts "---"
```

```ruby
    puts "Creating new page: #{file}"
    open(file, 'w') do |page|
      page.puts "---"
      page.puts "layout: page"
      page.puts "title: \"#{title}\""
      page.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M')}"
      page.puts "comments: true"
      page.puts "keywords: "
      page.puts "description: "
      page.puts "sharing: true"
      page.puts "footer: true"
      page.puts "---"
    end
```
