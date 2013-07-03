---
layout: post
title: "Gentoo 中用 mariadb 替换 mysql"
description: "Gentoo 中用 mariadb 替换 mysql"
category: SQL
tags: [SQL, Gentoo]
---
{% include JB/setup %}

- 编辑 `/var/lib/portage/world` 文件，用 `virtual/mysql` 替换 `dev-db/mysql`
- 编辑 `/etc/portage/package.use`，确保 `dev-db/mariadb` 具有与 `dev-db/mysql` 相同的 USE 标记

```
# /etc/init.d/mysql stop
# emerge -1 mariadb
# revdep-rebuild
# /etc/init.d/mysql start
# mysql_upgrade -u root -p
# mysqlcheck --repair --all-databases -u root -p
```

OK，完工
