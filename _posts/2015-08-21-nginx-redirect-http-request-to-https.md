---
layout: post
title: "Nginx http 跳转至 https"
category: Internet
tags: [Nginx]
---

前面已经介绍过 [服务端 SSL 配置]({% post_url 2015-06-10-strong-ssl-security-for-apache-nginx-lighttpd %})。

今天有人问起，如何让 http 自动跳转至 https 呢？

常见的写法有两种，即写两个 Server，这个没有什么异议，也不可能混淆，大家都会，即：

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
	......
}
```

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
	......
}
```

还有一种方法，不太常见，即利用 497 状态码。

当此虚拟机只允许 https 来访问时，用 http 访问会让 nginx 报 497 错误，然后利用 error_page 将链接重定向至 https 上，即：

```nginx
server {
    listen 443 ssl;
    listen 80;
    server_name domain.com;
	ssl on;
	......
	error_page 497 https://$server_name$request_uri;
}
```