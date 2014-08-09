---
layout: post
title: "为 Jekyll 添加百度统计"
description: "为 Jekyll 添加百度统计"
keywords: "jekyll, baidu, analytics"
category: Internet
tags: [Jekyll, Baidu]
---

虽然现在众多统计平台都宣传因为采用异步传输技术，所以不会影响打开网站的速度。不过俺最后还是添加了百度的统计。一句话，查看 [Google Analytics](https://www.google.com/analytics/settings/) 时打开页面太慢了。

其实也非常的简单，修改 `_config.yml` 成如下

```yaml
  analytics :
    provider : custom
```

<!-- more -->

随后，创建 `_includes/custom` 文件夹，如有的话进入该目录后直接创建 `analytics` 文件

    cd _includes; mkdir custom; cd custom; touch analytics

编辑 `analytics` 文件，填充你在百度站长平台获取到的统计代码，譬如

```html
<script>
var _hmt = _hmt || [];
(function() {
  var hm = document.createElement("script");
  hm.src = "//hm.baidu.com/hm.js?1fedf5827cc1b56fe29a9ce806f5114a";
  var s = document.getElementsByTagName("script")[0]; 
  s.parentNode.insertBefore(hm, s);
})();
</script>
```

搞定收工。

- - -
为什么一定要 `custom` 呢，因为尽量不要动 `_includes/JB/` 文件夹下的文件吧，以后升级方便....
