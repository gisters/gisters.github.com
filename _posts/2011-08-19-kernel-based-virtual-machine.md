---
layout: post
title: "KVM 内核虚拟化"
description: "KVM(Kernel-based Virtual Machine) 是一个全虚拟化的解决方案，它自 2.6.20 以后正式成为 Linux 内核的一部分，也就是说，它是 Linux 内核官方支持的一个虚拟化方案"
keywords: kvm, 内核, 虚拟化
category: Linux
tags: [KVM, Kernel, Virtual]
---

![kvm-xp]({{ site.qiniudn }}/images/2011/08/kvm-xp.png "kvm-xp")

KVM(Kernel-based Virtual Machine) 是一个全虚拟化的解决方案，它自 2.6.20 以后正式成为 Linux 内核的一部分，也就是说，它是 Linux 内核官方支持的一个虚拟化方案。KVM 的硬件需求是 CPU 必须支持虚拟化，对于 Intel CPU 来说是 Intel VT，AMD CPU 则是 AMD-V。

视频是我本地的 kvm guest 中的 winxp ，用的 rdesktop 连接

如何确定你的CPU支持硬件虚拟化呢？

    egrep '(vmx|svm)' /proc/cpuinfo

<!-- more -->
vmx 是Intel CPU 的，svm 则是 AMD CPU 的。我的 CPU 返回如下：

    flags : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc aperfmperf pni dtes64 monitor ds_cpl vmx est tm2 ssse3 cx16 xtpr pdcm sse4_1 sse4_2 popcnt lahf_lm arat dts tpr_shadow vnmi flexpriority ept vpid
    flags : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc aperfmperf pni dtes64 monitor ds_cpl vmx est tm2 ssse3 cx16 xtpr pdcm sse4_1 sse4_2 popcnt lahf_lm arat dts tpr_shadow vnmi flexpriority ept vpid
    flags : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc aperfmperf pni dtes64 monitor ds_cpl vmx est tm2 ssse3 cx16 xtpr pdcm sse4_1 sse4_2 popcnt lahf_lm arat dts tpr_shadow vnmi flexpriority ept vpid
    flags : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc aperfmperf pni dtes64 monitor ds_cpl vmx est tm2 ssse3 cx16 xtpr pdcm sse4_1 sse4_2 popcnt lahf_lm arat dts tpr_shadow vnmi flexpriority ept vpid

在 linux 下面需要安装下面的软件（各发行版命名差不错）： `kvm qemu-kvm`

加入个人用户到 kvm 组，以保证个人用户可以读写 `/dev/kvm`，否则，以个人用户无法运行 kvm：

    sudo adduser $USER kvm

如果想使用物理磁盘，则需要加个人用户到 disk 组：

    sudo adduser $USER disk

现在最好重启一下计算机！

下面开始安装操作系统，下面以 Windows XP 为例开始安装。

先准备好一个磁盘文件，有2种常用的格式，一个是qemu的qcow2格式，一个是raw格式，前者是可以增长的文件格式，后者实际是磁盘上一个连续区域。

qcow2:

    qemu-img create -f qcow2 /path/to/winxp.img 4G

Raw：

    dd if=/dev/zero of=/path/to/winxp.img bs=1024K count=4000

![kvm-gentoo]({{ site.qiniudn }}/images/2011/08/kvm-gentoo.png "kvm-gentoo")

- qemu-img： qemu 磁盘文件程序,主要用来创建、检查、转换等。
- creat： 创建一个新的磁盘文件。
- -f： 指定磁盘文件格式类型。
- qcow2： 这个是 qemu 的镜像格式,用的最普遍的格式。使用他可以有较小的镜像文件,且如果你的虚拟机系统不支持 holes 的话,如 fat32，那么使用这个格式还是有用的,另外它还支持可选的 AES 加密,基于 Zlib 的压缩和支持多种 VM 的快照。
- raw： raw 磁盘镜像格式,这个格式的优势在于可以简单容易的输出到任意其他的模拟器,如果你的文件系统支持 holes,那么被写过的扇区将会预留一些空间。
- qcow：旧版的 qemu 镜像格式。
- cow： user-mode linux copy on write 镜像格式,曾经是 qemu 唯一的可扩展的镜像格式,仅能与之前的版本兼容,且不能工作在 windows 上。
- vmdk： vmware 的镜像格式。
- cloop： linux coppressed loop 镜像,只能使用在完全压缩的 CD-ROW 镜像上。
- filename： 你想要的镜像文件名，为了方便识别用虚拟机系统标识,并加上 img 的后缀。
- size： 所创建的镜像文件的大小。

下面开始安装，准备好安装光盘文件 Deepin-Lite-XP.iso，下面是安装的指令：

    kvm -m 512 -smp 1 -vga std -localtime -boot d \
    -cdrom /path/to/Deepin-Lite-XP.iso -drive file=/path/to/winxp.img,cache=writeback

- -m： 说明虚拟机使用的内存；
- -smp： 表明只使用1个cpu核。
- -boot： 参数说明虚拟机启动方式，c为硬盘启动，d为光驱启动。
- -M： 选择模拟的及其的类型。pc-0.14是默认的，具体可以使用kvm -M ?进行查看。
- -cpu： 需要模拟cpu的类型，当然也可以使用kvm -cpu ?进行查看。
- -cdrom： 使用的光盘镜像文件，如果使用的是物理光驱安装，则把镜像文件名替换为/dev/cdrom。
- -net nic,macaddr=11:22:33:44:55:66,vlan=0： 给虚拟机指定网卡的MAC地址以及所属的VLAN。
- -usb： 开启USB设备。
- -k en-us： 键盘为美式键盘。
- -vga cirrus： 显卡是cirrus，还有其他的，可以通过man qemu-kvm来看。
- -soundhw： 声卡参数，使用kvm -soundhw ?来查看所支持的声卡类型。
- -localtime： 使用本地时间同步虚拟机时间。

