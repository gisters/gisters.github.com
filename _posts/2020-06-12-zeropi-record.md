---
layout: post
title: "ZeroPi 的一些记录"
category: Linux
tags: [arm, Linux]
---

![ZeroPi](/cdn/images/2020/06/zeropi.png)

购入 ZeroPi 半年多，吃灰居多，偶尔拿来构建 Docker 镜像，偶尔编译一些小工具，跑一些小测试等等，用处很单一。为防止记忆遗忘，记录一些备查。

ZeroPi 是友善出的一款单片机，对于没有接触过单片机的同学，它其实是一个比较好的上手玩具。当然，没有显示接口对于接触过 Linux 的同学来说，应该也没有什么问题。

<!-- more -->

Armbian 的安装没有什么难处，网上资料太多，这里只记录一些不是大众化的资料，其他的单片机使用 Armbian 的话也可以参考。

#### UART

Debug 的 UART 接驳好后，可以直接用 MacOS/Linux 的 screen 工具进入，针对 ZeroPi 的 UART 波特率为 115200bps。

    screen /dev/tty.usbserial-XXXXXXXX 115200

串行端口号可以直接用 tab 补全。

#### 网络

Armbian 默认是启用 Network Manager 来管理网络，armbian-config 中的网络配置也是使用此工具，不过看了下 ZeroPi 的硬件，本着能省则省的态度，我还是直接使用自带的网络工具去配置了，编辑 `/etc/network/interfaces`。

    source /etc/network/interfaces.d/*
    # Network is managed by Network manager
    auto lo
    iface lo inet loopback

    allow-hotplug eth0
    iface eth0 inet dhcp
      hwaddress ether 12:23:34:45:56:67

不要自动配置 nameserver

    dpkg-reconfigure resolvconf         # answer no

编辑 `/etc/resolvconf/resolv.conf.d/head`

    nameserver your-dns

最后更新 nameserver

    resolvconf -u

我有一个 N 年前购入的小米无线网卡，所以我增加了无线部分的设置，便于在断开网线的时候，也可以接入网络。

    iface wlxfc0123456789 inet dhcp
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

其中 `/etc/wpa_supplicant/wpa_supplicant.conf` 的配置可以使用如下命令写入

    wpa_passphrase your-ssid your-password >> \
        /etc/wpa_supplicant/wpa_supplicant.conf

#### udev

总感觉 led 的颜色怪怪的，后来发现，因为是红色灯常亮，改一下，让蓝色灯常亮，有数据读写的时候红灯闪。这里我使用 udev 来实现。

led 的接入并被挂载为 `/dev/uleds`：

    udevadm info -a -p `udevadm info -q path -n /dev/uleds`
    ......
      looking at device '//devices/virtual/misc/uleds':
        KERNEL=="uleds"
        SUBSYSTEM=="misc"
        DRIVER==""
    ......

这里获取到我们的led设备使用 `KERNEL=="uleds"` 与 `SUBSYSTEM=="misc"`，触发的控制一般在 `/sys/class/leds`中。

创建一个文件 `/etc/udev/rules.d/90-power-leds.rules` 后写入：

    KERNEL=="uleds", SUBSYSTEM=="misc", PROGRAM="/bin/sh -c '\
        echo mmc0 > /sys/class/leds/nanopi\:green\:pwr/trigger && \
        echo default-on > /sys/class/leds/nanopi\:blue\:status/trigger'"

你没看错，Armbian 适配的时候，名字与颜色都懒得改...不过不影响效果...

手动测试下是否有错误

    udevadm test /sys/class/leds/nanopi\:blue\:status
    udevadm test /sys/class/leds/nanopi\:green\:status

重载下规则

    udevadm control --reload

#### 蓝牙适配器

我也时而插一个蓝牙适配器到 USB 口上，使用 Filco 键盘打字。因为没有 GUI，所以直接 ssh 终端去做一些配置，过程如下：

    $ bluetooth
    [bluetooth]# power on                   # 激活蓝牙
    [bluetooth]# agent KeyboardOnly
    [bluetooth]# default-agent
    [bluetooth]# pairable on                # 配对模式
    [bluetooth]# scan on                    # 扫描
    [bluetooth]# pair 11:22:33:44:55:66     # 配对键盘蓝牙地址
    [bluetooth]# trust 11:22:33:44:55:66    # 键盘设备为可信
    [bluetooth]# connect 11:22:33:44:55:66  # 进行连接

配置的时候，你需要另外一个键盘......

#### tmpfs

不知为何，Debian 默认没有将 `/run/user/nums` 挂载为 tmpfs，考虑了下 MicroSD 的速度，一些东西还是尽量放内存吧。

    apt install libpam-systemd          # 嗯，装这个包，重启即可

另外，`/tmp` 默认也没有挂载为 tmpfs，修改下

    ln -sf /usr/share/systemd/tmp.mount /etc/systemd/system/
    systemctl enable tmp.mount
    systemctl start tmp.mount

#### iptables

一般单片机内存都小的可怜，firewalld 尽管好用，内存占用也不小，本着能省即省的态度，还是用 iptables 吧

    apt install iptables

写入 iptables 规则到文件 `/etc/iptables.rule`

    *filter
    :INPUT DROP [0:0]
    :FORWARD DROP [0:0]
    :OUTPUT ACCEPT [0:0]
    -A INPUT -p icmp -m icmp --icmp-type echo-request -j ACCEPT
    -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
    -A INPUT -i lo -j ACCEPT
    -A INPUT -p tcp -m conntrack --ctstate NEW -m tcp --dport 22 -j ACCEPT
    -A INPUT -m conntrack --ctstate INVALID -j DROP
    -A INPUT -j REJECT --reject-with icmp-host-prohibited
    -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
    -A FORWARD -m conntrack --ctstate INVALID -j DROP
    -A FORWARD -j REJECT --reject-with icmp-host-prohibited
    COMMIT

载入规则

    iptables-restore < /etc/iptables.rule

安装 iptables-persistent 使之开启自启动

    apt install iptables-persistent

#### nftables

也可以直接使用 nftable，写入配置 /etc/nftabls.conf

    #!/sbin/nft -f
    flush ruleset

    table inet filter {
        chain input {
            type filter hook input priority 0; policy drop;
            ct state invalid counter drop
            ct state { established, related } counter accept
            iif lo accept

            ip protocol icmp icmp type { destination-unreachable, echo-request, router-solicitation, router-advertisement, time-exceeded, parameter-problem } counter accept

            ct state new tcp dport { ssh } counter accept

            counter reject with icmp type host-prohibited
        }

        chain forward {
            type filter hook forward priority 0; policy drop;
            ct state invalid counter drop
            ct state { established, related } counter accept
            counter reject with icmp type host-prohibited
        }

        chain output {
            type filter hook output priority 0; counter; policy accept;
        }
    }

载入规则

    nft -f /etc/nftables.conf

#### swap

单片机基本不是 mmc 就是 tf，读写速度堪忧，在上面挂载 swap 性能极低，虽然 debian 自带了 zram-tools 工具去创建 zram 设备，但是 armbian 也自己维护了一个工具，默认就安装好，查看下

    zramctl
    free -h

本质是内存中动态划出一个空间用做 swap，性能一下子就上去了，如此，可以大胆的如下操作了，顺便将 bbr 也打开吧

    /etc/sysctl.d/custom.conf
    vm.swappiness = 100
    net.core.default_qdisc = fq
    net.ipv4.tcp_congestion_control = bbr
    #载入
    sysctl -p /etc/sysctl.d/custom.conf

#### docker-compose

docker 已经是必不可少了，官方文档就有安装步骤，但是并没有提供 docker-compose 的 arm 二进制文件，只能安装 debian 维护的版本，但是这个版本有一个问题，导致 docker login 失败。

所以还是直接搞一个 arm 的二进制单文件版本吧

    docker run --rm -v /usr/local/bin:/dist \
        gists/docker-compose-bin:latest
    docker rmi gists/docker-compose-bin:latest
    docker-compose -v

参考资料：

- <http://wiki.friendlyarm.com/wiki/index.php/ZeroPi>
- <https://docs.armbian.com>
