---
layout: post
title: "Jekyll 中的语法高亮：Pygments"
description: "在 Jekyll 中的语法高亮：pygments"
keywords: "jekyll, pygments, 高亮"
category: Internet
tags: [Jekyll, Pygments, Python]
---
{% include JB/setup %}

[Jekyll](http://jekyllrb.com/) 原生支持语法高亮工具 [Pygments](http://pygments.org/) ，Pygments 支持[多种语言高亮](http://pygments.org/docs/lexers/)。

#### 安装

假设你已经能正常运行 Jekyll ([安装方法](/internet/2013-07/jekyll-install.html))。

下面安装pygments ( 如果你同样使用archlinux的话 )

    $ sudo pacman -S python2-pygments

<!-- more -->
安装pygments.rb

    $ gem install pygments.rb

#### 配置

在 Jekyll 的配置文件 **_config.yml** 中设置打开 Pygments

    pygments: true
    mardown: redcarpet

> 注意：新版本 Jekyll 中，`pygments: true` 替换为 `highlighter: pygments`。

进到我们的网站目录，运行下面代码生成 [pygments 样式](#pygments)

    $ pygmentize -S default -f html > your/path/pygments.css

生成的样式文件加到我们的网页中

```html
<link rel="stylesheet" href="/your/path/pygments.css">
```

#### 使用

语法高亮的代码片段要放在标签对 {% raw %}`{% highlight language %}`{% endraw %} 和 {% raw %}`{% endhighlight %}`{% endraw %} 之间，其中的 language 为[多种语言高亮](http://pygments.org/docs/lexers/)页面中的 **Short names**。

{% highlight text %}
{% raw %}
{% highlight c %}
/* hello world demo */
#include <stdio.h>
int main(int argc, char **argv)
{
    printf("Hello, World!\n");
    return 0;
}
{% endhighlight %}
{% endraw %}
{% endhighlight %}

也可以采用这样的写法

{% highlight text %}
{% raw %}
```c
/* hello world demo */
#include <stdio.h>
int main(int argc, char **argv)
{
    printf("Hello, World!\n");
    return 0;
}
```
{% endraw %}
{% endhighlight %}

生成的 html 高亮结果

```c
/* hello world demo */
#include <stdio.h>
int main(int argc, char **argv)
{
    printf("Hello, World!\n");
    return 0;
}
```

#### pygments 样式

<span id=pygments>pygments 样式</span> 默认提供了 monokai、manni、rrt、perldoc、borland、colorful、default、murphy、vs、trac、tango、fruity、autumn、bw、emacs、vim、pastie、friendly、native 等多重的样式。通过 `-S` 来选择，譬如要生成 monokai 的样式：

    $ pygmentize -S monokai -f html > your/path/pygments.css

下面是 pygments 个各样式 show：

- monokai

![monokai]({{ site.qiniudn }}/images/2013/08/pygments-monokai.png)

- manni

![manni]({{ site.qiniudn }}/images/2013/08/pygments-manni.png)

- rrt

![rrt]({{ site.qiniudn }}/images/2013/08/pygments-rrt.png)

- perldoc

![perldoc]({{ site.qiniudn }}/images/2013/08/pygments-perldoc.png)

- borland

![borland]({{ site.qiniudn }}/images/2013/08/pygments-borland.png)

- colorful

![colorful]({{ site.qiniudn }}/images/2013/08/pygments-colorful.png)

- default

![default]({{ site.qiniudn }}/images/2013/08/pygments-default.png)

- murphy

![murphy]({{ site.qiniudn }}/images/2013/08/pygments-murphy.png)

- vs

![vs]({{ site.qiniudn }}/images/2013/08/pygments-vs.png)

- trac

![trac]({{ site.qiniudn }}/images/2013/08/pygments-trac.png)

- tango

![tango]({{ site.qiniudn }}/images/2013/08/pygments-tango.png)

- fruity

![fruity]({{ site.qiniudn }}/images/2013/08/pygments-fruity.png)

- autumn

![autumn]({{ site.qiniudn }}/images/2013/08/pygments-autumn.png)

- bw

![bw]({{ site.qiniudn }}/images/2013/08/pygments-bw.png)

- emacs

![emacs]({{ site.qiniudn }}/images/2013/08/pygments-emacs.png)

- vim

![vim]({{ site.qiniudn }}/images/2013/08/pygments-vim.png)

- pastie

![pastie]({{ site.qiniudn }}/images/2013/08/pygments-pastie.png)

- friendly

![friendly]({{ site.qiniudn }}/images/2013/08/pygments-friendly.png)

- native

![native]({{ site.qiniudn }}/images/2013/08/pygments-native.png)

参考：

* [http://pygments.org/docs/cmdline/](http://pygments.org/docs/cmdline/)
