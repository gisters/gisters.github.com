---
layout: post
title: "CentOS 7 配置记录"
category: Linux
tags: [Nginx, PHP, PHP-FPM, SQL, Tips]
---

几个 vps 逐步的升级到 CentOS 7，老是忘记一些配置，于是动手纪录下。

#### tools

    # yum update
    # yum install wget unzip bzip2 vim

#### hostname

    # hostname yourdomain
    # hostnamectl set-hostname yourdomain

<!-- more -->
#### Timezone

    # timedatectl set-timezone Asia/Shanghai

查看时区

    # timedatectl

#### adduser

不喜 root ssh，于是添加一个用户，因为用得到 sudo，故添加到 wheel 组

    # useradd -m -G users,wheel havanna
    # passwd havanna

#### firewall

    # systemctl start firewalld
    # systemctl enable firewalld

具体的方法可以阅读：[CentOS 7 下使用 Firewall]({% post_url 2015-01-02-using-firewalls-on-centos-7 %})，这里只添加基本的对外服务

    # firewall-cmd --permanent --zone=public --add-service=http
    # firewall-cmd --permanent --zone=public --add-service=https
    # firewall-cmd --reload

确认下

    # firewall-cmd --permanent --zone=public --list-services

#### ssh

调整 sshd 服务，采用 key 登陆，编辑 **/etc/ssh/sshd_config**

    ......
    Protocol 2
    PasswordAuthentication no
    ChallengeResponseAuthentication no
    PermitRootLogin no
    Port yourport
    AllowUsers havanna

至 **/home/havanna**

    # mkdir .ssh
    # chown havanna:havanna -R .ssh
    # chmod 700 .ssh

上传你的 **pub key** 至 **/home/havanna/.ssh** 目录，改名为 **authorized_keys** 并修改权限

    # chown havanna:havanna /home/havanna/authorized_keys
    # chmod 600 /home/havanna/authorized_keys

因为更改了默认的 22 端口，安装一个 selinux 工具

    # yum install policycoreutils-python
    # semanage port -a -t ssh_port_t -p tcp yourport
    # firewall-cmd --zone=public --permanent --add-port=yourport/tcp
    # firewall-cmd --zone=public --permanent --remove-service=ssh
    # firewall-cmd --reload

然后重启下 sshd 服务

    # systemctl restart sshd

确认下 sshd 服务

    # ss -tnlp | grep sshd

#### Nginx

    # yum install epel-release
    # yum install nginx
    # semanage permissive -a httpd_t

编辑 **/etc/nginx/nginx.conf**

```nginx
user  nginx nginx;
worker_processes  1;

worker_rlimit_nofile 102400;

pid        /var/run/nginx.pid;

events {
    worker_connections  102400;
    use epoll;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    include conf.d/*.conf;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"'
                      '"$gzip_ratio"';

    access_log  /var/log/nginx/access.log  main;
    error_log  /var/log/nginx/error.log  debug;

    server_names_hash_bucket_size 128;
    client_header_buffer_size 16k;
    large_client_header_buffers 4 16k;
    client_header_timeout 10m;
    client_body_timeout 10m;
    client_max_body_size 10m;
    send_timeout 10m;
    connection_pool_size 256;
    request_pool_size 4k;

    sendfile       on;
    tcp_nopush     on;
    tcp_nodelay    on;
    keepalive_timeout  75 20;

    fastcgi_connect_timeout 1800;
    fastcgi_send_timeout 1800;
    fastcgi_read_timeout 1800;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;

    gzip  on;
    gzip_min_length  1k;
    gzip_buffers     4 8k;
    gzip_http_version 1.1;
    gzip_comp_level 6;
    gzip_proxied any;
    gzip_types       text/xml text/css text/javascript text/plain application/json \
        application/x-javascript application/xml application/xml+rss;
    gzip_vary on;
    gzip_disable     "MSIE [1-6]\.";

    output_buffers 1 32k;
    postpone_output 1460;
    ignore_invalid_headers on;
    index index.html index.htm index.php;

    server_tokens off;
}
```

创建 **/etc/nginx/conf.d/**

    # mkdir /etc/nginx/conf.d
    # touch /etc/nginx/conf.d/localhost.conf

编辑 **localhost.conf**

```nginx
server {
    listen       80;
    server_name  localhost;
    root         /var/www/localhost;
    charset UTF-8;

    access_log  /var/log/nginx/localhost.access.log;
    error_log  /var/log/nginx/localhost.error.log;

    location ~ .*\.php$ {
        fastcgi_pass   unix:/run/php-fpm/php-fpm.sock;
        fastcgi_index  index.php;
        fastcgi_param SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }

    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|ico)$ {
        expires      30d;
        access_log   off;
    }

    location ~ .*\.(js|css|htm|html)$ {
        expires      12h;
        access_log   off;
    }

    #error_page  404              /404.html;

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location ~ /\.ht {
        deny  all;
    }
}
```

启动

    # systemctl start nginx
    # systemctl enable nginx

#### php-fpm

    # yum install php-fpm php-gd php-mbstring php-xml php-mysql

如需要 **php-mcrypt**、**php-pecl-apcu** 则需要 epel 源

    # yum install epel-release
    # yum install php-mcrypt php-pecl-apcu

编辑 **/etc/php-fpm.d/www.conf**

    ......
    listen = /run/php-fpm/php-fpm.sock
    ......
    user = nginx
    group = nginx
    ......

启动

    # systemctl start php-fpm
    # systemctl enable php-fpm

#### MariaDB

    # yum install mariadb-server
    # systemctl start mariadb

初始化下，配置 MariaDB 的 root 密码

```
# mysql_secure_installation
/bin/mysql_secure_installation: line 379: find_mysql_client: command not found

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

源里的版本较老，可以安装上游最新版本，创建如下文件并编辑

    # touch /etc/yum.repos.d/MariaDB.repo

编辑 **/etc/yum.repos.d/MariaDB.repo**

    [Mariadb]
    name = MariaDB
    baseurl = http://yum.mariadb.org/10.1/centos7-amd64
    gpgkey = https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
    gpgcheck = 1

随后安装

    # yum install MariaDB-server

启动

    # systemctl start mariadb
    # systemctl enable mariadb

