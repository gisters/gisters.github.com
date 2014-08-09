---
layout: post
title: "KVM 端口转发"
description: "kvm 端口转发"
keywords: kvm, 端口, 转发
category: Linux
tags: [KVM, Virtual]
---


    hostfwd=[tcp|udp]:[hostaddr]:hostport-[guestaddr]:guestport

将进入到主机端口的 TCP 或者 UDP 连接 转发到客户机的某个地址和端口

如果没有指定 "guestaddr", 那么默认转到 10.0.2.15

通过指定 "hostaddr"，可以绑定到主机某个特点的接口上

<!-- more -->
例如

    -net user,hostfwd=tcp:127.0.0.1:6001-10.0.2.15:6000

将主机 127.0.0.1 的 6001 端口, 转发到客户机 10.0.2.15 的 6000 端口 (注意不要忘记 `–` 号)

也可以简写成

    -net user,hostfwd=tcp::6001-:6000

将主机的 5555 转发到客户机的 23 端口

注意: 不要将新的 `hostfwd` 与 `-tftp -bootp -smb -redir` 选项同时使用, 混用会导致未知的结果

但只单独使用旧选项，目前依然是有效的。但不建议用户在新应用中使用，未来的版本可能删除这些选项

注意：使用 `-net user` 必须同 `-net nic` 配合

    -net nic -net user

示范

    kvm -enable-kvm -ctrl-grab -cpu core2duo -m 512 -smp 1 \
    -vga std -localtime -usb -usbdevice tablet -soundhw hda -daemonize \
    -drive file=gentoo.img,cache=writeback,media=disk \
    -net nic,model=virtio \
    -net user,hostfwd=tcp::1080-:80,\
    hostfwd=tcp::2200-:22,\
    hostfwd=tcp::2100-:21

注意：转发 HOST 机小于等于 1024 的端口，需要是用 root 权限
