---
layout: post
title: "Etag 和 Expires"
description: "本文对页面中 Etag 和 Expires 标识处理，使得页面更加有效被 Cache"
keywords: "etag, expires, cache, http, client, header, server"
category: Internet
tags: [Etag, Expires, Cache, Client, Server, Nginx, Lighttpd, Apache, Usage]
---

题记：本文对页面中 `Etag` 和 `Expires` 标识处理，使得页面更加有效被 Cache。

原版来源: [http://phaedo.cx/archives/2007/07/25/tools-for-optimizing-your-website-etag-and-expire-headers-in-django-apache-and-lighttpd/](http://phaedo.cx/archives/2007/07/25/tools-for-optimizing-your-website-etag-and-expire-headers-in-django-apache-and-lighttpd/)

摘要

- `Etag` 和 `Expires` 中 `Client` 端 `Http Request Header` 及 `Server` 端 `Http Reponse Header` 工作原理。
- 静态下 `Apache`、`Lighttpd` 和 `Nginx` 中 `Etag` 和 `Expires` 配置
- 非实时交互动态页面中 `Etag` 和 `Expires` 处理

在客户端通过浏览器发出第一次请求某一个 URL 时，根据 HTTP 协议的规定，浏览器会向服务器传送报头 (Http Request Header)，服务器端响应同时记录相关属性标记 (Http Reponse Header)，服务器端的返回状态会是 200，格式类似如下：

<!-- more -->
```http
HTTP/1.1 200 OK
Date: Tue, 03 Mar 2009 04:58:40 GMT
Content-Type: image/jpeg
Content-Length: 83185
Last-Modified: Tue, 24 Feb 2009 08:01:04 GMT
Cache-Control: max-age=2592000

Expires: Thu, 02 Apr 2009 05:14:08 GMT
Etag: “5d8c72a5edda8d6a:3239″
```

客户端第二次请求此 URL 时，根据 HTTP 协议的规定，浏览器会向服务器传送报头 (Http Request Header)，服务器端响应并记录相关记录属性标记文件没有发生改动,服务器端返回 304，直接从缓存中读取：

```http
HTTP/1.x 304 Not Modified
Date: Tue, 03 Mar 2009 05:03:56 GMT
Content-Type: image/jpeg
Content-Length: 83185
Last-Modified: Tue, 24 Feb 2009 08:01:04 GMT
Cache-Control: max-age=2592000
Expires: Thu, 02 Apr 2009 05:14:08 GMT
Etag: “5d8c72a5edda8d6a:3239″
```

其中 `Last-Modified`、`Expires` 和 `Etag`
是标记页面缓存标识

### 一、Last-Modified、Expires 和 Etag 相关工作原理

#### 1、Last-Modified

在浏览器第一次请求某一个 URL 时，服务器端的返回状态会是 200，内容是你请求的资源，同时有一个 `Last-Modified` 的属性标记 (Http Reponse Header) 此文件在服务期端最后被修改的时间，格式类似这样：

    Last-Modified: Tue, 24 Feb 2009 08:01:04 GMT

客户端第二次请求此 URL 时，根据 HTTP 协议的规定，浏览器会向服务器传送 `If-Modified-Since` 报头 (Http Request Header)，询问该时间之后文件是否有被修改过：

    If-Modified-Since: Tue, 24 Feb 2009 08:01:04 GMT

如果服务器端的资源没有变化，则自动返回 HTTP 304 （NotChanged.） 状态码，内容为空，这样就节省了传输数据量。当服务器端代码发生改变或者重启服务器时，则重新发出资源，返回和第一次请求时类似。从而保证不向客户端重复发出资源，也保证当服务器有变化时，客户端能够得到最新的资源。

注：如果 `If-Modified-Since` 的时间比服务器当前时间 (当前的请求时间 `request_time`) 还晚，会认为是个非法请求

#### 2、Etag 工作原理

HTTP 协议规格说明定义 `ETag` 为“被请求变量的实体标记” （参见14.19）。简单点即服务器响应时给请求 URL 标记，并在 HTTP 响应头中将其传送到客户端，类似服务器端返回的格式：

    Etag: “5d8c72a5edda8d6a:3239″

客户端的查询更新格式是这样的：

    If-None-Match: “5d8c72a5edda8d6a:3239″

如果 `ETag` 没改变，则返回状态 304。

即:在客户端发出请求后，`Http Reponse Header` 中包含 `Etag: “5d8c72a5edda8d6a:3239″`

标识，等于告诉 Client 端，你拿到的这个的资源有表示 `ID：5d8c72a5edda8d6a:3239`。当下次需要发 Request 索要同一个 URI 的时候，浏览器同时发出一个 `If-None-Match` 报头 ( Http RequestHeader) 此时包头中信息包含上次访问得到的 `Etag: “5d8c72a5edda8d6a:3239″` 标识。

    If-None-Match: “5d8c72a5edda8d6a:3239“

,这样，Client 端等于 Cache 了两份，服务器端就会比对 2 者的 etag。如果 `If-None-Match` 为 `False`，不返回 200，返回 304 (Not Modified) Response。

#### 3、Expires

给出的日期/时间后，被响应认为是过时。如 `Expires: Thu, 02 Apr 2009 05:14:08 GMT`

需和 `Last-Modified` 结合使用。用于控制请求文件的有效时间，当请求数据在有效期内时客户端浏览器从缓存请求数据而不是服务器端. 当缓存中数据失效或过期，才决定从服务器更新数据。

#### 4、Last-Modified 和 Expires

`Last-Modified` 标识能够节省一点带宽，但是还是逃不掉发一个 HTTP 请求出去，而且要和 `Expires` 一起用。而 `Expires` 标识却使得浏览器干脆连 HTTP 请求都不用发，比如当用户 F5 或者点击 `Refresh` 按钮的时候就算对于有 `Expires` 的 URI，一样也会发一个 HTTP 请求出去，所以，`Last-Modified` 还是要用的，而且要和 `Expires` 一起用。

#### 5、Etag 和 Expires

如果服务器端同时设置了 `Etag` 和 `Expires` 时，`Etag` 原理同样，即与 `Last-Modified/Etag` 对应的 `HttpRequest Header:If-Modified-Since` 和 `If-None-Match`。我们可以看到这两个 Header 的值和 WebServer 发出的 `Last-Modified`, `Etag` 值完全一样；在完全匹配 `If-Modified-Since` 和 `If-None-Match` 即检查完修改时间和 `Etag` 之后，服务器才能返回 304.

#### 6、Last-Modified 和 Etag

`Last-Modified` 和 `ETags` 请求的 http 报头一起使用，服务器首先产生 `Last-Modified/Etag` 标记，服务器可在稍后使用它来判断页面是否已经被修改，来决定文件是否继续缓存

过程如下:

1. 客户端请求一个页面（A）。
2. 服务器返回页面A，并在给 A 加上一个 Last-Modified/ETag。
3. 客户端展现该页面，并将页面连同 Last-Modified/ETag 一起缓存。
4. 客户再次请求页面 A，并将上次请求时服务器返回的 Last-Modified/ETag 一起传递给服务器。
5. 服务器检查该 Last-Modified 或 ETag，并判断出该页面自上次客户端请求之后还未被修改，直接返回响应 304 和一个空的响应体。

注：

1. `Last-Modified` 和 `Etag` 头都是由 Web Server 发出的 `Http Reponse Header`，Web Server 应该同时支持这两种头。
2. Web Server 发送完 `Last-Modified/Etag` 头给客户端后，客户端会缓存这些头；
3. 客户端再次发起相同页面的请求时，将分别发送与 `Last-Modified/Etag` 对应的 `Http RequestHeader:If-Modified-Since` 和 `If-None-Match`。我们可以看到这两个 Header 的值和 WebServer 发出的 `Last-Modified`, `Etag` 值完全一样；
4. 通过上述值到服务器端检查，判断文件是否继续缓存；

### 二、Apache、Lighttpd 和 Nginx 中针配置 Etag 和 Expires，有效缓存纯静态如 css/js/pic/页面/流媒体等文件。

#### A、Expires

##### A.1、Apache Etag

使用 `Apache` 的 `mod_expires` 模块来设置，这包括控制应答时的 `Expires` 头内容和 `Cache-Control` 头的 `max-age` 指令

```apacheconf
    ExpiresActive On
    ExpiresByType image/gif “access plus 1 month”
    ExpiresByType image/jpg “access plus 1 month”
    ExpiresByType image/jpeg “access plus 1 month”
    ExpiresByType image/x-icon “access plus 1 month”
    ExpiresByType image/bmp “access plus 1 month”
    ExpiresByType image/png “access plus 1 month”
    ExpiresByType text/html “access plus 30 minutes”
    ExpiresByType text/css “access plus 30 minutes”
    ExpiresByType text/txt “access plus 30 minutes”
    ExpiresByType text/js ”access plus 30 minutes”
    ExpiresByType application/x-javascript ”access plus 30 minutes”
    ExpiresByType application/x-shockwave-flash ”access plus 30 minutes”
```

或

```apacheconf
    <ifmodule mod_expires.c>
    <filesmatch “\.(jpg|gif|png|css|js)$”>
    ExpiresActive on
    ExpiresDefault “access plus 1 year”
    </filesmatch>
    </ifmodule>
```

当设置了 `expires` 后，会自动输出 `Cache-Control` 的 `max-age` 信息

具体关于 `Expires` 详细内容可以查看 `Apache` 官方文档。

在这个时间段里，该文件的请求都将直接通过缓存服务器获取，

当然如果需要忽略浏览器的刷新请求 （F5)，缓存服务器 `squid` 还需要使用 `refresh_pattern` 选项来忽略该请求

    refresh_pattern -i \.gif$ 1440 100% 28800 ignore-reload
    refresh_pattern -i \.jpg$ 1440 100% 28800 ignore-reload
    refresh_pattern -i \.jpeg$ 1440 100% 28800 ignore-reload
    refresh_pattern -i \.png$ 1440 100% 28800 ignore-reload
    refresh_pattern -i \.bmp$ 1440 100% 28800 ignore-reload
    refresh_pattern -i \.htm$ 60 100% 100 ignore-reload
    refresh_pattern -i \.html$ 1440 50% 28800 ignore-reload
    refresh_pattern -i \.xml$ 1440 50% 28800 ignore-reload
    refresh_pattern -i \.txt$ 1440 50% 28800 ignore-reload
    refresh_pattern -i \.css$ 1440 50% 28800 reload-into-ims
    refresh_pattern -i \.js$ 60 50% 100 reload-into-ims
    refresh_pattern . 10 50% 60

