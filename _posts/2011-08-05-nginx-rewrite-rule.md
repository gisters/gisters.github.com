---
layout: post
title: "Nginx 伪静态"
category: Internet
tags: [Nginx, Rewrite, Wordpress, Joomla, UChome]
---

#### 介绍

Nginx 伪静态，我们一般使用 rewrite 来实现，通过 Nginx 提供的变量或自己设置的变量，配合正则与标志位来进行 URL 重写。

##### 标志位

- last：标志完成
- break：停止后续 rewrite
- redirect：302临时重定向
- permanent：301 永久重定向

##### if 指令

一般用法

```nginx
if(condition) {
    statement
}
```

<!-- more -->

表示如果条件为真，则执行语句，其中，条件可以是一个变量或表达式。当为一个变量时，值为空或任何以 0 开头的字符串时，则都为 false，表达式可以直接使用 `=` 或 `!=` 来比较变量或内容，`~` 正则匹配，`~*` 匹配不区分大小写，`!~` 匹配区分大小写。

- -f/!-f：判断文件是否存在
- -d/!-d：判断目录是否存在
- -e/!-e：判断文件或目录是否存在
- -x/!-x：判断文件是否可以执行

##### 全局变量

- $args：请求中的参数
- $content_length：请求 HEAD 中的 Content-length
- $content_type：请求 HEAD 中的 Content_type
- $document_root：当前请求中 root 的值
- $host：主机头
- $http_user_agent：客户端 agent
- $http_cookie：客户端 cookie
- $limit_rate：限制连接速率
- $request_method：客户端请求方式，GET/POST
- $remote_addr：客户端 IP
- $remote_port：客户端端口
- $remote_user：验证的用户名
- $request_filename：请求的文件绝对路径
- $scheme：http/http
- $server_protocol：协议，HTTP/1.0 OR HTTP/1.1
- $server_addr：服务器地址
- $server_name：服务器名称
- $server_port：服务器端口
- $request_uri：包含请求参数的 URI
- $uri：不带请求参数的 URI
- $document_uri：同 $uri

示例：http://localhost:8080/name1/name2/name.php?args=n

    $host:              localhost
    $server_port:       8080
    $request_uri:       /name1/name2/name.php?args=n
    $document_uri:      /name1/name2/name.php
    $document_root:     /var/www/localhost
    $request_filename:  /var/www/localhost/name1/name2/name.php

##### 正则

- .：匹配任意字符，换行符除外
- ?：重复 1 次或 0 次
- +：重复 1 次或更多
- *：重复 0 次或更多
- \\d：匹配数字
- ^：匹配字符串开头
- $：匹配字符串结尾
- {n}：重复 n 次
- {n,}：重复次数大于等于 n
- [n]：匹配单个字符n
- [a-z]：匹配 a-z 任意一个小写字母

`$1`,`$2` ... `$n` 表示匹配表达式中的第一，第二 ... 第 N 个括号中内容

#### 示例

##### wordpress

```nginx
location / {
  try_files $uri $uri/ /index.php?q=$uri&$args;
}
```

##### joomla

```nginx
location / {
  if ( $args ~ "mosConfig_[a-zA-Z_]{1,21}(=|\%3d)" ) {
    set $args "";
    rewrite ^.*$ http://$host/index.php last;
  return 403;}
  if ( $args ~ "base64_encode.*\(.*\)") {
    set $args "";
    rewrite ^.*$ http://$host/index.php last;
  return 403;}
  if ( $args ~ "(\<|%3C).*script.*(\>|%3E)") {
    set $args "";
    rewrite ^.*$ http://$host/index.php last;
  return 403;}
  if ( $args ~ "GLOBALS(=|\[|\%[0-9A-Z]{0,2})") {
   set $args "";
    rewrite ^.*$ http://$host/index.php last;
  return 403;}
  if ( $args ~ "_REQUEST(=|\[|\%[0-9A-Z]{0,2})") {
    set $args "";
    rewrite ^.*$ http://$host/index.php last;
  return 403;}
  if (!-e $request_filename) {
    rewrite (/|\.php|\.html|\.htm|\.feed|\.pdf|\.raw|/[^.]*)$ /index.php last;
    break;}
}
```

##### uchome

```nginx
location / {
  rewrite ^/(space|network)\-(.+)\.html$ /$1.php?rewrite=$2 last;
  rewrite ^/(space|network)\.html$ /$1.php last;
  rewrite ^/([0-9]+)$ /space.php?uid=$1 last;
}
```
