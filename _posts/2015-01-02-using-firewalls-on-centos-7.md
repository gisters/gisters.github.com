---
layout: post
title: "CentOS 7 下使用 Firewall"
category: Linux
tags: [Firewall, Usage]
---

在 CentOS 7 中，引入了一个新的服务，**Firewalld**，下面一张图，让大家明确的了解 Firewall 与 iptables 之间的关系与区别。

![firewall stack](//cdn.09hd.com/images/2015/01/firewall_stack.png)

<!-- more -->

安装它，只需

    # yum install firewalld

如果需要图形界面的话，则再安装

    # yum install firewall-config

#### 一、介绍

防火墙守护 firewalld 服务引入了一个信任级别的概念来管理与之相关联的连接与接口。它支持 ipv4 与 ipv6，并支持网桥，采用 firewall-cmd (command) 或 firewall-config (gui) 来动态的管理 kernel netfilter 的临时或永久的接口规则，并实时生效而无需重启服务。

Firewall 能将不同的网络连接归类到不同的信任级别，Zone 提供了以下几个级别

- drop: 丢弃所有进入的包，而不给出任何响应
- block: 拒绝所有外部发起的连接，允许内部发起的连接
- public: 允许指定的进入连接
- external: 同上，对伪装的进入连接，一般用于路由转发
- dmz: 允许受限制的进入连接
- work: 允许受信任的计算机被限制的进入连接，类似 workgroup
- home: 同上，类似 homegroup
- internal: 同上，范围针对所有互联网用户
- trusted: 信任所有连接

#### 二、使用方法

    # systemctl start firewalld         # 启动,
    # systemctl enable firewalld        # 开机启动
    # systemctl stop firewalld          # 关闭
    # systemctl disable firewalld       # 取消开机启动

具体的规则管理，可以使用 `firewall-cmd`，具体的使用方法可以

    $ firewall-cmd --help

##### 1. 查看规则

查看运行状态

    $ firewall-cmd --state

查看已被激活的 Zone 信息

    $ firewall-cmd --get-active-zones
    public
      interfaces: eth0 eth1

查看指定接口的 Zone 信息

    $ firewall-cmd --get-zone-of-interface=eth0
    public

查看指定级别的接口

    $ firewall-cmd --zone=public --list-interfaces
    eth0

查看指定级别的所有信息，譬如 public

    $ firewall-cmd --zone=public --list-all
    public (default, active)
      interfaces: eth0
      sources:
      services: dhcpv6-client http ssh
      ports:
      masquerade: no
      forward-ports:
      icmp-blocks:
      rich rules:

查看所有级别被允许的信息

    $ firewall-cmd --get-service

查看重启后所有 Zones 级别中被允许的服务，即永久放行的服务

    $ firewall-cmd --get-service --permanent

##### 2. 管理规则

    # firewall-cmd --panic-on           # 丢弃
    # firewall-cmd --panic-off          # 取消丢弃
    # firewall-cmd --query-panic        # 查看丢弃状态
    # firewall-cmd --reload             # 更新规则，不重启服务
    # firewall-cmd --complete-reload    # 更新规则，重启服务

添加某接口至某信任等级，譬如添加 eth0 至 public，再永久生效

    # firewall-cmd --zone=public --add-interface=eth0 --permanent

设置 public 为默认的信任级别

    # firewall-cmd --set-default-zone=public

###### a. 管理端口

列出 dmz 级别的被允许的进入端口

    # firewall-cmd --zone=dmz --list-ports

允许 tcp 端口 8080 至 dmz 级别

    # firewall-cmd --zone=dmz --add-port=8080/tcp

允许某范围的 udp 端口至 public 级别，并永久生效

    # firewall-cmd --zone=public --add-port=5060-5059/udp --permanent

###### b. 管理服务

添加 smtp 服务至 work zone

    # firewall-cmd --zone=work --add-service=smtp

移除 work zone 中的 smtp 服务

    # firewall-cmd --zone=work --remove-service=smtp

###### c. 配置 ip 地址伪装

查看

    # firewall-cmd --zone=external --query-masquerade

打开伪装

    # firewall-cmd --zone=external --add-masquerade

关闭伪装

    # firewall-cmd --zone=external --remove-masquerade

###### d. 端口转发

要打开端口转发，则需要先

    # firewall-cmd --zone=external --add-masquerade

然后转发 tcp 22 端口至 3753

    # firewall-cmd --zone=external --add-forward-port=port=22:proto=tcp:toport=3753

转发 22 端口数据至另一个 ip 的相同端口上

    # firewall-cmd --zone=external --add-forward-port=port=22:proto=tcp:toaddr=192.168.1.100

转发 22 端口数据至另一 ip 的 2055 端口上

    # firewall-cmd --zone=external --add-forward-port=port=22:proto=tcp:toport=2055:toaddr=192.168.1.100

###### d. IP 封禁

    # firewall-cmd --permanent --add-rich-rule="rule family='ipv4' source address='222.222.222.222' reject"

以上都是一些常用方法，更多高级方法，请参考：

- <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Security_Guide/sec-Using_Firewalls.html>
- <https://fedoraproject.org/wiki/FirewallD>
