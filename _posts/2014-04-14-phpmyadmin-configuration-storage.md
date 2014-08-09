---
layout: post
title: "phpMyAdmin 扩展功能"
description: "phpMyAdmin "
keywords: "phpMyAdmin 的额外特性"
category: SQL
tags: [phpMyAdmin]
---

一直没有使用过 phpMyAdmin，今天通过 `brew install phpmyadmin` 安装了下，发现 phpMyAdmin 多了些新特性，需要激活使用。

>The phpMyAdmin configuration storage is not completely configured, some extended features have been deactivated. To find out why click here.

对于运行低于版本 3.0 的 phpMyadmin，或低于版本 4.1.2 的 MySQL，需要注意的事项。

<!-- more -->
- MySQL 版本低于 4.1.2，使用 `./scripts/create_tables.sql`。
    - phpMyAdmin 3.0 以及以后的版本，将不支持低于 4.1.2 版本的 MySQL。
- MySQL 版本高于 4.1.2，phpMyAdmin 低于 3.0的情况下，使用 `./scripts/create_tables_mysql_4_1_2+.sql`。

由于俺非主流了，运行的是 MariaDB 10.0.10，不清楚 phpMyAdmin 如何识别我的版本，反正有了以上的提示信息，按照说明照做吧。

1. 找到 `./scripts/create_tables.sql` 或 `./examples/create_tables.sql`，通过 phpMyAdmin 或其他方式导入库。

2. 编辑./config.inc.php

```js+php
/*
* phpMyAdmin configuration storage settings.
*/

/* User used to manipulate with storage */
// $cfg['Servers'][$i]['controlhost'] = '';
// $cfg['Servers'][$i]['controlport'] = '';
$cfg['Servers'][$i]['controluser'] = 'root';
$cfg['Servers'][$i]['controlpass'] = 'yourpassword';

/* Storage database and tables */
$cfg['Servers'][$i]['pmadb'] = 'phpmyadmin';
$cfg['Servers'][$i]['bookmarktable'] = 'pma__bookmark';
$cfg['Servers'][$i]['relation'] = 'pma__relation';
$cfg['Servers'][$i]['table_info'] = 'pma__table_info';
$cfg['Servers'][$i]['table_coords'] = 'pma__table_coords';
$cfg['Servers'][$i]['pdf_pages'] = 'pma__pdf_pages';
$cfg['Servers'][$i]['column_info'] = 'pma__column_info';
$cfg['Servers'][$i]['history'] = 'pma__history';
$cfg['Servers'][$i]['table_uiprefs'] = 'pma__table_uiprefs';
$cfg['Servers'][$i]['tracking'] = 'pma__tracking';
$cfg['Servers'][$i]['designer_coords'] = 'pma__designer_coords';
$cfg['Servers'][$i]['userconfig'] = 'pma__userconfig';
$cfg['Servers'][$i]['recent'] = 'pma__recent';
$cfg['Servers'][$i]['users'] = 'pma__users';
$cfg['Servers'][$i]['usergroups'] = 'pma__usergroups';
$cfg['Servers'][$i]['navigationhiding'] = 'pma__navigationhiding';
/* Contrib / Swekey authentication */
// $cfg['Servers'][$i]['auth_swekey_config'] = '/etc/swekey-pma.conf';
```

参考：[http://wiki.phpmyadmin.net/pma/Configuration_storage](http://wiki.phpmyadmin.net/pma/Configuration_storage)
