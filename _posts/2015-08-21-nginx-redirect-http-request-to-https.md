---
layout: post
title: "Nginx HTTP 跳转至 HTTPS"
category: Internet
tags: [Nginx]
---

前面已经介绍过 [服务端 SSL 配置]({% post_url 2015-06-10-strong-ssl-security-for-apache-nginx-lighttpd %})。

今天有人问起，如何让 HTTP 自动跳转至 HTTPS 呢？

抛开性能问题，nginx 的跳转有三种写法，即 `rewrite`、`return`、`error_page`。

一般，我们常见的写两个 Server，即：

```nginx
server {
    listen 80;
    server_name domain.com;
    rewrite ^(.*) https://$server_name$1 permanent;
}
server {
    listen 443 ssl;
    server_name domain.com;
    ssl on;
    # other
}
```

<!-- more -->

```nginx
server {
    listen 80;
    server_name domain.com;
    return 301 https://$server_name$request_uri;
}
server {
    listen 443 ssl;
    server_name domain.com;
    ssl on;
    # other
}
```

还有一种方法，非常规手段，即利用 497 状态码。

当此虚拟机只允许 HTTPS 来访问时，用 HTTP 访问会让 Nginx 报 497 错误，然后利用 error_page 将链接重定向至 HTTPS 上，即：

```nginx
server {
    listen 443 ssl;
    listen 80;
    server_name domain.com;
    ssl on;
    # other
    error_page 497 https://$server_name$request_uri;
}
```

当然 497 一般用于非标准端口上，譬如 9443 端口默认使用使用 ssl

```nginx
server {
    listen 9443 ssl;
    server_name domain.com;
    # other
    error_page 497 https://$server_name$server_port$request_uri;
}
```
