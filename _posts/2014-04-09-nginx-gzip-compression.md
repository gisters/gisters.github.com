---
layout: post
title: "Nginx 打开 Gzip 压缩"
description: "整理 Nginx 中 Gzip 模块的详细配置说明"
keywords: "nginx, gzip, 压缩"
category: Internet
tags: [Nginx]
---
{% include JB/setup %}

折腾无止境，最近重新看了下之前对 [Nginx](http://nginx.org) 中的 Gzip 模块的配置，很多参数都是不求甚解。今天整理下，作为笔记来记录。

首先我对 Nginx 中 Gzip 的配置如下：

```nginx
gzip  on;
gzip_min_length     1k;
gzip_buffers        4 8k;
gzip_http_version   1.0;
gzip_comp_level     6;
gzip_proxied        any;
gzip_types          text/xml text/html text/css text/javascript text/plain application/json \
    application/x-javascript application/xml application/xml+rss;
gzip_vary           on;
gzip_disable        "MSIE [1-6]\.";
```

<!-- more -->
下面是我整理的参数的详细说明：

表1：Gzip

|**Variable**|**Parameter**|**Example**|**Details**|
|:---|:---|:---|:---|
|**gzip**|on/off|`gzip on;`|决定是否开启 Gzip 模块。|
|**gzip_buffers**|param1:int/param2:int(k)|`gzip_buffer 4 8k;`|设置 Gzip 申请内存的大小，其作用是按照块大小的倍数来申请内存空间。|
|**gzip_comp_level**|1-9|`gzip_com_level 6;`|设置 Gzip 压缩等级，等级越低，压缩速度越快，文件压缩比越小；反之速度越慢，文件压缩比越大。|
|**gzip_desable**|regex ...|`"MSIE [1-6]\.";`|根据 "User-Agent" 头来关闭 Gzip，可用正则表达式。|
|**gzip_min_length**|int|`gzip_min_length 1k;`|当返回内容大于此值是才开启 Gzip 进行压缩，以 k 为单位，当值设置为 0 时，所有页面都进行压缩。|
|**gzip_http_version**|1.0/1.1|`gzip_http+version 1.0;`|用于识别 http 协议的版本，早期的浏览器不支持 Gzip 压缩，用户就会看到乱码，所以为了支持前期版本加上了这个选项，**如果你用了 Nginx 的反向代理并期望也启用 Gzip 压缩的话，由于末端通信是 `http/1.0`，故请设置为 `1.0`**。|
|**gzip_proxied**|off/expired/no-cache/no-store/private/no_last_modified/no_etag/auth/any|`gzip_proxied no-cache;`|详细说明见下表格。|
|**gzip_types**|mime-type|gzip_type text/html;|设置需要压缩的 MIME 类型，不设置则不进行压缩。|
|**gzip_vary**|on/off|`gzip_vary on;`|加上 http 头信息`Vary: Accept-Encoding`给后端代理服务器识别是否启用 Gzip 压缩。|

表2：gzip_proxied

|**Parameter**|**Details**|
|:---|:---|
|off|关闭所有的代理结果的数据压缩。|
|expired|启用压缩，如果 Header 中包含 `Expires` 头信息。|
|no-cache|启用压缩，如果 Header 中包含 `Cache-Control: no-cache` 头信息。|
|no-store|启用压缩，如果 Header 中包含 `Cache-Control: no-store` 头信息。|
|private|启用压缩，如果 Header 中包含 `Cache-Control: private` 头信息。|
|no_last_modified|启用压缩，如果 Header 中包含 `Last_Modified` 头信息。|
|no_etag|启用压缩，如果 Header 中包含 `ETag` 头信息。|
|auth|启用压缩，如果 Header 中包含 `Authorization` 头信息。|
|any|无条件压缩所有结果数据。|

可以用以下命令判断服务器 Nginx 是否开启 Gzip 压缩

    curl -IL -H "Accept-Encoding: gzip, deflate" "havee.me"

参考：[http://nginx.org/en/docs/http/ngx_http_gzip_module.html](http://nginx.org/en/docs/http/ngx_http_gzip_module.html)

有关其他模块的详细说明：[http://wiki.nginx.org/Modules](http://wiki.nginx.org/Modules)
