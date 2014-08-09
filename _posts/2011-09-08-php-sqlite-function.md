---
layout: post
title: "PHP SQLite 函数库"
description: "PHP SQlite数据库对于拥有相当经验的PHP程序员来说是不会陌生的，但是要想完全掌握PHP SQlite数据库的相关函数的应用也不是一件易事"
keywords: php, sqlite, 函数
category: SQL
tags: [PHP, SQL, Function]
---

PHP SQlite 数据库对于拥有相当经验的 PHP 程序员来说是不会陌生的，但是要想完全掌握 PHP SQlite 数据库的相关函数的应用也不是一件易事。下面为大家总结一些 PHP SQlite 数据库的相关函数。

    sqlite_array_query

// 发送一条 SQL 查询，并返回一个数组。

    sqlite_unbuffered_query

// 发送一条 SQL 查询，并不获取和缓存结果的行。

    sqlite_busy_timeout
<!-- more -->
// 设置超时时间 (busy timeout duration)，或者频繁的用户失去权限 (disable busy handlers)

    sqlite_changes

// 返回被最新的 SQL 查询 (changed by the most recent SQL statement) 改变的行数。

    sqlite_close

// 关闭一个打开的 SQLite 数据库。

    sqlite_column

// 在当前的行中取得一列 (a column from the current row of a result set)。

    sqlite_create_aggregate

// Register an aggregating UDF for use in SQL statements。

    sqlite_create_function

// Registers a “regular” User Defined Function for use in SQL statements。

    sqlite_current

// 在返回的数组中取得当前的行 (the current row from a result set as an array)。

    sqlite_error_string

// 返回错误代码的原始描述 (the textual description of an error code)。

    sqlite_escape_string

// 释放一个用于查询的字符串 (Escapes a string for use as a query parameter)。

    sqlite_fetch_array

// 取得下一行并设置成一个数组 (the next row from a result set as an array)。

    sqlite_fetch_single

// 取得第一列并设置成一个字符串 (Fetches the first column of a result set as a string)。

    sqlite_fetch_string

// sqlite_fetch_single() 的别名。

    sqlite_field_name

// 取得结果中指定字段的字段名。

    sqlite_has_more

// 返回是否有更多可用的行 (whether or not more rows are available)。

    sqlite_last_error

// 返回数据库的最新的错误代码 (the error code of the last error for a database)。

    sqlite_last_insert_rowid

// 返回最新插入的行的行号 (the most recently inserted row)。

    sqlite_libencoding

// 返回 SQLite 库 (SQLite library) 的编码 (encoding)。

    sqlite_libversion

// 返回 SQLite 库 (SQLite library) 的版本。

    sqlite_next

// 返回下一行的行号。

    sqlite_num_fields

// 取得结果集中字段的数目。

    sqlite_num_rows

// 取得结果集中行的数目。

    sqlite_open

// 打开一个 SQLite 数据库。如果文件不存在则尝试创建之。

    sqlite_popen

// 用永久连接的方式打开一个 SQLite 数据库。如果文件不存在则尝试创建之。

    sqlite_query

// 发送一条 SQL 查询，并返回一个结果句柄 (a result handle)。

    sqlite_rewind

// 倒回第一行 (Seek to the first row number)。

    sqlite_seek

// 在缓存结果中查找特定的行号 (Seek to a particular row number of a buffered result set)。

    sqlite_udf_decode_binary

// Decode binary data passed as parameters to an UDF。

    sqlite_udf_encode_binary

// Encode binary data before returning it from an UDF。
