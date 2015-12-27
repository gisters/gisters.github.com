---
layout: post
title: "为 Jekyll 添加百度统计"
category: Internet
tags: [Jekyll, Baidu]
---

虽然现在众多统计平台都宣传因为采用异步传输技术，所以不会影响打开网站的速度。不过俺最后还是添加了百度的统计。一句话，查看 [Google Analytics](https://www.google.com/analytics/settings/) 时打开页面太慢了。

其实也非常的简单，修改 `_config.yml` 成如下

```yaml
# 网站统计
analytics:
    baidu:
        siteid: xxxxxxxxxxxxxxxx        # 百度统计 js 代码后的字符串
```

<!-- more -->

随后，创建 `_includes/custom` 文件夹，如有的话进入该目录后直接创建 `analytics` 文件

    cd _includes; touch baidu-analytics.html

编辑 `baidu-analytics.html` 文件，填充如下

{% raw %}
    {% if site.analytics.baidu.siteid %}
    <script>
      var _hmt = _hmt || [];
      (function() {
        var hm = document.createElement("script");
        hm.src = "//hm.baidu.com/hm.js?{{ site.analytics.baidu.siteid }}";
        var s = document.getElementsByTagName("script")[0];
        s.parentNode.insertBefore(hm, s);
      })();
    </script>
    {% endif %}
{% endraw %}

最后，添加一行至你的模版也的 `</body>` 之前

    {% include baidu-analytics.html %}

搞定收工。

如果你使用其他统计工具，方法类似。