有关 `Squid` 中 `Expires` 的说明，请参考 `Squid` 官方中 `refresh_pattern` 介绍。

##### A.2、Lighttpd Expires

和 `Apache` 一样 `Lighttpd` 设置 `expire` 也要先查看是否支持了 `mod_expire` 模块，

下面的设置是让 URI 中所有 images 目录下的文件1小时后过期；

    expire.url = ( “/images/” => “access 1 hours” )

下面是让作用于 images 目录及其子目录的文件；

    $HTTP["url"] =~ “^/images/” {
    expire.url = ( “” => “access 1 hours” )
    }

也可以指定文件的类型；

    $HTTP["url"] =~ “\.(jpg|gif|png|css|js)$” {
    expire.url = ( “” => “access 1 hours” )
    }

具体参考 `Lighttpd` 官方 `Expires` 解释

##### A.3、Nginx 中 Expires

```nginx
location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
{
expires 30d;
}
location ~ .*\.(js|css)?$
{
expires 1h;
}
```

这类文件并不常修改，通过 `expires` 指令来控制其在浏览器的缓存，以减少不必要的请求。 `expires` 指令可以控制 HTTP 应答中的 `Expires` 和 `Cache-Control` 的头标（起到控制页面缓存的作用）。其他请参考 `Nginx` 中 `Expires`

