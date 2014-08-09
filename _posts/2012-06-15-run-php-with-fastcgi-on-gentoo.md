---
layout: post
title: "Gentoo 中以 FastCGI 方式运行 PHP"
description: "PHP 的 FastCGI 使你的所有 php 应用软件通过 mod_fastci 运行，而不是 mod_phpsusexec 。FastCGI 应用速度很快是因为他们持久稳定。"
keywords: gentoo, php-fpm, fastcgi, lighttpd, nginx, apache2
category: Internet
tags: [Gentoo, PHP-FPM, FastCGI, Lighttpd, Nginx, Apache]
---

PHP 的 FastCGI 使你的所有 php 应用软件通过 mod_fastci 运行，而不是 mod_phpsusexec 。FastCGI 应用速度很快是因为他们持久稳定。不必对每一个请求都启动和初始化。好处是 PHP 脚本运行速度提升 3-30 倍；并不需要对现有的PHP代码做任何的更改；PHP 解释程序被载入内存而不用每次需要时从存储器读取，极大的提升了依靠脚本运行的站点的性能；同时速度的提升并不会增加 CPU 的负担。

#### php

很简单，打开 fpm 这个 USE 标记即可。

    # echo "dev-lang/php fpm" >> /etc/portage/package.use
    # emerge -av dev-lang/php

配置很简单，我本机版本是 php5.4，那么编辑文件 `/etc/php/fpm-php5.4/php-fpm.conf`，修改 listen address

```ini
;listen = 127.0.0.1:9000
listen = /var/run/php-fpm.sock
```

<!-- more -->

#### nginx

打开 fastcgi USE 标记

    # echo "www-servers/nginx fastcgi" >> /etc/portage/package.use
    # emerge -av www-servers/nginx

编辑 /etc/nginx/nginx.conf

```nginx
location ~ .*\.php$ {
    fastcgi_pass   unix:/var/run/php-fpm.sock;
    fastcgi_index  index.php;
    fastcgi_param SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    include        fastcgi_params;
}
```
#### lighttpd

打开 php USE 标记

    # echo "www-servers/lighttpd php" >> /etc/portage/package.use
    # emerge -av www-servers/lighttpd

编辑 `/etc/lighttpd/mod_fastcgi.conf`

```javascript
server.modules += ("mod_fastcgi")
fastcgi.server = ( ".php" =>
    ( "localhost" =>
        (
            "socket"    => "/var/run/php-fpm.sock",
        )
    )
)
fastcgi.map-extensions = ( ".php3" => ".php", ".php4" => ".php", ".php5" => ".php" )
```

#### apache

首先需安装 `www-apache/mod_fastcgi_handler`

    # emerge -av www-apache/mod_fastcgi_handler

同时 PHP 需要打开 apache2 USE 标记

    # echo "dev-lang/php apache2" >> /etc/portage/package.use
    # emerge -av dev-lang/php

编辑文件 `/etc/apache2/modules.d/70_mod_php5.conf`，告诉 apache2，php-fpm sock 的路径

```apache
<IfModule mod_mime.c>
#    AddHandler application/x-httpd-php .php .php5 .phtml
#    AddHandler application/x-httpd-php-source .phps
    AddHandler fcgi:/var/run/php-fpm.sock .php .php5
</IfModule>
```

编辑文件 `/etc/apache2/modules.d/20_mod_fastcgi_handler.conf`

```apache
<IfDefine FASTCGI_HANDLER>
LoadModule fastcgi_handler_module modules/mod_fastcgi_handler.so
</IfDefine>
```

编辑文件 `/etc/conf.d/apache2`，在 `-D php5` 后面添加 `-D FASTCGI_HANDLER`，告诉 apache2 以 fastcgi-handler 模式启动，如下是我的配置

```ini
APACHE2_OPTS="-D DEFAULT_VHOST -D INFO -D SSL -D SSL_DEFAULT_VHOST -D LANGUAGE -D PHP5 -D FASTCGI_HANDLER"
```

参考资料

- [http://mattmcadoo.com/content/mini-howto-setting-php-fpm-apache-gentoo](http://mattmcadoo.com/content/mini-howto-setting-php-fpm-apache-gentoo)
- [http://serversreview.net/lighttpd-php-with-php-fpm-and-mysql-under-ubuntu-maverick](http://serversreview.net/lighttpd-php-with-php-fpm-and-mysql-under-ubuntu-maverick)
- [http://php-fpm.org/wiki/Configuration_File](http://php-fpm.org/wiki/Configuration_File)
