---
layout: post
title: "服务端 SSL 配置"
category: Linux
tags: [SSL, Apache, Nginx, Lighttpd]
---

快要步入全民 SSL 时代了，有必要记录下 SSL 的相关配置了。

SSL cipher 依据你申请的证书来，譬如：WoSign 的免费证书是 AES128 加密的，则 Nginx 可以：

    ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES128+EECDH:AES128+EDH";

而 SSL 的协议则推荐使用 **TLSv1 TLSv1.1 TLSv1.2**，Nginx 下如此：

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

<!-- more -->
Forward Secrecy 也加上，终端运行：

    openssl dhparam -out dhparam.pem 4096

随后在 nginx 配置中加上：

    ssl_dhparam /your/path/dhparam.pem;

解释下 Forward Secrecy，客户端服务端协商一个永不重用密钥，在会话结束时销毁它。

##### Apache、Nginx、Lighttpd 各配置

###### Apache

    SSLCipherSuite EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH
    SSLProtocol All -SSLv2 -SSLv3
    SSLHonorCipherOrder On
    SSLSessionTickets Off
    Header always set Strict-Transport-Security "max-age=63072000; includeSubdomains; preload"
    Header always set X-Frame-Options DENY
    Header always set X-Content-Type-Options nosniff
    # Requires Apache >= 2.4
    SSLCompression off
    SSLUseStapling on
    SSLStaplingCache "shmcb:logs/stapling-cache(150000)"

###### Nginx

    ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";  # HSTS
    add_header X-Frame-Options DENY;
    add_header X-Content-Type-Options nosniff;
    ssl_session_tickets off; # nginx >= 1.5.9
    ssl_dhparam /your/path/dhparam.pem;   # Forward Secrecy
    ssl_stapling on; # nginx >= 1.3.7
    ssl_stapling_verify on; # nginx => 1.3.7
    resolver 8.8.8.8 8.8.4.4 valid=300s;
    resolver_timeout 5s;

###### Lighttpd

    ssl.honor-cipher-order = "enable"
    ssl.cipher-list = "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH"
    ssl.use-compression = "disable"
    setenv.add-response-header = (
        "Strict-Transport-Security" => "max-age=63072000; includeSubdomains; preload",
        "X-Frame-Options" => "DENY",
        "X-Content-Type-Options" => "nosniff"
    )
    ssl.use-sslv2 = "disable"
    ssl.use-sslv3 = "disable"

我使用的是 Nginx，在以上 Nginx 中加入如下即完成配置：

    ssl_certificate /your/path/name.crt;
    ssl_certificate_key /your/path/name.key;

最后，你可以去 [SSL Labs](https://www.ssllabs.com/ssltest/) 跑下域名，过段时间回来看看你的 SSL Server 得分。

![SSL LABS](/cdn/images/2015/06/SSL-LABS.png)

参考：

- <https://cipherli.st/>
