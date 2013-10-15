---
layout: post
title: "Jekyll 中的语法高亮：Pygments"
description: "在 Jekyll 中的语法高亮：pygments"
keywords: "jekyll, pygments, 高亮"
category: Internet
tags: [Jekyll, pygments, python]
---
{% include JB/setup %}

[Jekyll](http://jekyllrb.com/) 原生支持语法高亮工具 [Pygments](http://pygments.org/) ，Pygments 支持[多种语言高亮](http://pygments.org/docs/lexers/)。

#### 安装

假设你已经能正常运行 Jekyll ([安装方法](/internet/2013-07/jekyll-install.html))。

下面安装pygments

    $ sudo pacman -S python2-pygments

<!-- more -->
安装pygments.rb

    $ gem install pygments

#### 配置

在 Jekyll 的配置文件 **_config.yml** 中设置打开 Pygments

    pygments: true
    mardown: redcarpet

进到我们的网站目录，运行

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

参考：

* [http://truongtx.me/2012/12/28/jekyll-bootstrap-syntax-highlighting/](http://truongtx.me/2012/12/28/jekyll-bootstrap-syntax-highlighting/)
