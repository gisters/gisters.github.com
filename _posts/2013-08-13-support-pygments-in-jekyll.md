---
layout: post
title: "Jekyll 中的语法高亮：Pygments"
description: "在 Jekyll 中的语法高亮：pygments"
keywords: "jekyll, pygments, 语法, 高亮"
category: Internet
tags: [Jekyll, Pygments, Python]
---

Jekyll 原生支持语法高亮工具 [Pygments](http://pygments.org/) ，Pygments 支持[多种语言高亮](http://pygments.org/docs/lexers/)。

#### 一. 安装

假设你已经能正常运行 Jekyll ([安装方法]({% post_url 2013-07-06-jekyll-install %}))。

##### 1. 安装 pygments

archlinux:

    $ sudo pacman -S python2-pygments

或直接通过 pip 来安装

    $ pip install pygments --user

<!-- more -->
##### 2. 安装 pygments.rb

    $ gem install pygments.rb

#### 二. 配置

在 Jekyll 的配置文件 **_config.yml** 中设置打开 Pygments

    pygments: true
    mardown: redcarpet

> 注意：新版本 Jekyll 中，`pygments: true` 替换为 `highlighter: pygments`。

进到我们的网站目录，运行下面代码生成 [Pygments 样式](#pygments)

    $ pygmentize -S default -f html > your/path/pygments.css

生成的样式文件加到我们的网页中

```html
<link rel="stylesheet" href="/your/path/pygments.css">
```

#### 三. 使用

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

#### 四. Pygments 样式

<span id=pygments>Pygments 样式</span> 默认提供了 monokai、manni、rrt、perldoc、borland、colorful、default 等等，多种的样式。

可以通过以下命令列出当前 Pygments 支持的样式：

```python
>>> from pygments.styles import STYLE_MAP
>>> STYLE_MAP.keys()
['monokai', 'manni', 'rrt', 'perldoc', 'borland', 'colorful', 'default', 'murphy', 'vs', 'trac', 'tango', 'fruity', 'autumn', 'bw', 'emacs', 'vim', 'pastie', 'friendly', 'native']
```

通过 `-S` 来选择，譬如要生成 monokai 的样式：

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
