---
layout: post
title: "域名反向代理"
description: "，Nginx 的域名反向代理"
keywords: "域名, 反向, 代理, nginx"
category: Internet
tags: [Nginx,Domain]
---

最近，忽然又想重新拿起博客域名，想备案了，拿到阿里云上，等待4天左右，拿到备案号。

由于阿里云的备案复查比较严，时不时给你个电话告诉你，你的域名没解析到备案IP。于是想了个办法，[Nginx](http://nginx.org/) 的域名反向代理。

想到就做，将 `ihavanna.org` 解析到阿里云指定IP：

```sh
$ dig ihavanna.org +nostats +nocomments +nocmd

; <<>> DiG 9.9.2-P2 <<>> ihavanna.org +nostats +nocomments +nocmd
;; global options: +cmd
;ihavanna.org.                  IN      A
ihavanna.org.           3600    IN      A       111.111.111.111
```

<!-- more -->
将 `www.ihavanna.org` cname 到 `ihavanna.org`：

```sh
$ dig www.ihavanna.org +nostats +nocomments +nocmd

; <<>> DiG 9.9.2-P2 <<>> www.ihavanna.org +nostats +nocomments +nocmd
;; global options: +cmd
;www.ihavanna.org.              IN      A
www.ihavanna.org.       3600    IN      CNAME   ihavanna.org.
ihavanna.org.           3600    IN      A       111.111.111.111
```

同时ssh到阿里云，创建个主机配置文件：

```nginx
server {
  listen                    80              default;
  server_name    ihavanna.org;
  index  index.html index.htm;

  loction /{
    proxy_pass              http://ihavanna.github.com/;
    proxy_redirect          off;
    proxy_set_header        Host            $http_host;
    proxy_set_header        X-Real-IP       $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_buffer_size       64k;
    proxy_buffers           32              64k;
  }
}
```

OK，完工。
