---
layout: post
title: "Gentoo 修复 Firefox loading.png 动画"
category: Linux
tags: [Firefox]
---

由于上次的误操作，导致多年的 Gentoo 数据毁于一旦。辛辛苦苦到现在，终于有些恢复原样了，只是 Firefox 的tab 上的圈圈在载入网页时一动不动，Mozilla Firefox 社区一直没有解决方案，不过记得上次在哪里看到过解决方案的。

又是不停的切换关键字搜索，终于在 bugs.archLinux.org 上找到解决方案。

<!-- more -->

Archlinux 的 bug report 上的解决方案是在 userChrome.css 中替换个 loading.png 的图片，鄙人依样画葫芦从 OS X 下的 Firefox 中提取了 loading.png，base64 编码后放入 userChrome.css 中。

![tab loading icon](http://cdn.09hd.com/images/2014/06/firefox-tab-loading-icon.gif)

```css
/* fix tab loading icon */
@namespace url(http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul);
.tab-throbber[progress] {
  list-style-image: url("firefox-loading.png") !important;
}
```

修改好后，tab 上的 loading.png 终于开始转圈了。

顺便给出一个地址栏下拉的单行显示风格，一直觉得地址栏双行显示比较吃资源。

```css
/* the old urlbar stylish */
#PopupAutoCompleteRichResult {
  -moz-binding: url(chrome://browser/content/urlbarBindings.xml#browser-autocomplete-result-popup) !important;
}
```

参考：[https://bugs.archlinux.org/task/37576](https://bugs.archlinux.org/task/37576)
