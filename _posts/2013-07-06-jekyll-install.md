---
layout: post
title: "Jekyll 的安装"
description: "Jekyll 安装方法的官方说明，学习的使用 Jekyll 的起点。这篇文章译自 Jekyll 的官方 Wiki，希望能对不熟悉英文的朋友们有所帮助。"
keywords: "jekyll, 安装, 方法"
category: Internet
tags: [Jekyll, Installation]
---

> 原文地址：[https://github.com/mojombo/jekyll/wiki/Install](https://github.com/mojombo/jekyll/wiki/Install "Install")

### 安装

Jekyll 最好的安装方法就是通过 RubyGems 来安装:

```bash
gem install jekyll
```

<!-- more -->
Jekyll 需要以下的 gems: `directory_watcher`,`liquid`,`open4`,`maruku`和`classifier`。这些组件将会在 gem 的安装命令之后自动安装。

如果你在 gem 的安装过程中遇到错误，你可能需要安装 ruby1.9.1 的编译扩展组件的头文件。如果是 Debian 系统，你可以这样做：

```bash
sudo apt-get install ruby1.9.1-dev
```

如果是 Red Hat、CentOS 或 Fedora 系统，你可以这样做：

```bash
sudo yum install ruby-devel
```

在 [NearlyFreeSpeech](https://www.nearlyfreespeech.net/ "NearlyFreeSpeech") 上，你需要：

```bash
RB_USER_INSTALL=true gem install jekyll
```

如果你在 Windows 操作系统上遇到像 `Faild to build gem native extension` 这样的错误，你可能需要安装 [RubyInstaller DevKit](https://github.com/oneclick/rubyinstaller/wiki/development-kit "RubyInstaller DevKit")

在 OSX 上，你可能需要升级 RubyGems:

```bash
sudo gem update --system 
```

如果你见到 `missing headers` 这样的错误，你可能还需要为 Xcode 安装命令行工具，你可以从 [这里](https://developer.apple.com/downloads/index.action) 下载。


### 从 LaTex 到 PNG

Maruku 本身对从 LaTex 到 PNG 的转换是可选的，它是通过 blahtex(Version 0.6) 完成的。但是，blahtex 必须和 `dvips` 一起放在你的 `$PATH` 中。

（**注意**：你需要自己设置 dvips 的位置，因为 [remi's fork of Maruku](http://github.com/remi/maruku/tree/master) 不会固定它的位置）



### RDiscount

如果你希望使用 [RDiscount](http://github.com/rtomayko/rdiscount/tree/master) 来渲染 markdown，而不是 [Maruku](http://maruku.rubyforge.org/),只要确保 RDiscount 被正确地安装：

```bash
sudo gem install rdiscount
```

然后运行 Jekyll，并使用以下的参数选项：

```bash
jekyll --rdiscount
```

你可以在你的 `_config.yml` 中写入如下代码，从而不必指定标志

```bash
markdown: rdiscount
```


### Pygments

如果你希望在你的文章中通过 `highlight` 标签实现代码高亮，你需要安装 [Pygments]({% post_url 2013-08-13-support-pygments-in-jekyll %})。

**在 OS X Leopard 和 Snow Leopard上 :**

它和 Python2.6 已经预装了：

```bash
sudo easy_install Pygments
```

或者在 OS X 中使用 MacPorts:

```bash
sudo port install python25 py25-pygments
```

或者在 OS X中 使用 HomeBrew

```bash
brew install python
#export PATH="/usr/local/share/python:$(PATH)"
easy_install pip
pip install --upgrade distribute
pip install pygments
```

**注意**：Homebrew 并不会为你自动链接到可执行文件。对于 Homebrew 默认的 Cellar 路径和 Python2.7 来说，确保把 `/usr/local/share/python` 添加到你的 `PATH` 中。想要了解更多信息，请查看 [这里](https://github.com/mxcl/homebrew/wiki/Homebrew-and-Python)

**在 Archlinux 上：**

```bash
sudo pacman -S python-pygments
```

或者使用 python2 版的 pygments:

```bash
sudo pacman -S python2-pygments
```

**注意**:python2 版本的 pygments 创建一个名为 `pygmentize2` 的可执行文件，而 Jekyll 会尝试寻找 `pygmentize`。创建一个执行链接 `# ln -s /usr/bin/pygments2 /usr/bin/pygmentize` 或者使用 python3 版的 pygments 都是可以的。(这条建议似乎已经过时了，因为 python2 版的 pygments 现在确实安装 pygmentize)

**在 Unbutu 和 Debian 上:**

```bash
sudo pat-get install python-pygments
```

**在 Fedora 和 CentOS 上:**

```bash
sudo yum install python-pygments
```

**在 Gentoo 上:**

```bash
sudo emerge -av dev-python/pygments
```

转自：[【译文】Jekyll的安装](http://zhouyichu.com/%E7%BF%BB%E8%AF%91/Jekyll-Wiki-Install.html)