##### B.1、Apache 中 Etag设置

在 `Apache` 中设置 `Etag` 的支持比较简单，只用在含有静态文件的目录中建立一个文件 .htaccess, 里面加入：

    FileETag MTime Size

这样就行了，详细的可以参考 `Apache` 的 `FileEtag` 文档页

##### B.2、Lighttpd Etag

在 `Lighttpd` 中设置 `Etag` 支持：

- etag.use-inode: 是否使用 inode 作为 Etag

- etag.use-mtime: 是否使用文件修改时间作为 Etag

- etag.use-size: 是否使用文件大小作为 Etag

- static-file.etags: 是否启用 Etag 的功能

第四个参数肯定是要 `enable` 的， 前面三个就看实际的需要来选吧，推荐使用修改时间

##### B.3、 Nginx Etag

`Nginx` 中默认没有添加对 `Etag` 标识 .Igor Sysoev 的观点：“在对静态文件处理上看不出如何 Etag 好于 Last-Modified 标识。”

>Note:
>
>Yes, it’s addition,and it’s easy to add, however, I do not see howETag is better than Last-Modified for static files. -Igor Sysoev
>
>A nice short description is here:
>
>h>ttp://www.mnot.net/cache_docs/#WORK
>
>It looks to me that it makes some caches out there to cache theresponse from the origin server more reliable as in rfc2616(ftp://ftp.rfc-editor.org/in-notes/rfc2616.txt) is written.

###### 3.11 Entity Tags 13.3.2 Entity Tag Cache Validators 14.19 ETag

当然也有第三方 `nginx-static-etags` 模块了，请参考 [http://mikewest.org/2008/11/generating-etags-for-static-content-using-nginx](http://mikewest.org/2008/11/generating-etags-for-static-content-using-nginx)

### 三、对于非实时交互动态页面中 Epires 和 Etag 处理

对数据更新并不频繁、如 `tag` 分类归档等等，可以考虑对其 `cache`。简单点就是在非实时交互的动态程序中输出 `expires` 和 `etag` 标识，让其缓存。但需要注意关闭 `session`，防止 `http response` 时 `http header` 包含 `session id` 标识；

#### 1、Expires

如 `expires.php`

```php
<?php
header(’Cache-Control: max-age=86400,must-revalidate’);
header(’Last-Modified: ‘ .gmdate(’D, d M Y H:i:s’) . ‘ GMT’ );
header(”Expires: ” .gmdate (’D, d M Y H:i:s’, time() + ‘86400′ ). ‘ GMT’);
?>
```

以上信息表示该文件自请求后 24 小时后过期。

其他需要处理的动态页面直接调用即可。

#### 2、Etag

根据 Http 返回状态来处理。当返回 304 直接从缓存中读取

如 `etag.php`

```php
<?php
cache();
echo date(”Y-m-d H:i:s”);
function cache()
{
$etag = “http://longrujun.name”;
if ($_SERVER['HTTP_IF_NONE_MATCH'] == $etag)
{
header(’Etag:’.$etag,true,304);
exit;
}
else header(’Etag:’.$etag);
}
?>
```
