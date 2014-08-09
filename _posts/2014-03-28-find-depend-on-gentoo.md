---
layout: post
title: "Gentoo 中打包确定依赖"
description: "在 Gentoo 中打包时如何确定依赖"
keywords: "Gentoo, 依赖"
category: Linux
tags: [Gentoo, Packager]
---

看到 Maxthon 发布了 Linux 版本，Gentoo 同学速度很快，ebuild 已经提交到 Gentoo-zh 中，不过为了验证依赖是否全部满足，临时写了个脚本去验证。

几步走：

1. 下载二进制包，解压
2. `ldd file`，确定需要的库文件
3. 取第一结果，排序，去除重复数据
4. `equery b name.so` 去网络上获取软件包名

<!-- more -->
很简单，当然 `equery b name.so` 是在线查询，有时网络环境不好的时候，需要耗费些时间很长，甚至返回空的查询结果。

```sh
#! /usr/bin/env sh
#
# depend.sh
# Copyright (C) 2014 Havee <registerdedicated(at)yeah.net>
#
# Distributed under terms of the GPLv3 license.
#
# Depends on app-portage/pfl
#
# use it on Gentoo

exefile=$1
tmpfile="/tmp/${exefile}.so.show"

[[ -f ${tmpfile} ]] && rm -rf ${tmpfile}

for so in $(ldd ${exefile} | awk '{print $1}' | sort | uniq ); do
    [[ ! -f $so ]] && echo $so >> /tmp/${exefile}.so
done

for so in $(cat /tmp/${exefile}.so); do
    echo  "$so need:"
    equery b $so
    echo ""
done >> ${tmpfile}

rm /tmp/${exefile}.so
cat ${tmpfile}
```
