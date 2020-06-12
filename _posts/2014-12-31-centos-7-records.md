---
layout: post
title: "CentOS 7 配置记录"
category: Linux
tags: [Nginx, PHP, PHP-FPM, SQL, Tips, CentOS]
---

几个 vps 逐步的升级到 CentOS 7，老是忘记一些配置，于是动手纪录下。首先当然是更新到最新的包


    # yum update

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

    # useradd -m -G users,wheel test
    # passwd test

#### firewall

    # systemctl start firewalld
    # systemctl enable firewalld

具体的方法可以阅读：[CentOS 7 下使用 Firewall]({% post_url 2015-01-02-using-firewalls-on-centos-7 %})，这里只添加基本的对外服务

    # firewall-cmd --permanent --zone=public --add-service=http
    # firewall-cmd --permanent --zone=public --add-service=https
    # firewall-cmd --reload

确认下

    # firewall-cmd --permanent --zone=public --list-services

#### iptables

或者，你可能仍然习惯使用 iptables，则

    # yum install iptables-services iptables

规则文件 `/etc/sysconfig/iptables`，写入内容

    *filter
    :INPUT DROP [0:0]
    :FORWARD DROP [0:0]
    :OUTPUT ACCEPT [0:0]
    -A INPUT -p icmp -m icmp --icmp-type echo-request -j ACCEPT
    -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
    -A INPUT -i lo -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 28480 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 2377 -j ACCEPT
    -A INPUT -p udp -m state --state NEW -m udp --dport 4789 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 7946 -j ACCEPT
    -A INPUT -p udp -m state --state NEW -m udp --dport 7946 -j ACCEPT
    -A INPUT -p esp -j ACCEPT
    -A INPUT -m conntrack --ctstate INVALID -j DROP
    -A INPUT -j REJECT --reject-with icmp-host-prohibited
    -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
    -A FORWARD -m conntrack --ctstate INVALID -j DROP
    -A FORWARD -j REJECT --reject-with icmp-host-prohibited
    COMMIT

随后导入规则，再启用服务

    # iptables-restore < /etc/sysconfig/iptables
    # systemctl enable iptables && systemctl start iptables

#### ssh

调整 sshd 服务，采用 key 登陆，编辑 **/etc/ssh/sshd_config**

    ......
    AcceptEnv LANG LC_*

    Subsystem	sftp	/usr/lib/openssh/sftp-server

    Protocol 2
    UsePAM yes
    UseDNS no
    HostKey /etc/ssh/ssh_host_rsa_key
    HostKey /etc/ssh/ssh_host_ecdsa_key
    HostKey /etc/ssh/ssh_host_ed25519_key
    AddressFamily inet
    PermitRootLogin no
    PasswordAuthentication no
    SyslogFacility AUTHPRIV
    PubkeyAuthentication yes
    ChallengeResponseAuthentication no
    ClientAliveInterval 60
    Port port-num
    Match User user-name
        X11Forwarding no
        PermitTTY yes

至 **/home/test**

    # mkdir .ssh
    # chown test:test -R .ssh
    # chmod 700 .ssh

上传你的 **pub key** 至 **/home/test/.ssh** 目录，改名为 **authorized_keys** 并修改权限

    # chown test:test /home/test/authorized_keys
    # chmod 600 /home/test/authorized_keys

因为更改了默认的 22 端口，安装一个 selinux 工具

    # yum install policycoreutils-python
    # semanage port -a -t ssh_port_t -p tcp yourport
    # firewall-cmd --zone=public --permanent --add-port=yourport/tcp
    # firewall-cmd --zone=public --permanent --remove-service=ssh
    # firewall-cmd --reload

然后重启下 sshd 服务

    # systemctl restart sshd

确认下 sshd 服务

    # ss -alnptu | grep sshd

#### tmpfs

内存足够的话，直接将 tmpfs 挂载至 /tmp，先检查下

    # systemctl is-enabled tmp.mount

如果没有，需要手动操作下

    # systemctl enable tmp.mount && systemctl start tmp.mount

个别主机上采用的模版镜像，可能默认 mask 了服务，你可能需要先 unmask

    # systemctl unmask tmp.mount && systemctl enable tmp.mount && systemctl start tmp.mount

当然，我们也可以使用传统的方式来挂载，譬如在 /etc/fstab 中指定

    tmpfs   /tmpfs  tmpfs   size=512m   0 0

