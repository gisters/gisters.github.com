---
layout: post
title: "Git 进阶：rebase"
category: Linux
tags: [Git, Usage]
---

两个分支 —— 姑且称之为 old、new，当 old 分支没有改变，new 分支有了新的 commit 后，想要将 new 分支的 commit 合并到 old 上，那么

    git checkout old
    git merge new

这样简单的操作即可完成，但是如果 old 分支也有了新的 commit，用 merge 合并时就会出现冲突，这时，rebase 的用处就体现出来了

    git checkout old
    git rebase new

<!-- more -->
这样会将 new 分支的变更合并过来，并按照提交的顺序排序，出现冲突时，先解决冲突，然后

    git add .

无需 `git commit`，解决冲突后，继续执行

    git rebase --continue

直至完成整个合并操作。

其中，你可以随时通过

    git rebase --abort

来终止 rebase 的整个操作，恢复到 rebase 之前的状态。

更多详情，可以参考：<http://git-scm.com/docs/git-rebase>
