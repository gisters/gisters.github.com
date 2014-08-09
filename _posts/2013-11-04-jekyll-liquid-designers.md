---
layout: post
title: "Jekyll 扩展的 Liquid 设计"
description: "在 Liquid 中有两种类型的标记： Output 和 Tag。"
keywords: "liquid,jekyll"
category: "Internet"
tags: [Liquid,Jekyll]
---

> 原文地址：https://github.com/shopify/liquid/wiki/liquid-for-designers

在Liquid中有两种类型的标记： `Output` 和 `Tag`。

* `Output` 标记（有些可能解析文本）被包含在：

  {% raw %}
  ```
  {{ 两个配对的花括号中 }}
  ```
  {% endraw %}

* `Tag` 标记（不能解析文本）被包含在:

  {% raw %}
  ```
  {% 成对的花括号和百分号中 %}
  ```
  {% endraw %}

<!-- more -->
### Output

下面是关于输出标记的简单实例：

{% raw %}
```
Hello {{name}}
Hello {{user.name}}
Hello {{ 'tobi' }}
```
{% endraw %}

#### 高级输入：过滤器

输入标记带有过滤器，方法很简单。第一个参数总是过滤器左边值的输出。当下个过滤器运行时，刚刚所得到的过滤器返回值就会成为新的左边值。直到最后没有过滤器时，模板就会接受最后的结果字符串。

{% raw %}
```
Hello {{ 'tobi' | upcase }}
Hello tobi has {{ 'tobi' | size }} letters!
Hello {{ 'tobi' | capitalize }}
Hello {{ '1984-02-01' | date: "%Y" }}
```
{% endraw %}

输出结果是：

{% raw %}
```
Hello {{ 'tobi' | upcase }}
Hello tobi has {{ 'tobi' | size }} letters!
Hello {{ 'tobi' | capitalize }}
Hello {{ '1984-02-01' | date: "%Y" }}
```
{% endraw %}

#### 标准过滤器

{% raw %}
* `date` - 格式化日期
* `capitalize` - 将输入语句的首字母大写
* `downcase` - 将输入字符串转为小写
* `upcase` - 将输入字符串转为大写
* `first` - 得到传递数组的第一个元素
* `last` - 得到传递数组的最后一个元素
* `join` - 将数组中的元素连成一串，中间通过某些字符分隔
* `sort` - 对数组元素进行排序
* `map` - 从一个给定属性中映射/收集一个数组
* `size` - 返回一个数组或字符串的大小
* `escape` - 对一串字符串进行编码
* `escape_once` - 返回一个转义的html版本，而不影响现有的转义文本
* `strip_html` - 去除一串字符串中的所有html标签
* `strip_newlines` - 从字符串中去除所有换行符(\n) 
* `newline_to_br` - 将所有的换行符(\n)换成html的换行标记
* `replace` - 匹配每一处指定字符串，如 {{ 'foofoo' | replace:'foo','bar' }} #=> 'barbar'
* `replace_first` - 匹配第一处指定的字符串，如 {{ 'barbar' | replace_first:'bar','foo' }} #=> 'foobar'
* `remove` - 删除每一处匹配字符串，如 {{ 'foobarfoobar' | remove:'foo' }} #=> 'barbar'
* `remove_first` - 删除第一处匹配的字符串，如 {{ 'barbar' | remove_first:'bar' }} #=> 'bar'
* `truncate` - 将一串字符串截断为x个字符
* `truncatewords` - 将一串字符串截断为x个单词
* `prepend` - 在一串字符串前面加上指定字符串，如 {{ 'bar' | prepend:'foo' }} #=> 'foobar'
* `append` - 在一串字符串后面加上指定字符串，如 {{ 'foo' | append:'bar' }} #=> 'foobar'
* `minus` - 减，如 {{ 4 | minus:2 }} #=> 2
* `plus` - 加，如 {{ '1' | plus:'1' }} #=> '11', {{ 1 | plus:1 }} #=> 2
* `times` - 乘，如 {{ 5 | times:4 }} #=> 20
* `divided_by` - 除，如 {{ 10 | divided_by:2 }} #=> 5
* `split` - 将一串字符串根据匹配模式分割成数组，如 {{ "a~b" | split:~ }} #=> \['a','b'\]
* `modulo` - 余数，如 {{ 3 | modulo:2 }} #=> 1
{% endraw %}

