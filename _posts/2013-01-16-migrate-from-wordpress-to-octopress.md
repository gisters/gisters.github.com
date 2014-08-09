---
layout: post
title: "迁移 Wordpress 至 Octopress"
description: "迁移 Wordpress 至 Octopress"
keywords: octopress, jekyll, wordpress, 迁移
category: Internet
tags: [Wordpress, Octopress, Jekyll]
---

### 前言

不知道在想什么，忽然心血来潮想备案下 [Ihavanna.org](http://ihavanna.org) 的域名，于是就有了接下来的一些事。

由于备案期间，域名要求下线，且只能cname别名解析到指定的一个接入商的二级域名上。想想时间总不能浪费吧，想起之前 [WordPress](http://wordpress.org) 程序的强大且臃肿，一个想法总是缠绕心中，遂将 Wordpress 内容转到 [Octopress](http://octopress.org) 吧。

<!-- more -->

当初在 Octopress 与 Jeckyll 之间徘徊，不知该选择那个，想想反正两个的书写格式都是遵循 markdown，先上手 Octopress 吧，以后如再有机会的话，随时可以转到 jeckyll。Octopress 可以看作是加了一些 Plugins 的 jeckyll。

至于 Jekyll 的优势，不仅仅是 markdown 撰写方式，还有：

- git 管理版本
- 纯净的博客内容
- 方便的本地预览
- 源码、网页一条龙的保存在github上，cname别名一做即可
- 网页纯静态，迁移方便

### 备份

#### Post 备份

言归正传，首先就是 Wordpress 的备份，这里我们可以使用一个插件 [wordpress to jekyll exporter](https://github.com/benbalter/wordpress-to-jekyll-exporter) 来实现将 Wordpress 的 Post 转为 markdown 格式，插件的安装就不多说了，安装玩该插件在后台 Tools -- Export to Jekyll 执行导出，导出后是一个压缩包，打开一看，内容基本都转到 markdown 格式。

其中需要注意的是，在执行导出之前，wordpress 的 rewrite 规则需要设置下，类似与 `p?123` 的方式是不行的，最好类似于 `/2013-01/title-of-post.html`、`/2013/01/16/title-of-post.html` 这样的格式。

#### Comment 备份

也很简单，直接在 Wordpress 后台的插件中，搜索 [Disqus](http://disqus.com) 后安装即可，安装完毕后需要注册 Disqus 的账户，然后在 Wordpress 后台登录，导出 Wordpress 至 Disqus 中即可，不过时间较慢，需要24小时。

>Your import has been successfully uploaded and queued. We'll email you when your import has completed.
>Imports may take up to 24 hours to complete. If your import hasn't finished after 24 hours, check our system status. If you have any further questions, you can contact our support team.

### 安装

#### Octopress 的安装

Github 的注册及 git 的使用方法就不多说了，资料漫天都是。我们来说说 Octopress 的安装。其实也很简单：

```sh
git clone git://github.com/imathis/octopress.git octopress
cd octopress # If you use RVM, You'll be asked if you trust the .rvmrc file (say yes).
ruby --version  # Should report Ruby 1.9.3
gem install bundler
rbenv rehash    # If you use rbenv, rehash to be able to run the bundle command
bundle install
rake install
```

完工。其中 `rake install` == `rake install[classic]` == (`rake update_source[classic]` && `rake update_style[classic]`)

#### 部署到 github

```sh
rake setup_github_pages

rake generate
rake deploy
```

这是将 html 等静态文件 push 到你的项目 master 分支，但是别忘记将源码也 push 上去

```sh
git add .
git commit -m 'your message'
git push origin HEAD:source
```

#### 域名

最后

```sh
echo 'domain' >> source/CNAME
rake gen_deploy
```

再去域名控制面板，作下别名解析，`domain -> yourname.github.com`

等待几个小时后，你的域名基本就能访问了。

### 其他

好了，基本完工了。后面有时间再讲讲 Octopress 的一些使用技巧吧，我也在摸索中......
