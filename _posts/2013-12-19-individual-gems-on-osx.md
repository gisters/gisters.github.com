---
layout: post
title: "OS X 的 rubygems 用户目录"
description: "新订了本 macbook pro，一切的东东都陆续搬到 OS X 系统中，xcode 安装完后，兴匆匆的准备 `gem install jekyll`，果然出了问题了。"
keywords: "rubygems, OS X"
category: "MAC"
tags: [Ruby, Gem]
---

新订了本 macbook pro，一切的东东都陆续搬到 OS X 系统中，xcode 安装完后，兴匆匆的准备 `gem install jekyll`，果然出了问题了。

    $ gem install jekyll
    Fetching: liquid-2.5.4.gem (100%)
    ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /Library/Ruby/Gems/2.0.0 directory.

<!-- more -->
嗯，我知晓只要 `sudo` 安装即可解决问题，可 Gentoo 下养成的洁癖不是说改就能改的啊，好吧，方法：

在 `~/.gemrc` 文件中添加如下

    all: --user-install

然后在 `~/.profile` 中添加如下

    # gem home
    if [ -f /usr/bin/gem ]; then
        export Gem_home=$HOME/.gem
        export PATH=$GEM_HOME/bin:$PATH
    fi

