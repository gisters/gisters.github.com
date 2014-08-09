---
layout: post
title: "更新 Jekyll 的 sitemap.xml"
description: "Jekyll 默认的 sitemap.txt 功能有些羸弱，在我开启 _config.yml 中的 paginate 参数后，sitemap.txt 中多出了很多的 page2 page3 page4 page5，显然这些在sitemap中都是不必要的。"
keywords: "jekyll, sitemap"
category: Internet
tags: [Jekyll]
---

Jekyll 默认的 sitemap.txt 功能有些羸弱，在我开启 _config.yml 中的 paginate 参数后，sitemap.txt 中多出了很多的 page2 page3 page4 page5，显然这些在sitemap中都是不必要的。

既然目的明确，那么开始动手，新建个 sitemap.xml

    git rm sitemap.txt; touch sitemap.xml

填充以下内容

<!-- more -->

{% raw %}
```xml
---
# Remember to set production_url in your _config.yml file!
title : Sitemap
---

<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">

    {% for post in site.posts %}
    <url>
      <loc>{{ site.production_url }}{{ post.url }}</loc>
      <changefreq>daily</changefreq>
      <lastmod>{{ post.date | date_to_xmlschema }}</lastmod>
      <priority>1.0</priority>
    </url>
    {% endfor %}

    {% for page in site.pages %}
    {% if page.layout != nil %}
    {% if page.layout != 'feed' %}
    {% if page.group == 'navigation' %}
    <url>
      <loc>{{ site.production_url }}{{ page.url | remove: 'index.html' }}</loc>
      <changefreq>monthly</changefreq>
      <priority>0.80</priority>
    </url>
    {% endif %}
    {% endif %}
    {% endif %}
    {% endfor %}

    <url>
      <loc>{{ site.production_url }}</loc>
      <changefreq>daily</changefreq>
      <priority>0.6</priority>
    </url>
</urlset>
```
{% endraw %}

OK，完美

参考：

- [http://www.sitemaps.org/protocol.html](http://www.sitemaps.org/protocol.html)

