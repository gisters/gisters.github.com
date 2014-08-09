---
layout: post
title: "Vim 状态栏"
description: "动手让vim的状态栏显示出编码格式"
keywords: vim, 状态栏, bom
category: Linux
tags: [Vim, Usage, Configuration]
---

![utf8-bomb]({{ site.qiniudn }}/images/2011/08/utf8-bomb.png "utf8-bomb")

玩无损音乐，其中接触到 `.cue` 的索引文件，由于 windows 与 linux 默认的文件编码差异，导致我本地的 `.cue` 全部转为 utf-8 编码，但是如此则共享给其他 PT fans 时，出现了问题。

<!-- more -->
foobar2000 认 cuesheet 的 `UTF-8+bom` 编码，so，通过 `:set bomb` 将本地所有 cuesheet 文件全部转为 `UTF-8+bom` 编码，写了个脚本解决。

下面又出现个问题，不清楚本地 `cuesheet` 文件的编码是 `UTF-8` 还是 `UTF-8+BOM` 呢。

so，动手让 vim 的状态栏显示出 `UTF-8+BOM` 编码格式。

`.vimrc` 中状态栏相关代码如下

```vim
if has("statusline")
set statusline=%F%m%r%h%w\ %=[FORMAT=%{&ff}]\ %{\"[\".(&fenc==\"\"?&enc:&fenc).((exists(\"+bomb\")\ &&\ &bomb)?\",B\":\"\").\"]\"}\ [TYPE=%Y]\ [POS=%l,%v][%p%%]\ %{strftime(\"%d/%m/%y\ -\ %H:%M\")}
endif
```
