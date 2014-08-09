---
layout: post
title: "PHP SQLite 示例"
description: "介绍下 SQLite API 所支持的重要方法，提供一个能够用在你开发中的简单脚本模板，从而告诉你如何使用 PHP 与 SQLite 数据库进行交互操作"
keywords: php, sqlite, 示例, api, 方法
category: SQL
tags: [PHP, SQL, Usage]
---

介绍下 SQLite API 所支持的重要方法，提供一个能够用在你开发中的简单脚本模板，从而告诉你如何使用 PHP 与 SQLite 数据库进行交互操作。假设你已经安装好了 (Apache|nginx) & PHP。

你的系统上并不是一定非要安装可交互的 SQLite 程序；但是为了能够简化创建本教程所需要的一系列初始表格，你应该下载和安装这个程序。然后，为你的 SQL 查询创建一个示例表格，方法是创建一个空白的文本文件，将该文件名作为下列命令（列表A）的参数在交互命令提示符下执行二进制程序：

```sql
sqlite> CREATE TABLE users (id INTEGER PRIMARY KEY, username TEXT, country TEXT);
sqlite> INSERT INTO users VALUES (1, 'john', 'IN');
sqlite> INSERT INTO users VALUES (2, 'joe', 'UK');
sqlite> INSERT INTO users VALUES (3, 'diana', 'US');
```

<!-- more -->

一旦表格创建好了，下面就是使用 PHP 的 SQLite 方法建立一个脚本模板。

```php
<?php
// 使用的 DB 库
$db = "users.db";

// 打开 DB 连接，请确保 DB 的可读写性
// make sure script has read/write permissions!
$conn = sqlite_open($db) or die ("ERROR: Cannot open database");

// 执行 insert 插入操作
$sql = "INSERT INTO users (id, username, country) VALUES ('5', 'pierre', 'FR')";
sqlite_query($conn, $sql) or die("Error in query execution: " .　sqlite_error_string(sqlite_last_error($conn)));

// 执行 select 查询操作，并将结果传递给 $result
$sql = "SELECT username, country FROM users";
$result = sqlite_query($conn, $sql) or die("Error in query execution: " . sqlite_error_string(sqlite_last_error($conn)));

// 将 $result 数组化，并用数组的序号和索引来选择显示的内容，最后打印出
// print if available
if (sqlite_num_rows($result) --> 0) {
    while($row = sqlite_fetch_array($result)) {
        echo $row[0] . " (" . $row[1] . ") ";
    }
}

// 关闭 DB 连接
sqlite_close($conn);
?>
```

在使用 PHP 的 SQLite 扩展执行 SQL 查询的时候，要按照下列四个简单步骤进行：

1. 调用 sqlite_open() 函数来初始化数据库句柄。数据库的路径和文件名（要记住，SQLite 是基于文件的，而不是像 MySQL 那样基于服务器）被作为自变量传递给函数。

2. 创建 SQL 查询字符串，用 sqlite_query() 函数执行它。这些方法的结果对象会依据查询的类型以及它是否成功而有所不同。成功的 SELECT 查询会返回一个结果对象；成功的 INSERT／UPDATE／DELETE 查询会返回一个资源标识符；不成功的查询会返回“伪”。 sqlite_error_string() 和 sqlite_last_error() 方法能够被用来捕捉错误并显示相应的错误信息。

3. 对于 SELECT 查询，结果对象可以被进一步处理，以便从中提取数据。当 sqlite_fetch_array() 函数用在循环里的时候会把每条记录作为PHP的数组取回。你可以通过调用数组合适的键来访问每条记录的各个字段。

4. 调用 sqlite_close() 函数可以结束会话。

PHP 5.x 的一个创新之举是加入了 SQLite 数据库引擎。 SQLite 是一个基于文件的、功能齐全的可移植数据库引擎，它能够被用来进行绝大多数 SQL 操作而不会加重客户端－服务器通信的负载。PHP 5.x 里的这个 SQLite API 在默认情况下会被激活，这也意味着你可以立即就使用 SQLite。

希望这个脚本模块能够在你下一次坐下来用 PHP 编写 SQLite 连接／交互例程的时候为你节省一些时间。
