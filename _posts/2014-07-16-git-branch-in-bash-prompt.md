---
layout: post
title: "让 Bash Prompt 显示 Git Branch"
description: "配置 bash prompt 显示 git branch"
keywords: "bash, prompt, git, branch"
category: Linux
tags: [Git, CLI]
---

![Git Branch Name]({{ site.qiniudn }}/images/2014/07/git-branch.png)

看到他人的 terminal 中显示出 git branch 名，查阅了一些资料，需要 `__git_ps1`，下面记录下如何安装使用。

<!-- more -->
#### OS X

需要安装 Command Line Tools，在 10.9 的 OS X 中 Xcode 已经默认自带了 Command Line Tools。之前的版本需要 `xcode-select --install` 去安装。

随后，在 `~/.bash_profile` 中

```sh
if [ -f $(xcode-select -p)/usr/share/git-core/git-completion.bash ]; then
    . $(xcode-select -p)/usr/share/git-core/git-completion.bash
    . $(xcode-select -p)/usr/share/git-core/git-prompt.sh
fi
```

最后，`~/.bashrc` 中去组织你的 PS1，在适当的地方添加上 `$(__git_ps1)`，譬如我的

```sh
if [ `id -u` -ne 500 ]; then
    export GIT_PS_SHOWDIRTYSTATE=1
    export PS1='\[\e[1;36m\]→\[\e[m\] \[\e[0;32m\]\w\[\e[0;35m\]$(__git_ps1)\[\e[1;32m\] \$\[\e[m\] '
fi
```

顺便，每次登陆 terminal，都提示 "Last login: xxxxx"，又不是服务器，匿了它

    $ touch .hushlogin

#### Linux

首先当然是通过包管理器来安装 git，一些发行版打包 git 时可能会将一些文件精简掉，譬如俺的 [Gentoo](https://bugs.gentoo.org/show_bug.cgi?id=477920) 就是如此。

此时可以如此

```sh
$ curl -o ~/.git-prompt.sh \
    https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh
```

随后在 `~/.bash_profile` 中 source

```sh
[[ -f ~/.git-prompt.sh ]] && . ~/.git-prompt.sh
```

最后 `~/.bashrc` 中编辑你的 PS1，在其中某处添加 `$(__git_ps1)` 即可。

#### screen

在一些发行版中的 screen 下可能会提示 `-bash: __git_ps1: command not found`。

这是因为在 screen 下， `$(__git_ps1)` 需要 bash 运行，可以在 `~/.screenrc` 中配置如下一行  `shell -bash` 即可。
