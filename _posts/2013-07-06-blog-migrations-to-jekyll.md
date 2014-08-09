---
layout: post
title: "迁移博客至 Jekyll"
description: "Jekyll迁移方法的官方Wiki文档，希望对大家有帮助。"
keywords: "blog, 博客, 迁移, jekyll"
category: Internet
tags: [Jekyll, Usage]
---

> 原文地址：[https://github.com/mojombo/jekyll/wiki/Blog-Migrations](https://github.com/mojombo/jekyll/wiki/Blog-Migrations)

这个页面中的大部分方法都需要拥有对原系统数据库的访问权限。每一种方法都是依据数据库中的文章条目来生成 `_posts` 目录下的 `.markdown` 文件。请查看生成页面，确保它已经被正确的转换了。同时，大多数的导入脚本并不会检查你的博客文章是公开的还是私人的，所以请仔细查看 **Jekyll** 为你生成的页面。

### 如何使用

这些 [迁移方法](https://github.com/mojombo/jekyll/tree/master/lib/jekyll/migrators) 都是 **Jekyll** 的 Gem 的一部分.

1. 添加一个名为_import的目录到你的项目中
2. 打开一个命令终端
3. `gem install sequel mysqlplus`
4. 分别独立地运行下面的命令。

<!-- more -->
### 从 WordPress 迁移

#### 使用 Jekyll 和 Mysql 服务的链接

```bash
$ ruby -rubygems -e 'require "jekyll/migrators/wordpress";Jekyll::WordPress.process("database","user","pass")'
```

如果你使用的是 Webfaction，必须设置一个 [SSH 通道](http://docs.webfaction.com/user-guide/databases.html?highlight=mysql#starting-an-ssh-tunnel-with-ssh)，确保主机名是 (127.0.0.1)，否则 MySQL 可能会阻止你基于本地主机的访问，127.0.0.1 也不会在授权系统中具有等效的效果：

```bash
$ ruby -rubygems -e 'require "jekyll/migrators/wordpress";Jekyll::WordPress.process("database","user","pass","127.0.0.1")'
```

#### 使用 Jekyll 和 Wordpress 导出文件(适用于 wordpress.com)

如果 hpricot 还没有被安装，请先运行 `gem install hpricot`。导出你的博客：https://YOUR-USER-NAME.wordpress.com/wp-admin/export.php，假设这个文件叫做 wordpress.xml:

```bash
$ ruby -rubygems -e 'require "jekyll/migrators/wordpress";jekyll::WordPress.process("wordpress.xml")'
```

#### 关于 WordPress 的更多内容和其他选择

虽然以上的办法通常是有效的，但是他们却不能完全导入存储在 **wordpress** 文章和页面中的元数据。如果你想保留更多的页面、标签、图片和自定义数据等，你可能会对以下的资源感兴趣：

- [Exitwp](https://github.com/thomasf/exitwp) 是一个用 Python 写成配置工具，专门用来将一个或多个 **wordpress** 博客转换成 Jekll | Markdown 格式，同时保留尽可能多的元数据，包括下载附件和页面。
- 这是一篇非常优秀的 [文章](http://vitobotta.com/how-to-migrate-from-wordpress-to-jekyll/)，一步一步地教你在保持大部分元数据的前提下迁移 **wordpress** 博客。
- [wpXml2Jekyll](https://github.com/theaob/wpXml2Jekyll) 是一个 windows 下的可执行工具，可以根据你 **wordpress** 博客导出的XML文件创建相应的 **Markdown** 文件。
- [WordPress to Jekyll Exporter](https://github.com/benbalter/wordpress-to-jekyll-exporter) 是一个 **wordpress** 的一键式插件，能够将所有文章、页面、分类信息、元数据和相关的设置信息转换成 **Jekyll** 能够使用的 **Markdown** 和 **YAML** 格式的文件。它使用的是 `the_content` 过滤器，导出的是用户看到的页面，而不是数据库中的数据。


### 从 Drupal 迁移

注意：文本是为 **Drupal6.1** 写的，如果需要请进行升级。

```bash
$ ruby -rubygems -e 'require "jekyll/migrators/drupal"; Jekyll::Drupal.process("database", "user", "pass")'
```

### Movable Type 迁移

```bash
$ ruby -rubygems -e 'require "jekyll/migrators/mt"; Jekyll::MT.process("database", "user", "pass")'
```


### 从 Typo 4+ 迁移

```bash
$ ruby -rubygems -e 'require "jekyll/migrators/typo"; Jekyll::Typo.process("database", "user", "pass")'
```

这些代码同样只在 **Typo4** 以上测试过。


### 从 TextPattern 迁移

```bash
$ ruby -rubygems -e 'require "jekyll/migrators/textpattern"; Jekyll::TextPattern.process("database_name", "username", "password", "hostname")'
```

在 _import 的上层目录下运行以上命令。例如，如果 _import 目录在 /path/source/_import，那就在 /path/source 中运行上面的命令。

### 从 Mephisto 迁移

```bash
$ ruby -rubygems -e 'require "jekyll/migrators/mephisto"; Jekyll::Mephisto.process("database", "user", "password")'
```

如果你的数据在系统表中，你可以这样做：

```bash
$ ruby -rubygems -e 'require "jekyll/migrators/mephisto"; Jekyll::Mephisto.postgres({:database => "database", :username=>"username", :password =>"password"})'
```



### 从博客平台迁移

请查看 [从博客平台迁移到jekyll](http://coolaj86.info/articles/migrate-from-blogger-to-jekyll.html)

[kennym](https://github.com/kennym) 制作了一个小迁移 [脚本](https://gist.github.com/1115810)，因为之前文章中方法对他来说无效。

[ngauthier](https://github.com/ngauthier) 制作了 [另一个转换器](https://gist.github.com/1506614) 用来导入评论，这是通过博客的归档来实现的而不是RSS。

[juniorz](https://github.com/juniorz)为[Octopress](http://octopress.org/) 制作了 [另一个转换器](https://gist.github.com/1564581)。这个工具和 [ngauthier](https://github.com/ngauthier) 的版本很像，但是它把文章和草稿分开了，导入了标签和永久链接。

[kcargile](https://github.com/kcargile) 制作了一个 Windows 命令行 [转换](https://github.com/kcargile/blogger2jekyll) 工具，它使用博客平台XML格式的归档文件和 XSLT 来生成文章、标签和永久链接。

### 从Posterous迁移

对于你原来的博客:

```bash
$ ruby -rubygems -e 'require "jekyll/migrators/posterous"; Jekyll::Posterous.process("my_email", "my_pass")'
```

对于其他博客:

```bash
$ ruby -rubygems -e 'require "jekyll/migrators/posterous"; Jekyll::Posterous.process("my_email", "my_pass", "blog_id")'
```

一个可选的迁移工具在 [这里](https://github.com/pepijndevos/jekyll/blob/patch-1/lib/jekyll/migrators/posterous.rb)，它可以保留永久链接地址，还会尝试导入图片。

### 从Tumblr

从 Tumblr 迁移同样需要 nokogiri:`gem install nokogiri`

```bash
$ ruby -rubygems -e 'require "jekyll/migrators/tumblr"; Jekyll::Tumblr.process("http://www.your_blog_url.com", true)'
```

[这里](https://github.com/stephenmcd/jekyll/blob/master/lib/jekyll/migrators/tumblr.rb) 是一个修改过的 Tumblr 迁移工具，它能将文章输出为 **Markdown** 文档，包括文章的标签。

它需要 json gem 和用 Python 写的 html2text:

```bash
$ gem install json
$ pip install html2text
```

一旦安装好之后，只要简单地使用格式化的参数:

```bash
$ ruby -rubygems -e 'require "jekyll/migrators/tumblr"; Jekyll::Tumblr.process("http://www.your_blog_url.com", format="md")'
```

转自：[【译文】Jekyll博客迁移](http://zhouyichu.com/%E7%BF%BB%E8%AF%91/Jekyll-Wiki-Blog-Migrations.html)
