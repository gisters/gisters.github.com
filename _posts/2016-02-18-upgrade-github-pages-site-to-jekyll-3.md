---
layout: post
title: "托管 Github Pages 的博客升级至 jekyll 3"
category: Internet
tags: [Jekyll]
---

昨天收到 Github 的邮件，提示 GitHub Pages 将于 5 月 1 号以后只支持 kramdown。

马上翻到官网看了下，在 Jekyll 3 中，一些功能被剥离，成为插件，这意味着 `--safe` 模式下，一些 Jekyll 2 下的功能不被支持，如下：

- jekyll-paginate
- jekyll-coffescript
- jekyll-gist
- pygments.rb
- redcarpet
- toml
- classifier-reborn

<!-- more -->

如果你托管于 Github Pages，可以在 5 月 1 日前后关注 <https://pages.github.com/versions/> 上的变化。

好吧，动手进行版本升级吧。

#### 1. Update

    $ gem update jekyll

注意，Jekyll 3 需要 Ruby >= 2.0.0

#### 2. Kramdown

首先要做的，就是修改 markdown 解析引擎至 [Kramdown](http://kramdown.gettalong.org/)

- 移除 **_config.yml** 中的 `markdown: xxx`
- 添加 kramdown 的 GFM ，来支持 fenced code blocks

```yaml
kramdown:
  input: GFM
```

#### 3. Syntax Highlighting

语法高亮将仅支持 [**Rouge**](http://rouge.jneen.net/)

- 移除 **_config.yml** 中的 `highlighter:xxx`

理论上，Rouge 100% 兼容 Pygments。

#####  a. Rouge

你可以使用 Rouge 重新为你的 Jekyll 生成样式：

    $ rougify style monokai > syntax.css

同时，Rouge 内置了多个样式，可以用如下命令列出

    $ rougify help style
    usage: rougify style [<theme-name>] [<options>]

    Print CSS styles for the given theme.  Extra options are
    passed to the theme.  Theme defaults to thankful_eyes.

    options:
      --scope(default: .highlight) a css selector to scope by

    available themes:
    base16, base16.dark, base16.monokai, base16.monokai.light, base16.solarized, base16.solarized.dark, colorful, github, molokai, monokai, monokai.sublime, thankful_eyes

如果你喜欢 Github 的默认样式，你可以这样生成

    $ rougify style github > syntax.css

#### 4. Permalink

##### a. Relative Permalink

Jekyll 3 以后的版本，默认情况下，Permalink 中将不再支持相对路径，可以通过如下开启

    relative_permalinks: true

##### b. Trailing slash

Jekyll 3 以后的版本，Permalink 不再自动添加 trailing slash

譬如 `permalink: /:year-:month-:day-:title`

- jekyll 2 中，生成 example.com/2016-02-18-text
- jekyll 3 中，生成 example.com/2016-02-18-test.html

所以，如果你想在 jekyll 3 中仍然目录形式，那么你需要修改成如此：`permalink: /:year-:month-:day-:title/`

#### 5. jekyll-paginate

这个应该是一个基本功能，是在想不通，为什么 Jekyll 默认剥离

- 修改 **_config.yml**，添加 `gems: [jekyll-paginate]`

如此 Github Pages 上就正常运行了，本地的话，还需要 `gem install jekyll-paginate`

参考：

- <https://github.com/blog/2100-github-pages-now-faster-and-simpler-with-jekyll-3-0>
- <http://jekyllrb.com/docs/upgrading/2-to-3/>
