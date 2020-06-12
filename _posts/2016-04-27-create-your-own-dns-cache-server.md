---
layout: post
title: "创建你自己的 DNS 缓存服务器"
category: Linux
tags: [DNS, Gentoo, Centos]
---

好长时间没写文章了，琢磨着写些啥。正好今天有个一个需求，需要自建 DNS 来避免污染。于是今天花了些时间，研究了下自建 DNS。也正好将一些过程与心得写下来，期间在两个发行版 ( CentOS 7、Gentoo ) 上尝试了下，正常使用。

自建 DNS 的方案可以多种搭配，不过如果项目团队内部使用的话，个人还是推荐 Dnsmasq + DNSCrypt-proxy 方案，小巧，部署方便。

- Dnsmasq:
    - 提供 DNS 缓存和 DHCP 服务功能
- DNSCrypt-proxy:
    - 在客户端与 DNS 服务端之间提供加密与认证

工具清楚了，那么开始动手

<!-- more -->

#### DNSCrypt-proxy

在 Gentoo 下，已经有了现成的包，可以直接安装。

    emerge -av net-dns/dnscrypt-proxy

配置部分，比较简单,Gentoo 的配置文件地址在 `/etc/conf.d/dnscrypt-proxy`，打开后一目了然。两种配置方法，一种配置 resolver name，一种配置 resolver ip，将你选中的那种方案取消注释，再去 `/usr/share/dnscrypt-proxy/dnscrypt-resolvers.csv` 文件中找一个 ping 值低的公开的认证服务器即可。

譬如：

    $ cat /etc/conf.d/dnscrypt-proxy
    DNSCRYPT_LOCALIP=127.0.0.1
    DNSCRYPT_LOCALPORT=5355
    DNSCRYPT_USER=dnscrypt
    DNSCRYPT_OPTIONS="--ephemeral-keys"

    DNSCRYPT_RESOLVERIP=178.216.201.222
    DNSCRYPT_RESOLVERPORT=2053
    DNSCRYPT_PROVIDER_NAME=2.dnscrypt-cert.soltysiak.com
    DNSCRYPT_PROVIDER_KEY=25C4:E188:2915:4697:8F9C:2BBD:B6A7:AFA4:01ED:A051:0508:5D53:03E7:1928:C066:8F21

可以看到，配置中前面两行，监听 127.0.0.1:5355 的请求，你可以更改，只不过，后面 dnsmasq 的配置中你也要做相应的修改。

最后就是启动了

    systemctl start dnscrypt-proxy.service
    systemctl enable dnscrypt-proxy.service

#### Dnsmasq

Gentoo 与 CentOS 都提供现成的，可以直接安装：

Gentoo

    USE="dnssec auth-dns" emerge -av net-dns/dnsmasq

CentOS

    yum install dnsmasq

关于配置比较简单，1个目录，3个配置文件

- 文件：/etc/dnsmasq.conf
- 文件：/etc/dnsmasq.resolv.conf
- 文件：/etc/dnsmasq.hosts
- 目录：/etc/dnsmasq.d/

没有的话则创建之，依次说明

    dnssec
    conf-file=/usr/share/dnsmasq/trust-anchors.conf
    no-resolv
    resolv-file=/etc/dnsmasq.reslolv.conf
    no-poll
    server=127.0.0.1#5355           # 上文中，dnscrypt-proxy 监听的 ip:port
    listen-address=127.0.0.1        # 如果允许外网，则填写公网 ip，对内网提供，则填写内网 ip 地址，中间用逗号隔开
    no-hosts                        # 不启用 /etc/hosts
    addn-hosts=/etc/dnsmasq.hosts   # 启用自己的 hosts 配置
    conf-dir=/etc/dnsmasq.d/,*.conf # 引入 /etc/dnsmasq.d/ 下所有的以 conf 结尾的配置文件

需要说明的是，CentOS 下，可能是版本过低，也可能是打包者没有开启某些开关，导致 dnssec 无法使用，所以，在 CentOS 下，需要注释两行

    # dnssec
    # conf-file=/usr/share/dnsmasq/trust-anchors.conf

OK，大体就这么些，再在目录 `/etc/dnsmasq.d` 下创建个个 personal.conf，写入自己的一些特殊配置，获取着去网上找人家的特殊配置都可。

#### 参考：

- <https://wiki.gentoo.org/wiki/Dnsmasq>
- <https://wiki.archlinux.org/index.php/dnsmasq>
- <https://wiki.installgentoo.com/index.php/DNSCrypt>
- <https://dnscrypt.org/#dnscrypt-proxy>
- <http://www.thekelleys.org.uk/dnsmasq/doc.html>
