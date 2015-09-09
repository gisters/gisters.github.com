---
layout: post
title: "Mysql 最小化资源配置"
category: Linux
tags: [MySQL, SQL]
---

小内存 vps 很多，有时我们只是用来安装一个博客而已，但是 MySQL 默认的配置比较迟资源

|Setting|Default|Minimum|
|---|---|---|
|innodb_buffer_pool_size|128M|5M|
|innodb_log_buffer_size|1M|0|
|query_cache_sizequery_cache_size|1M|0|
|max_connections|151|1(although 10 might be more reasonable)|
|key_buffer_size|8388608|8|
|thread_cache_size|(autosized)|0|
|host_cache_size|(autosized)|0|
|innodb_ft_cache_size|8000000|1600000|
|innodb_ft_total_cache_size|640000000|32000000|
|thread_stack|262144|131072|
|sort_buffer_size|262144|32K|
|read_buffer_size|131072|8200|
|read_rnd_buffer_size|262144|8200|
|max_heap_table_size|16777216|16K|
|tmp_table_size|16777216|1K|
|bulk_insert_buffer_size|8388608|0|
|join_buffer_size|262144|128|
|net_buffer_length|16384|1K|
|innodb_sort_buffer_size|1M|64K|
|binlog_cache_size|32K|4K|
|binlog_stmt_cache_size|32K|4K|

<!-- more -->
你当然可以通过禁用 InnoDB 来节省内存，但一般不建议这么做，除非你的内存小到了极致。

如此，我们针对便宜的小内存 vps 的 my.cnf 应该是如下：

```mysql
# /etc/my.cnf:
innodb_buffer_pool_size=5M
innodb_log_buffer_size=256K
query_cache_size=0
max_connections=10
key_buffer_size=8
thread_cache_size=0
host_cache_size=0
innodb_ft_cache_size=1600000
innodb_ft_total_cache_size=32000000

# per thread or per operation settings
thread_stack=131072
sort_buffer_size=32K
read_buffer_size=8200
read_rnd_buffer_size=8200
max_heap_table_size=16K
tmp_table_size=1K
bulk_insert_buffer_size=0
join_buffer_size=128
net_buffer_length=1K
innodb_sort_buffer_size=64K

#settings that relate to the binary log (if enabled)
binlog_cache_size=4K
binlog_stmt_cache_size=4K

```