XP 安装很快，20 分钟左右就可以安装好。下面开始以下后面的工作，会涉及更多的参数。

配置硬盘和网络的 virtio 模式，virtio 是个半虚拟化的模式，速度比传统方式还要快些，根据 Using virtio_net For The Guest NIC 一文的测试，virtio 还是很有优势的。

在虚拟机里面下载 virtio windows guest 驱动： Storage driver 和 Network driver，其中已经包含了 xp、win7 等的驱动。

下面我们用以下方式进行安装驱动：

下载 virtio-win-1.1.16.iso

启动虚拟机：

    kvm -m 512 -smp 1 -vga std -localtime \
    -drive file=/path/to/winxp.img,cache=writeback,if=ide,boot=on \
    -drive file=fake.img,if=virtio -net nic,model=virtio \
    -net user -usb -usbdevice tablet \
    -cdrom /path/to/virtio-win-1.1.16.iso

在启动的虚拟机里，会找到 virtio 设备，然后安装这两个驱动。安装完毕后，关机。以下面方式重新启动：

    kvm -m 512 -smp 1 -vga std -localtime -usb -usbdevice tablet -soundhw es1370 \
    -drive file=/path/to/winxp.img,cache=writeback,if=virtio,boot=on \
    -net nic,model=virtio -net user

下面配置远程桌面对 Windows 的访问，首先需要在虚拟机的 Windows 里面开启远程桌面，而且必须给用户加上密码，Windows XP 需要是 Professional 以上版本。然后运行 mmc 继续配置远程桌面访问参数。

打开 mmc 后，“添加/删除管理单元“ -> “添加” -> “组策略对象编辑器” -> “本地计算机”。在里面配置：“计算机配置” -> “管理模板” -> “Windows 组件” -> “终端服务” -> “限制最大颜色深度”，设置为24位，原来默认是16位。

关机，重新以可以以新的模式后台运行：

    kvm -snapshot -m 512 -smp 1 -vga std -localtime -usb -usbdevice tablet -soundhw es1370 \
    -drive file=/path/to/winxp.img,cache=writeback,if=virtio,boot=on \
    -net nic,model=virtio -net user -vnc 127.0.0.1:0 -daemonize -nographic \
    -redir tcp:3389::3389 -smb qemu -name "Windows XP"

- -vnc 127.0.0.1:0表示以vnc模式运行；
- -daemonize表示kvm以daemon运行；
- -snapshot写入临时文件，ctrl_a+s写入镜像；
- -nographic表示无图形输出，后台运行，可以用rdesktop连接；
- -redir tcp:3389::3389表示将虚拟机的3389端口映射为宿主机的3389端口上，这样rdesktop本机就可以进行远程桌面访问了。

如果想实时查看状态，可以使用 vncviewer 或者 vinagre 查看。

现在可以 rdesktop 进行访问了，下面是 rdesktop 的参数：

    rdesktop localhost:3389:3389 -u username -p password -g 1024×600 \
    -D -r sound:remote -z -r disk:Public=/home/havanna/Public -clipboard

这里 -z 参数可以保证鼠标运行速度不缓迟，-f 表示全屏，具体的可以 rdesktop -h 查看。

虚拟机如何使用 USB 设备呢，首先要保证用户可以访问 USB 设备，qemu 支持 usb 设备在虚拟机里面的拔插，在 qemu 界面里（或者 vnc 访问的界面里），`Ctrl+Alt+2` 进入 qemu monitor 命令行控制模式，然后

    usb_add host:vid:pid

查看 usb 设备：

    info usb

那么虚拟机如何使用 cdrom 呢，同样 `Ctrl+Alt+2` 进入 qemu monitor 控制台

    QEMU 0.10.5 monitor - type 'help' for more information
    (qemu)

help 命令可以查看更多帮助，一些命令你可能用的到，info block,eject,change

    (qemu) info block
    ide0-hd0: type=hd removable=0 file=/dev/null ro=0 drv=host_device encrypted=0
    ide1-cd0: type=cdrom removable=1 locked=0 [not inserted]
    floppy0: type=floppy removable=1 locked=0 [not inserted]
    sd0: type=floppy removable=1 locked=0 [not inserted]


    (qemu) change ide1-cd0 /tmp/dsl-4.4.10.iso
    (qemu) info block
    ide0-hd0: type=hd removable=0 file=/dev/null ro=0 drv=host_device encrypted=0
    ide1-cd0: type=cdrom removable=1 locked=0 file=/tmp/dsl-4.4.10.iso ro=0  drv=raw encrypted=0
    floppy0: type=floppy removable=1 locked=0 [not inserted]
    sd0: type=floppy removable=1 locked=0 [not inserted]


    (qemu) eject ide1-cd0
    (qemu) info block
    ide0-hd0: type=hd removable=0 file=/dev/null ro=0 drv=host_device encrypted=0
    ide1-cd0: type=cdrom removable=1 locked=0 [not inserted]
    floppy0: type=floppy removable=1 locked=0 [not inserted]
    sd0: type=floppy removable=1 locked=0 [not inserted]
    (qemu) change ide1-cd0 /tmp/fedora11.iso
    (qemu) info block
    ide0-hd0: type=hd removable=0 file=/dev/null ro=0 drv=host_device encrypted=0
    ide1-cd0: type=cdrom removable=1 locked=0 file=/tmp/fedora11.iso ro=0 drv=raw  encrypted=0
    floppy0: type=floppy removable=1 locked=0 [not inserted]
    sd0: type=floppy removable=1 locked=0 [not inserted]
