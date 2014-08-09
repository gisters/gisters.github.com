---
layout: post
title: "Markdown 之使用表格"
description: "要在 markdown 中使用表格，需要 redcarpet 语法解释引擎"
keywords: "markdown, 表格, table, redcarpet, jekyll"
category: Internet
tags: [Markdown, Jekyll, Redcarpet]
---

看到人家在 [Markdown]({% post_url 2013-07-02-markdown-syntax %}) 文件中随意的书写表格，但在我自己的博客中却怎么也无法输出表格样式，最后还是通过 Google 才找到答案，需要语法解释引擎 [Redcarpet](https://github.com/vmg/redcarpet)，且开启 **tables** 选项。

![e.g. tables]({{ site.qiniudn }}/images/2013/10/table.png)

在 [Jekyll]({% post_url 2013-07-06-jekyll-install %}) 中使用，请修改 `_config.yml`

```yaml
markdown: redcarpet
redcarpet:
    extensions: ["tables"]
```

<!-- more -->
随后

    $ gem install redcarpet

在 Markdwon 文件中可以依据以下语法进行书写

```
|head1 head1 head1|head2 head2 head2|head3 head3 head3|head4 head4 head4|
|---|:---|:---:|---:|
|row1text1|row1text3|row1text3|row1text4|
|row2text1|row2text3|row2text3|row2text4|
```

其中 `:`所在位置表示表格的位置对齐

其最后输出的代码是

```html
<table>
  <thead>
    <tr>
      <th>head1 head1 head1</th>
      <th align="left">head2 head2 head2</th>
      <th align="center">head3 head3 head3</th>
      <th align="right">head4 head4 head4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>row1text1</td>
      <td align="left">row1text3</td>
      <td align="center">row1text3</td>
      <td align="right">row1text4</td>
    </tr>
    <tr>
      <td>row2text1</td>
      <td align="left">row2text3</td>
      <td align="center">row2text3</td>
      <td align="right">row2text4</td>
    </tr>
  </tbody>
</table>
```

鄙人博客中添加 `table thead tobody th tr td` 样式后显示的效果是

|head1 head1 head1|head2 head2 head2|head3 head3 head3|head4 head4 head4|
|---|:---|:---:|---:|
|row1text1|row1text3|row1text3|row1text4|
|row2text1|row2text3|row2text3|row2text4|

redcarpet有很多选项可以开启，譬如我就开启了

```yaml
markdown: redcarpet
redcarpet: 
    extensions: ["fenced_code_blocks", "tables", "highlight", "with_toc_data", "strikethrough", "underline"]
```

参考：

- [https://github.com/vmg/redcarpet][redcarpet]

[redcarpet]: https://github.com/vmg/redcarpet
