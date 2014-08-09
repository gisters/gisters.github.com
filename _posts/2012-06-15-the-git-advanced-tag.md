---
layout: post
title: "Git 进阶：tag"
description: "Git Tag 功能就如同 Cvs Tag 是一样的，您可以在目录树里面随意的增减 Tag,方便您记录讯息，一般用来标记版本的发布点"
keywords: git, tag
category: Linux
tags: [Git, Usage]
---

Git Tag 功能就如同 Cvs Tag 是一样的，您可以在目录树里面随意的增减 Tag,方便您记录讯息，一般用来标记版本的发布点。

列出目录树中现有的 Tag

```
$ git tag
 v0.1
 v1.3
```

如果工作目录中 Tag 过多，通过 -l 参数过滤

```
$ git tag -l 'v1.4.2.*'
 v1.4.2.1
 v1.4.2.2
 v1.4.2.3
 v1.4.2.4
```

<!-- more -->

新增标签

-a 标签名称，-m 标签说明，-s GPG 标签签名

```
$ git tag -a v0.1 -m v0.1 d375fcf
$ git tag -a v0.2 -m v0.2 95a6c8c
$ git tag -a v0.3 -m v0.3 1caaa35
$ git tag -a v0.4 -m v0.4 32f5c00
$ git tag
v0.1
v0.2
v0.3
v0.4
```

git show 显示 tag 说明，以及 commit 资料

```
$ git show v0.3
tag v0.3
Tagger: Havanna <skykiss_2000@163.com>
Date:   Fri Jun 15 12:21:48 2012 +0800
 
v0.3
 
commit 1caaa350fddd91c290426e506d77879903e3fc59
Author: Bardon <bardon.sh@gmail.com>
Date:   Tue Jun 12 15:32:48 2012 +0800
 
    change README
 
diff --git a/README b/README
index dd4623f..6a9e8ba 100644
--- a/README
+++ b/README
@@ -1 +1 @@
-my
+Havanna's vimwiki
```

上传 tag 到远端

```
$ git push origin v0.3
Enter passphrase for key '/home/havanna/.ssh/id_rsa':
Counting objects: 1, done.
Writing objects: 100% (1/1), 155 bytes, done.
Total 1 (delta 0), reused 0 (delta 0)
To git@github.com:Havanna/wiki.ihavanna.org.git
 * [new tag]         v0.3 -> v0.3
```

将所有 tag 一次全上传上去

```
$ git push --tags
Enter passphrase for key '/home/havanna/.ssh/id_rsa':
Counting objects: 3, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 402 bytes, done.
Total 3 (delta 0), reused 0 (delta 0)
To git@github.com:Havanna/wiki.ihavanna.org.git
 * [new tag]         v0.1 -> v0.1
 * [new tag]         v0.2 -> v0.2
 * [new tag]         v0.4 -> v0.4
```

删除 tag 很简单，在本地工作目录数中

```
$ git tag -d v0.1
Deleted tag 'v0.1' (was 6445d3f)
```

删除远端 tag

```
$ git push origin :refs/tags/v0.1
Enter passphrase for key '/home/havanna/.ssh/id_rsa':
To git@github.com:Havanna/wiki.ihavanna.org.git
 - [deleted]         v0.1
```

参考资料：[http://www.kernel.org/pub/software/scm/git/docs/git-tag.html](http://www.kernel.org/pub/software/scm/git/docs/git-tag.html)