### Tags

`Tags` 用于你的模板逻辑。新的标签很容易开发，因此我希望在发布这些代码后，大家可以为标准标签库增加更多的内容。

下列是当前已经支持的标签：

* **assign** - 将一些值赋给一个变量
* **capture** - 块标记，把一些文本捕捉到一个变量中
* **case** - 块标记，标准的 case 语句
* **comment** - 块标记，将一块文本作为注释
* **cycle** - Cycle 通常用于循环轮换值，如颜色或 DOM 类。
* **for** - 用于循环 For loop
* **if** - 标准的 if/else 块
* **include** - 包含其他的模板；对于区块化非常有效
* **raw** - 暂时性的禁用的标签的解析
* **unless** - if 语句的简版

#### 注释

注释是最简单的标签，它只是把内容包含起来。

{% raw %}
```
We made 1 million dollars {% comment %} in losses {% endcomment %} this year
```
{% endraw %}

#### Raw

Raw 暂时性的禁用的标签的解析。这在需要展示一些可能产生冲突的内容（如本页面，要展示 liquid 语句，就需要包含在 raw 标签间，否则会被解析）时非常有用。

```
{{ "{% raw "}}%}
{% raw %}In Handlebars, {{ this }} will be HTML-escaped, but {{{ that }}} will not.{% endraw %}
{{ "{% endraw "}}%}
```

#### If/Else

`if/else` 在其他编程语言里应该已经被熟知了。Liquid 使得你可以通过 `if` 或 `unless` ( `elsif` 和 `else` 为可选 ) 编写简单的表达式:

{% raw %}
```ruby
{% if user %}
    Hello {{ user.name }}
{% endif %}

{% if user.name == 'tobi' %}
    Hello tobi
{% elsif user.name == 'bob' %}
    Hello bob
{% endif %}

{% if user.name == 'tobi' or user.name == 'bob' %}
    Hello tobi or bob
{% endif %}

{% if user.name == 'bob' and user.age > 45 %}
    Hello old bob
{% endif %}

{% if user.name != 'tobi' %}
    Hello non-tobi
{% endif %}

# 同上
{% unless user.name == 'tobi' %}
    Hello non-tobi
{% endunless %}

# 检测是否用户有一张信用卡
{% if user.creditcard != null %}
   poor sob
{% endif %}

# 同上
{% if user.creditcard %}
   poor sob
{% endif %}

# Check for an empty array
{% if user.payments == empty %}
   you never paid !
{% endif %}

{% if user.age > 18 %}
   Login here
{% else %}
   Sorry, you are too young
{% endif %}

# array = 1,2,3
{% if array contains 2 %}
   array includes 2
{% endif %}

# string = 'hello world'
{% if string contains 'hello' %}
   string includes 'hello'
{% endif %}
```
{% endraw %}

#### Case语句

如果你需要更多的条件判断，你可以使用 `case` 语句:

{% raw %}
```ruby
{% case condition %}
    {% when 1 %}
        hit 1
    {% when 2 or 3 %}
        hit 2 or 3
    {% else %}
        ... else ...
{% endcase %}
```
{% endraw %}

Example:

{% raw %}
```ruby
{% case template %}
    {% when 'label' %}
        // {{ label.title }}
    {% when 'product' %}
        // {{ product.vendor | link_to_vendor }} / {{ product.title }}
    {% else %}
        // {{page_title}}
{% endcase %}
```
{% endraw %}

#### Cycle

我们常常需要在不同的颜色或类似的任务间轮流切换。Liquid 对于这样的操作有内置支持，通过使用 `cicle` 标签。

