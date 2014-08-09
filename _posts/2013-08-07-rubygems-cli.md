---
layout: post
title: "RubyGems 常用命令"
description: "rubygems一些常用命令"
keywords: "ruby, gem, 命令"
category: Linux
tags: [Usage, Ruby, Gem]
---

**查看 ruby 版本**

    ruby -v

**查看 watir 版本**

    ruby -e ''require"watir"; puts Watir::IE::VERSION''

**查看 gem 版本**

    gem -v

<!-- more -->
**更新所有包**

    gem update

**更新 RubyGems 软件**

    gem update --system

**安装 rake，从本地或远程服务器**

    gem install rake

**#安装 rake，从远程服务器**

    gem install rake --remote

**指定安装版本**

    gem install watir -v/--version 1.6.2

**卸载 rake 包**

    gem uninstall rake

**列出本地以 d 打头的包**

    gem list d

**查找本地含有数字的包**

    gem query -n ''[0-9]'' --local

**从本地和远程服务器上查找含有 log 字符串的包**

    gem search log --both

**只从远程服务器上查找含有 log 字符串的包**

    gem search log --remoter

**只从远程服务器上查找含有 log 字符串的包**

    gem search -r log

**提醒式的帮助**

    gem help

**列出 install 命令**

    gem help install

**列出 gem 命令使用一些例子**

    gem help examples

**把 rake.gemspec 编译成rake.gem**

    gem build rake.gemspec

**检测 rake 是否有效**

    gem check -v pkg/rake-0.4.0.gem

**清除所有包旧版本，保留最新版本**

    gem cleanup

**显示 rake 包中所包含的文件**

    gem contents rake

**列出与 rails 相互依赖的包**

    gem dependency rails -v 0.10.1

**查看 gem 的环境**

    gem environment

