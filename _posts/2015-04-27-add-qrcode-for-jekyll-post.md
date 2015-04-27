---
layout: post
title: "为 Jekyll 的每篇文章添加二维码"
category: Internet
tags: [Jekyll, QRCode]
---

朋友问起，可以给 Jekyll 的每篇文章添加二维码显示吗？

想了下，Jekyll 生成的博客是纯静态的网页，如果要为每篇文章添加二维码的话，似乎也只有 javascript，或干脆为 Jekyll 写一个 plugin 了。

找了下 jquery，已经有现成的 [qrcode](https://github.com/jeromeetienne/jquery-qrcode) 了，按照说明，一步一步做下去。

<!-- more -->
每个 Jekyll 的模板不太一样，所以做法也不太一样，主要的就是在模板的相关区域添加：

    <script src="{{ site.assets_path }}/js/jquery.qrcode.min.js"></script>

然后再需要生成二维码的页面模板中的相关部分，添加：

    <div id="code"></div>

    <script type="text/javascript">
      $("#code").qrcode({
        width: 150,
        height: 150,
        text: "http:{{ site.url }}{{ page.url }}"
      });
    </script>

完了，就那么简单。
