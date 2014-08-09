---
layout: post
title: "Vim 配色"
description: "默认的 Xterm 只有 8 色，而一些 Vim 的漂亮的 colorscheme 基本都要 256 色"
keywords: "vim, screen, xterm 配色, 主题"
category: Linux
tags: [Vim, Screen, Xterm]
---

**Gentoo** 下，默认的 **Xterm** 只有 8 色，而一些 **Vim** 的漂亮的 **colorscheme** 基本都要 256 色。之前通过在 `vimrc` 设置 `set t_Co=256` 来避免 colorscheme 的不堪入目。直到有一次在 `screen` 下 vim 编辑一些文件才发现，仅仅如此还不行，需要重新配置颜色相关。

首先确认你的终端

    $ echo $TERM; tput colors
    xterm
    8

<!-- more -->
我得到的结果是 xterm，8 色。进入 screen 下执行看看

    $ echo $TERM; tput colors
    screen-256color
    256

是 256 色，此时在该 screen 下操作 vim ，还是 8 色的。哪怕在 screen 状态下执行 `export XTERM=xterm-256color`，screen 下 vim 的color 还是 8 色的。

通过 google 得知，需要在进 screen 前 export 才可以，于是

    $ echo "export TERM=xterm-256color" >> ~/.bashrc

再在 .vimrc 中配置

```vim
" color
if $TERM =~ '^xterm' || $TERM =~ '^screen' || $TERM=~ '256color$'
    "set t_Co=256
    set background=dark
    let g:solarized_termcolors = 256
    colorscheme solarized
elseif has('gui_running')
    set background=light
    let g:solarized_termcolors = 256
    colorscheme solarized
elseif $TERM =~ 'cons25'
    colorscheme default
endif
```

于是，可以很好的在 xterm 下及 xterm screen 下使用同一 colorscheme，而在 tty 下，则使用 default colorscheme。

以下是我 **vim & gvim** 的一些截图：

![VIM]({{ site.qiniudn }}/images/2013/10/vim.png "VIM")

![GVIM]({{ site.qiniudn }}/images/2013/10/gvim.png "GVIM")

![VIM with NerdTree]({{ site.qiniudn }}/images/2013/10/vim-nerdtree.png "VIM with NerdTree")

![GVIM with NerdTree]({{ site.qiniudn }}/images/2013/10/gvim-nerdtree.png "GVIM with NerdTree")

你也可以直接 clone 我的配置[https://github.com/Ihavee/.vim](https://github.com/Ihavee/.vim)

