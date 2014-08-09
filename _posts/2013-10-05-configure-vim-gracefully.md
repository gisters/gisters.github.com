---
layout: post
title: "优雅的配置 Vim"
description: "如果你是个 Vim 爱好者，且如果你在各种不同的计算机，不同的操作系统 ( Windows, Linux,  Macos X 以及 BSD 等 )下"
keywords: "vim, 配置"
category: Linux
tags: [Vim]
---

如果你是个 [Vim](http://www.vim.org/) 爱好者，且如果你在各种不同的计算机，不同的操作系统 ( Windows, Linux,  Macos X 以及 BSD 等 )下，不同的 **Vim 版本** 下，以及不同的 Vim 打包下（最小安装或自定义安装），你的 vimrc 可能无法通用，启动 vim 时出现很多错误。当然你也可以保存一个最精简的 vimrc，阉割掉很多实用的功能来忽略这些可能出现的错误。

不过实际上完全不必如此，一个兼容性良好的 **vimrc** 即可，下面几种方法可以有效的解决这些问题。

#### 功能：

可能这是最好的方式了，在 vimrc 中采用 `has()` 函数来检查这些功能的存在，并尝试相关的任何配置，譬如

```vim
if has("spell")
    set spelllang=en_us
    nnoremap <leader>s :set spell!<CR>
endif
```
<!-- more -->
这里设置 spelllang 存在的情况下，配置选项，并定义快捷键。

在不同的 **Linux** 发行版下，Vim 被编译成默认包含不同功能的版本，不过你可以通过 `vim --version` 来列出你系统上 Vim 的功能列表清单，看看 Vim 包含哪些库，譬如：

```
^_^ ~ $ vim --version
VIM - Vi IMproved 7.4 (2013 Aug 10, compiled Sep 28 2013 07:48:12)
Included patches: 1-41
Modified by Gentoo-7.4.41
Compiled by danny@Havee
Huge version without GUI.  Features included (+) or not (-):
+acl             +farsi           +mouse_netterm   +syntax
+arabic          +file_in_path    +mouse_sgr       +tag_binary
+autocmd         +find_in_path    -mouse_sysmouse  +tag_old_static
-balloon_eval    +float           +mouse_urxvt     -tag_any_white
......
   system vimrc file: "/etc/vim/vimrc"
     user vimrc file: "$HOME/.vimrc"
 2nd user vimrc file: "~/.vim/vimrc"
      user exrc file: "$HOME/.exrc"
  fall-back for $VIM: "/usr/share/vim"
Compilation: x86_64-pc-linux-gnu-gcc -c -I. -Iproto -DHAVE_CONFIG_H     -march=corei7 -O2 -pipe -U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=1      
Linking: x86_64-pc-linux-gnu-gcc   -Wl,-O1 -L/usr/local/lib -Wl,--as-needed -o vim    -lSM -lICE -lXpm -lXt -lX11 -lXdmcp -lSM -lICE  -lm -lncurses -lelf -lnsl  -lacl -lattr -ldl
```

列表中列出了很多不同的功能，譬如 `+statusline`，可能其他的发行版或其他的计算机默认没有包含次功能，那么可以如此设置：

```vim
if has("statusline")
  hi Statusline cterm=bold ctermfg=yellow ctermbg=blue
  set statusline=%F%m%r%h%w\ %=[FORMAT=%{&ff}]\ %{\"[\".(&fenc==\"\"?&enc:&fenc).((exists(\"+bomb\")\ &&\ &bomb)?\"    ,B\":\"\").\"]\"}\ [TYPE=%Y]\ [POS=%l,%v][%p%%]
  set laststatus=2
endif
```

如果你经常在 **Windows & Linux** 下使用 Vim，那么可以如此设置：

```vim
if has("unix")
    set some
else
    set other
endif
```

#### 检查

你可以检查一些功能是否存在其本身的一些选项，譬如

```vim
if exists("&foldenable")
    set foldenable
endif
```

一些功能可能只能存在高版本的 Vim 中

```vim
if v:version >= 700
    set foldenable
endif
```

不同的终端

```vim
if &term == "xterm"
  " Do stuff for xterm
elseif &term == "vt100"
  " Do stuff for a vt100 terminal
else
  " Do something for other terminals
endif
```

#### Silent calls

如果你实在无法确定是否包含的功能与版本，则可以通过 `silent!` 来避免一旦没有包含相关功能选项时的出错。

```vim
silent! call pathogen#infect()
silent! colorscheme pablo
```

#### Try/Catch/If

你也可以使用 `try/catch/endtry` 来配置一些备用的选项，譬如

```vim
try
  colorscheme pablo
catch
  colorscheme default
endtry
```

更详细参考： [http://vimdoc.sourceforge.net/htmldoc/](http://vimdoc.sourceforge.net/htmldoc/)
