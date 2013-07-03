---
layout: post
title: "Ctrl_Alt_Bksp 无法退回 tty"
description: "Ctrl_Alt_Bksp 无法退回 tty"
keywords: "ctrl_alt_bksp, tty"
category: Linux
tags: [tty]
---
{% include JB/setup %}

两个方法：

#### 在 `/etc/X11/xorg.conf.d/10-keyboards.conf文件的Section “InputClass”` 中添加以下内容

    Option “XkbOptions” “terminate:ctrl_alt_bksp”

#### 在~ `/.xinitrc` 文件中加入以下内容（需要 `xorg-xset` 工具包）

    setxkbmap -option terminate:ctrl_alt_bksp

目前滚动发行的发行版通用

goodluck
