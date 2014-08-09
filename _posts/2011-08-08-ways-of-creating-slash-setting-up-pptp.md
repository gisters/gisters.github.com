---
layout: post
title: "PPTP 的使用"
description: "首页用包管理器去安装pptpclient，名称可能有所不同，用包管理器的好处是无需自己编译，依赖等一并解决"
keywords: pptp, pptpclient, 配置
category: Linux
tags: [pptp, Configuration, CLI]
---

首先用包管理器去安装pptpclient，名称可能有所不同，用包管理器的好处是无需自己编译，依赖等一并解决。

然后创建配置文件

    # pptpsetup --create vpncup --server f.vpncup.com --username havanna --password 123456 --encrypt

![pptp 1]({{ site.qiniudn }}/images/2011/08/1.png "pptp 1")

<!-- more -->
完毕后的配置文件类似于

    # cat /etc/ppp/peers/vpncup

![pptp 2]({{ site.qiniudn }}/images/2011/08/2.png "pptp 2")

然后开始拨号，第一次最好打开debug模式方便排查

    # pon vpncup debug dump logfd 2 nodetach

![pptp 3]({{ site.qiniudn }}/images/2011/08/3.png "pptp 3")

可以的话，那么以后就可以直接拨号

    # pon vpncup

![pptp 4]({{ site.qiniudn }}/images/2011/08/4.png "pptp 4")

如果连上的话，那么最后就写路由表了

    # route add default dev ppp0

![pptp 5]({{ site.qiniudn }}/images/2011/08/5.png "pptp 5")

至此，pptp 拨号成功

ifconfig 看下，多了个 ppp0

![pptp 6]({{ site.qiniudn }}/images/2011/08/6.png "pptp 6")

网页上看下，是不是地址变了

![pptp 7]({{ site.qiniudn }}/images/2011/08/7.png "pptp 7")
![pptp 8]({{ site.qiniudn }}/images/2011/08/8.png "pptp 8")

如果大家有什么好的免费的 vpn，推荐下哇
