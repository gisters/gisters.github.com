---
layout: post
title: "防 Joomla 后台被暴力猜解"
description: "今天朋友吐糟，最近他的 **Joomla** 后台老被境外的 IP **暴力猜解**密码，烦不胜烦"
keywords: "joomla, 后台, 猜解, 暴力破解"
category: Internet
tags: [Joomla]
---

今天朋友吐糟，最近他的 **Joomla** 后台老被境外的 IP **暴力破解**密码，烦不胜烦，特别是他们并非一秒钟几十次猜解，而是几秒钟猜解一次，结果就是 **logs/error.php** 日志大小成上升态势。

拿到 **error.php** 文件

```
……
2013-07-17	16:47:54	INFO	213.203.234.132	Joomla FAILURE: 	Username and password do not match or you do not have an account yet.
2013-07-17	16:47:57	INFO	213.203.234.132	Joomla FAILURE: 	Username and password do not match or you do not have an account yet.
2013-07-17	16:48:00	INFO	213.203.234.132	Joomla FAILURE: 	Username and password do not match or you do not have an account yet.
2013-07-17	16:48:03	INFO	213.203.234.132	Joomla FAILURE: 	Username and password do not match or you do not have an account yet.
……
```

<!-- more -->
仔细看了下，确实是几秒一次猜解，文档很大，IP 基本每分钟就变换下，传统的 netstat 统计 IP 再屏蔽方式已经不适用，考虑到 **Joomla** 平台 本身就有 logs 系统，为何不就对 **error.php** 动一些脑筋呢

思路明确了，那么就开始，awk 取第四个字段，统计排序，取次数大于 4 的 IP 加入到 iptables 中加以屏蔽，配合 Linux 系统计划任务，每 5 分钟执行一次

```
/etc/init.d/iptables restart; [[ -f /tmp/block ]] && rm /tmp/block; awk '{print $4}' /var/www/YOURPATH/error.php | sort | uniq -c | sort -r | awk '($1>4) {print $2}' >> /tmp/block; for i in `awk '{print $1}' /tmp/block`;do iptables -I INPUT -p tcp -s $i -j DROP; done; rm /tmp/block; echo "" > /var/www/YOURPATH/error.php
```

可读性不强，整理下

```bash
#!/bin/bash

# 重启 iptables，系统路径可能有所不同
/etc/init.d/iptables restart

[[ -f /tmp/block ]] && rm /tmp/block

# 取 error.php 第四个字段，排序后，打印序号大于 4 的行到 /tmp/black
awk '{print $4}' /var/www/YOURPATH/error.php | sort | uniq -c | sort -r \
    | awk '($1>4) {print $2}' >> /tmp/block

# for 循环加入 ip 到 iptables
for i in `awk '{print $1}' /tmp/block`; do
    iptables -I INPUT -p tcp -s $i -j DROP
done

# 一些清理工作
rm /tmp/block
echo "" > /var/www/YOURPATH/error.php
```
存为 block 文件，加上执行权限 `chmod +x block`。

放入计划任务中，编辑 /etc/crontab，在最后加上：

    */5 * * * * root    sh /YOURPATH/block

每5分钟 4 次的后台猜解，条件已经设定的很低了，如果自己遗忘密码，那么等 5 分钟后再登录吧。

肯定还有不完善的地方，给了朋友后同时嘱咐下后面改进改进，一些特殊情况再加一些条件做判断吧，大体不会出问题的了。这里我们抛弃了传统的 netstat，转而采用平台本身的日志来进行 iptables 屏蔽操作。