{% raw %}
```ruby
{% cycle 'one', 'two', 'three' %}
{% cycle 'one', 'two', 'three' %}
{% cycle 'one', 'two', 'three' %}
{% cycle 'one', 'two', 'three' %}

will result in

one
two
three
one
```
{% endraw %}

如果一组 `cycle` 没有命名，那默认情况下有用相同参数的会被认为是一个组。

如果你希望完全控制 `cycle` 组，你可以指定一个组名，这个组名甚至可以是一个变量。

{% raw %}
```ruby
{% cycle 'group 1': 'one', 'two', 'three' %}
{% cycle 'group 1': 'one', 'two', 'three' %}
{% cycle 'group 2': 'one', 'two', 'three' %}
{% cycle 'group 2': 'one', 'two', 'three' %}

will result in

one
two
one
two
```
{% endraw %}

#### 循环

Liquid 允许循环一个集合 :

{% raw %}
```ruby
{% for item in array %}
    {{ item }}
{% endfor %}
```
{% endraw %}

在每次循环期间，下列的帮助变量都可用于额外的展示需要:

```
forloop.length      # => length of the entire for loop
forloop.index       # => index of the current iteration
forloop.index0      # => index of the current iteration (zero based)
forloop.rindex      # => how many items are still left?
forloop.rindex0     # => how many items are still left? (zero based)
forloop.first       # => is this the first iteration?
forloop.last        # => is this the last iteration?
```

你可以使用一些属性来影响接受循环中的哪项。

`limit:int` 使你可以限制接受的循环项个数；`offset:int` 可以可以让你从循环集合的第 n 项开始.

{% raw %}
```ruby
# array = \[1,2,3,4,5,6\]
{% for item in array limit:2 offset:2 %}
    {{ item }}
{% endfor %}
# results in 3,4
```
{% endraw %}
	
反转循环

{% raw %}
```ruby
{% for item in collection reversed %}
    {{item}}
{% endfor %}
```
{% endraw %}

除了对一个已经存在的集合进行循环，你还可以定义一段范围区域内的数字进行循环。这段区域既可以通过文字也可以通过变量数定义得到:

{% raw %}
```ruby
# if item.quantity is 4...
{% for i in (1..item.quantity) %}
    {{ i }}
{% endfor %}
# results in 1,2,3,4
```
{% endraw %}
	
####Variable Assignment

你可以把数据存储在你自己定义的变量中，以便在输出或者其他标签中使用。创建一个变量的最简单方式是使用 assign 标签，其语法也是简单明了的：

{% raw %}
```ruby
{% assign name = 'freestyle' %}

{% for t in collections.tags %}
    {% if t == name %}
        <p>Freestyle!</p>
    {% endif %}
{% endfor %}
```
{% endraw %}

另一种常见用法是把 `true/false` 值赋给变量:

{% raw %}
```ruby
{% assign freestyle = false %}

{% for t in collections.tags %}
    {% if t == 'freestyle' %}
        {% assign freestyle = true %}
    {% endif %}
{% endfor %}

{% if freestyle %}
    <p>Freestyle!</p>
{% endif %}
```
{% endraw %}

如果你希望把一系列字符串连接为一个字符串，并将其存储到变量中，你可以使用 `capture` 标签。这个标签是一个块级标签，它会 `captures` 任何在其中渲染的元素，并把捕获的值赋给给定的变量，而不是把这些值渲染在页面中。

{% raw %}
```html
{% capture attribute_name %}{{ item.title | handleize }}-{{ i }}-color{% endcapture %}

<label for="{{ attribute_name }}">Color:</label>
<select name="attributes[{{ attribute_name }}]" id="{{ attribute_name }}">
  <option value="red">Red</option>
  <option value="green">Green</option>
  <option value="blue">Blue</option>
</select>
```
{% endraw %}


转自：http://yanshasha.com/2013/01/22/Liquid-for-Designers/
