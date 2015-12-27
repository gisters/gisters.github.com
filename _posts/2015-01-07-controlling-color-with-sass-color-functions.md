---
layout: post
title: "Sass 色彩函数"
category: Internet
tags: [Sass]
---

最近迷上了 [Sass]({% post_url 2013-08-07-convert-scss-to-css %})，加上 Jekyll 2.4.0 开始已经内置 Sass 预处理器，于是将自己博客的样式彻底的换成 Sass，期间遇到颜色相关的部分，查阅资料后，纪录下来。

Sass 色彩相关的函数非常强大，当你定下页面基调色彩变量后，就可以大大的加快了开发速度，特别是对我这种半调子且没有任何色彩搭配概念的人来说，使用起来确实是轻松加愉快的。

废话不多说，下面开始，定下一个基本色，譬如咖啡色，[更多色彩值](//github.com/sass/sass/blob/stable/lib/sass/script/value/color.rb#L28-L180)

```scss
$base: chocolate;
```

<!-- more -->

这样定义由两个好处：

- 涉及色彩的部分，可以非常容易的修改
- 可以不用记住颜色值

#### Darken & Lighten

这两个是用来调整颜色的 HSL 值的亮度，顾名思义，Darken 变暗，Lighten 变亮。Sass 会自动的分析我们的颜色的 HSL 值来进行调整，用百分比来计算

```scss
darken($base, 10%);
lighten($base, 10%);
```

![Darken & lighten](//cdn.09hd.com/images/2015/01/darken-lighten.png)

#### Saturate & Desaturate

这两个将调整饱和度与去色，同样，Sass 会自动分析 HSL 值来进行调整，也需要百分比来表示

```scss
saturate($base, 20%);
desaturate($base, 20%);
```

![Saturate & Desaturate](//cdn.09hd.com/images/2015/01/saturate-desaturate.png)

#### Adjust-hue

这将调整色彩的色相，同样通过百分比计算

```scss
adjust-hue($base, 20%);
```

![adjust-hue](//cdn.09hd.com/images/2015/01/adjust-hue.png)

#### Other

HSL 相关的函数还有很多，譬如

```scss
hsl($hue, $saturation, $lightness);
hsla($hue, $saturation, $lightness, $alpha);
hue($color);
saturation($color);
lightness($color);
grayscale($color);
complement($color);
invert($color);
```

除此以外，Sass 提供了其他有趣的函数，譬如 rgb 函数：

```scss
rgb($red, $green, $blue);
rgba($red, $green, $blue, $alpha);
red($color);
green($color);
blue($color);
mix($color1, $color2, [$weight]);
```

了解更多：<http://sass-lang.com/documentation/Sass/Script/Functions.html>
