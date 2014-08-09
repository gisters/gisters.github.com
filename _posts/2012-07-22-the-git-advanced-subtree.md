---
layout: post
title: "Git 进阶：Subtree"
description: "在 git 版本 1.7.9.4 中，引入了 git-subtree，基本是 用于替换 git-submodule。因为在 git-submodule 中，你只能引用别人的仓库，对于修改 git-submodule 下的子项目的代码非常麻烦，git-subtree 因此应运而生。git 官方也推荐尽量采用 git-subtree。"
keywords: "git, submodule"
category: Linux
tags: [Git, Usage]
---

在 git 版本 1.7.9.4 中，引入了 git-subtree，基本是 用于替换 git-submodule。因为在 git-submodule 中，你只能引用别人的仓库，对于修改 git-submodule 下的子项目的代码非常麻烦，git-subtree 因此应运而生。git 官方也推荐尽量采用 git-subtree。

相比于 git-submodule 的好处是显而易见的：

- 管理与更新便捷
- clone全新项目不在需要如 git-submodule 那样注册 (init) 与更新 (update) 了
- 不会再有 .gitmodule
- 再也不用删除 submodule 时的费劲
- 团队下，不需要另外 fork，然后 push 了

<!-- more -->
下面说下 git-subtree 的一些基本操作。

#### 1. clone 一个远程仓库 dotfiles 到你本地

    $ git clone git@github.com:Ihavee/dotfiles.git
    $ cd dotfiles

#### 2. 增加一个 subtree bash

    $ git remote add bash git@github.com:Ihavee/bash.git        # 可以理解为远程仓库的别名
    $ git subtree add pull -P home/.bash bash master --squash   # 拉取远程仓库 bash 到本地仓库的home/.bash 目录。

嗯，就是如此的简单。

#### 3. 修改 subtree bash 下代码然后提交到远程 bash 的 master分支

    ...... edit home/.bash/file......
    $ git commit -a -m 'update some'
    $ git subtree push -P home/.bash bash master
    $ git push origin master                                    # 顺便主项目也 push 了

#### 4. 远程的子项目有更新了，拉下来合并

    $ git subtree pull -P home/.bash bash master --squash

非常的简单，需要掌握基本的 pull 与 push 命令即可。当然还有一些其他的用法

    $ git subtree -h
    usage: git subtree add   --prefix=<prefix> <commit>
       or: git subtree add   --prefix=<prefix> <repository> <commit>
       or: git subtree merge --prefix=<prefix> <commit>
       or: git subtree pull  --prefix=<prefix> <repository> <refspec...>
       or: git subtree push  --prefix=<prefix> <repository> <refspec...>
       or: git subtree split --prefix=<prefix> <commit...>

在团队协作中，应用到 git-subtree 的，如果需要对 git-subtree 下子项目有修改需求的，请先 `git subtree pull` 吧

参考：[https://github.com/git/git/blob/master/contrib/subtree/git-subtree.txt](https://github.com/git/git/blob/master/contrib/subtree/git-subtree.txt)