如果想要调整当前 tmpfs 的大小，可以如此操作

    # mount -o remount,size=N /tmp

#### swap

小内存，是需要 swap 的，而我习惯不论 8G 以内的内存，创建 swapfile

    # fallocate -l 64M /swap
    or
    # dd if=/dev/zero of=/swap bs=1 coun=0 seek=64M         # seek 方式来创建稀疏文件
    # chmod 600 /swap
    # mkswap /swap
    # swapon /swap

临时卸载 swap

    # swapoff /swap
    or
    # swapoff -a

最后，写入 fstab

    /swap	none	swap	sw	0 0

性能优化方面，其实我们还是希望系统尽量使用物理内存，而不要有限使用 swap 交换文件，swappiness 参数代表内核对于交换空间的喜好，值越小代表越减少内存的交换，从而提升一些响应速度。编辑 /etc/sysctl.conf

    vm.swappiness=1
    vm.vfs_cache_pressure=50

#### zram

swap 频繁交换会极大的影响主机性能，现在一般对小内存主机，使用 zram，不过 CentOS 7 并没有提供管理脚本，自己创建一个，以来 `bc`

    # yum install bc
    # touch /etc/init.d/zramswap
    # chmod +x /etc/init.d/zramswap

写入如下内容

```
#!/bin/bash
### BEGIN INIT INFO
# Provides: zram
# Required-Start:
# Required-Stop:
# Default-Start: 2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: Virtual Swap Compressed in RAM
# Description: Virtual Swap Compressed in RAM
### END INIT INFO

start() {
    # get the number of CPUs
    num_cpus=$(grep -c processor /proc/cpuinfo)
    # if something goes wrong, assume we have 1
    [ "$num_cpus" != 0 ] || num_cpus=1

    # set decremented number of CPUs
    decr_num_cpus=$((num_cpus - 1))

    # get the amount of memory in the machine
    mem_total_kb=$(grep MemTotal /proc/meminfo | grep -E --only-matching '[[:digit:]]+')

    #we will only assign 50% of system memory to zram
    mem_total_kb=$((mem_total_kb / 2))

    mem_total=$((mem_total_kb * 1024))

    # load dependency modules
    modprobe zram num_devices=$num_cpus

    # initialize the devices
    for i in $(seq 0 $decr_num_cpus); do
    echo $((mem_total / num_cpus)) > /sys/block/zram$i/disksize
    done

    # Creating swap filesystems
    for i in $(seq 0 $decr_num_cpus); do
    mkswap /dev/zram$i
    done

    # Switch the swaps on
    for i in $(seq 0 $decr_num_cpus); do
    swapon -p 100 /dev/zram$i
    done
}

stop() {
    for i in $(grep '^/dev/zram' /proc/swaps | awk '{ print $1 }'); do
        swapoff "$i"
    done

    if grep -q "^zram " /proc/modules; then
        sleep 1
        rmmod zram
    fi
}

status() {
        ls /sys/block/zram* > /dev/null 2>&1 || exit 0
        echo -e "-------\nzram Compression Stats:\n-------"
        for i in /sys/block/zram*; do
            compr=$(< $i/compr_data_size)
        orig=$(< $i/orig_data_size)
        ratio=0
        if [ $compr -gt 0 ]; then
            ratio=$(echo "scale=2; $orig*100/$compr" | bc -q)
        fi
        echo -e "/dev/${i/*\/}:\t$ratio% ($orig -> $compr)"
        done
        echo -e "-------\nSWAP Stats:\n-------"
        swapon -s | grep zram
        echo -e "-------\nMemory Stats:\n-------"
        free -m -l -t
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    restart)
        stop
        sleep 3
        start
        ;;
    status)
        status
        ;;
    *)
        echo "Usage: $0 {start|stop|restart|status}"
        RETVAL=1
esac
```

最后

    # systemctl enable zramswap
    # systemctl start zramswap
    # free -h

#### journal

是个好东西，接管日志，统一的格式，但是呢，时间长了，几个 GB 的日志还是挺占用空间的，需要配置下

手动清理日志

    # journalctl --vacuum-time=1d
    # journalctl --vacuum-size=100M

写入配置 **/etc/systemd/journald.conf**

    SystemMaxUse=100M

#### ulimits on systemd

编辑 /etc/systemd/system.conf 或 /etc/systemd/user.conf，依据 unit 文件在哪个目录。

    DefaultLimitNOFILE=51200
    DefaultLimitNPROC=51200

