---
layout: post
title: "Git 进阶：Submodule"
description: "Git Submodule 对于大型团队项目来说，是个很好的协作工具"
keywords: "git, submodule, 方法"
category: Linux
tags: [Git, Usage]
---

>Git has something called submodule support. This allows you to specify one or more other git repositories within another – a bit like svn:externals (except trickier, but more powerful of course :).

**Git Submodule** 对于大型团队项目来说，是个很好的团队协作工具，它允许你的项目模块化成为每一个 Repository，最终汇聚成一个完整的项目。换句话说，**Git Submodule** 可以别人的 Repo 挂到你自己的 Repo 中的任何位置，成为的 Repo 的一部分。

下面说说一些基本的操作

#### 1. 增加一个 Submodule 到你的项目

```sh
$ git submodule add git@domain.com:another_project.git another_project
```

<!-- more -->
该命令会在你的项目 Repository 下产生一个 .gitmodules 文件，来记录你的 Submodule 信息，同时 another_project项目也clone下来，此时观察你的 Repo

```sh
$ git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD ..." to unstage)
#
#       new file:   .gitmodules
#       new file:   another_project
```

你可能会注意到，git 只记录了 submodule 目录，而没有记录目录下的文件。实际上，git 是按照 commit id 来比对 submodule 变动的

```sh
$ git add .gitmodules another_project
$ git commit -m "Add another_project submodule"
$ git submodule init
```

#### 2. 更新你项目中的 Submodule

```sh
$ cd another_project
$ git pull origin master
$ cd your_project
$ git add another_project
$ git commit another_project -m "Updated another_project Submodule to latest HEAD"
$ git push
```

更新 repo 下所有的 submodules

```sh
$ git submodule foreach git pull
```

#### 3. 修改你项目中的 Submodule

如果你恰巧拥有 Submodule 远程 commit 权限，进入你的 Submodule 目录，作些修改，提交

```sh
$ cd another_project
...make your changes...
$ git commit -a -m "fixed a bug"
$ git push origin master
$ cd your_project
$ git add another_project
$ git commit -m "Updated another_project"
$ git push origin master
```

重要一点：由于 Submodule 只比对 commit id，所以你务必要在 Submodule 内做 commit、push后，再到你当前的 Repo 作 push

#### 4. 删除 Submodule

```sh
$ cd your_project
$ git rm --cached another_project
$ rm -rf another_project
$ vim .gitmodules
...remove another_project...
$ vim .git/config
...remove another_project...
$ git commit -a -m 'Remove another_project submodule'
```

**Git Submodule** 虽然是个强大的团队合作开发功能，但请务必不要混乱，否则一些版本错乱是难免的

#### 5. 示例

clone 一个含 submodule 的分支，其 submodule 下再含 submodule，可以采用递归参数 `--recursive`

```sh
$ git clone git@github.com:Ihavee/dotfiles.git
$ cd dotfiles
$ git submodule init
$ git submodule update
$ git submodule foreach --recursive git submodule init
$ git submodule foreach --recursive git submodule update
```
