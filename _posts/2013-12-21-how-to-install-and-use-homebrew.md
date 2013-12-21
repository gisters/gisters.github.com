---
layout: post
title: "Homebrew 的安装与使用"
description: "与 MacPorts 类似，OS X 下还有款包管理工具为 Homebrew，安装方法也很简单。"
keywords: "brew, osx, homebrew, 方法"
category: "MAC"
tags: [Homebrew]
---
{% include JB/setup %}

与 MacPorts 类似，OS X 下还有款包管理工具为 [Homebrew](http://brew.sh)，安装方法也很简单。

    ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"

好了，安装完毕，Homebrew 的使用方法也很简单：

- brew search package   #搜索软件包
- brew install package  #安装软件包
- brew remove package   #移除软件包
- brew cleanup package  #清除旧包
- brew list             #列出已安装的软件包
- brew update           #更新 Homebrew
- brew upgrade          #升级软件包
- brew home package     #用浏览器打开
- brew info package     #显示软件内容信息
- brew deps package     #显示包的依赖
- brew server           #启动 web 服务器，可以通过浏览器访问 http://localhost:4567 来通过网页来管理包
- brew -h               #帮助

<!-- more -->
具体的用法可以 `man brew`。

这里主要说下，brew search package 时有时会出现

    Error: GitHub API rate limit exceeded for ip. See http://developer.github.com/v3/#rate-limiting for details. You may want to create an API token: http://github.com/settings/applications and then set HOMEBREW_GITHUB_API_TOKEN.

提示已经很明确了，按照说明照做，注册个 github 账号，打开页面 http://github.com/settings/applications. 点击 `Create new token`，填入 Token descrption 后，点击 Create Token

![Create new token](/assets/images/2013/12/create-new-token.png)

![Personal Access Tokens](/assets/images/2013/12/personal-access-tokens.png)

在家目录的 `~/.bash_profile` 中添加如下你申请到的 token

```bash
if [ -f /usr/local/bin/brew ]; then
    export HOMEBREW_GITHUB_API_TOKEN=xxxxxxxxxx
fi
```

最后再 `. ~/.bash_profile` 更新下你的环境变量。
