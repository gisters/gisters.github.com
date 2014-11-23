---
layout: post
title: "修改固定大小的 VDI 虚拟磁盘"
description: "记录修改 VirtualBox 固定大小的 VDI 虚拟磁盘"
keywords: "virtualbox, VBoxManage, fixed, dynamic, 扩容, 虚拟, 磁盘"
category: Other
tags: [VirtialBox]
---

空间紧张，故给 VirtualBox 下的 Windows 8.1 分配的虚拟磁盘是固定大小的 20GB，今天给 Windows 8.1 打补丁的时候，终于出现磁盘空间不足的警告了，不得不给虚拟磁盘扩容。

网上爬文，找到解决方法，是通过参数 modifyhd 来修改，记录下：

首先固定大小的虚拟磁盘是不能扩容的

<!-- more -->
```
/Users/cnhavee/Documents/Virtual Machines.localized/Windows 8.1 $ VBoxManage modifyhd Windows\ 8.1.vdi --resize 31480
0%...
Progress state: VBOX_E_NOT_SUPPORTED
VBoxManage: error: Resize hard disk operation for this format is not implemented yet!
/Users/cnhavee/Documents/Virtual Machines.localized/Windows 8.1 $ VBoxManage showhdinfo Windows\ 8.1.vdi
UUID:           4f58019c-f45e-477a-9f82-2b6377bc956b
Parent UUID:    base
State:          created
Type:           normal (base)
Location:       /Users/cnhavee/Documents/Virtual Machines.localized/Windows 8.1/Windows 8.1.vdi
Storage format: VDI
Format variant: fixed default
Capacity:       20480 MBytes
Size on disk:   20482 MBytes
In use by VMs:  Windows 8.1 (UUID: 0e44d03f-7824-46f6-855d-81fbf9b31fc5)
```

Format variant 为 fixed default，动手转化吧

```
/Users/cnhavee/Documents/Virtual Machines.localized/Windows 8.1 $ VBoxManage clonehd Windows\ 8.1.vdi ./Windows\ 8.1_clone.vdi
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
Clone hard disk created in format 'VDI'. UUID: 380da4bf-e8fd-40eb-884e-b6a3568739c6
/Users/cnhavee/Documents/Virtual Machines.localized/Windows 8.1 $ VBoxManage showhdinfo Windows\ 8.1_clone.vdi
UUID:           380da4bf-e8fd-40eb-884e-b6a3568739c6
Parent UUID:    base
State:          created
Type:           normal (base)
Location:       /Users/cnhavee/Documents/Virtual Machines.localized/Windows 8.1/Windows 8.1_clone.vdi
Storage format: VDI
Format variant: dynamic default
Capacity:       20480 MBytes
Size on disk:   20075 MBytes
```

好了，现在是 dynamic default，扩容

```
/Users/cnhavee/Documents/Virtual Machines.localized/Windows 8.1 $ VBoxManage modifyhd Windows\ 8.1_clone.vdi --resize 31480
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
/Users/cnhavee/Documents/Virtual Machines.localized/Windows 8.1 $ VBoxManage showhdinfo Windows\ 8.1_clone.vdi
UUID:           380da4bf-e8fd-40eb-884e-b6a3568739c6
Parent UUID:    base
State:          created
Type:           normal (base)
Location:       /Users/cnhavee/Documents/Virtual Machines.localized/Windows 8.1/Windows 8.1_clone.vdi
Storage format: VDI
Format variant: dynamic default
Capacity:       31480 MBytes
Size on disk:   20075 MBytes
```

扩容成功，最后通过 VirtualBox 的界面，添加一块虚拟磁盘，定位为 Windows 8.1_clone.vdi，再移除旧的磁盘 Windows 8.1.vdi 即可。

当然，最后还得在虚拟 Windows 8.1 中，扩展下你的 C 盘，否则显示的还是 20GB。

顺带提一下如何压缩 VDI 磁盘，需要写零工具 SDelete：<http://technet.microsoft.com/en-us/sysinternals/bb897443.aspx>

下载下来的 sdelete.exe 放入 `%system32%` 下。

1. 首先虚拟机下整理磁盘
2. 打开 cmd（win7 以后需要管理员权限），`sdelete -z`
3. `VBoxManage modifyhd path|uuid --compact`

参考：<https://www.virtualbox.org/manual/ch08.html#vboxmanage-modifyvdi>
