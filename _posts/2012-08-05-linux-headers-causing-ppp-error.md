---
layout: post
title: "linux-headers 升级到 3.5 后 ppp 问题"
description: "linux-headers 升级到 3.5 后解决 ppp 编译问题"
keywords: linux, ppp, patch, 补丁
category: Linux
tags: [Patch]
---

**ppp-2.4.5-r3已经解决该情况**

Gentoo 中，最近升级 sys-kernel/linux-headers 到 3.5 后会出现 ppp-2.4.5-r2 编译不过的情况，具体的错误为

```make
In file included from plugin.c:53:0:
/usr/include/linux/if_pppox.h:84:26: error: field ‘pppol2tp’ has incomplete type
/usr/include/linux/if_pppox.h:99:28: error: field ‘pppol2tp’ has incomplete type
make[2]: *** [plugin.o] Error 1
make[2]: Leaving directory `/var/tmp/portage/net-dialup/ppp-2.4.5-r2/work/ppp-2.4.5/pppd/plugins/rp-pppoe'
/bin/sh: line 0: exit: minconn.so: numeric argument required
make[1]: *** [all] Error 255
make[1]: Leaving directory `/var/tmp/portage/net-dialup/ppp-2.4.5-r2/work/ppp-2.4.5/pppd/plugins'
make: *** [all] Error 2
emake failed
* ERROR: net-dialup/ppp-2.4.5-r2 failed (compile phase):
*   compile faile
```

给之打一个补丁即可解决。[http://www.city-fan.org/tips/PaulHowarth/Blog/2012-05-29](http://www.city-fan.org/tips/PaulHowarth/Blog/2012-05-29) 给出的补丁打不上，是因为 Gentoo 的补丁包已经包含了其中的一部分，下面我重新 diff 了一个 patch，供有相同问题的同学使用。

<!-- more -->

```diff
--- include/linux/if_pppol2tp.h.old 2012-08-05 07:25:38.665533862 +0800
+++ include/linux/if_pppol2tp.h 2012-08-05 07:26:53.122603021 +0800
@@ -32,6 +32,20 @@ struct pppol2tp_addr
    __u16 d_tunnel, d_session;  /* For sending outgoing packets */
 };
 
+/* Structure used to connect() the socket to a particular tunnel UDP
+ * socket over IPv6.
+ */
+struct pppol2tpin6_addr {
+    __kernel_pid_t  pid;            /* pid that owns the fd.
+                                     * 0 => current */
+    int     fd;                     /* FD of UDP socket to use */
+
+    __u16 s_tunnel, s_session;      /* For matching incoming packets */
+    __u16 d_tunnel, d_session;      /* For sending outgoing packets */
+
+    struct sockaddr_in6 addr;       /* IP address and port to send to */
+};
+
 /* The L2TPv3 protocol changes tunnel and session ids from 16 to 32
  * bits. So we need a different sockaddr structure.
  */
@@ -46,6 +60,17 @@ struct pppol2tpv3_addr {
        __u32 d_tunnel, d_session;      /* For sending outgoing packets */
 };
 
+struct pppol2tpv3in6_addr {
+    __kernel_pid_t  pid;            /* pid that owns the fd.
+                                     * 0 => current */
+    int     fd;                     /* FD of UDP or IP socket to use */
+
+    __u32 s_tunnel, s_session;      /* For matching incoming packets */
+    __u32 d_tunnel, d_session;      /* For sending outgoing packets */
+
+    struct sockaddr_in6 addr;       /* IP address and port to send to */
+};
+
 /* Socket options:
  * DEBUG   - bitmask of debug message categories
  * SENDSEQ - 0 => don't send packets with sequence numbers
```

修改的时候别忘记ebuild中的相关部分插入 `epatch “${FILESDIR}/eaptls-mppe-0.99.patch”`，我已经将之放入github上：[https://github.com/Ihavee/overlay/blob/master/net-dialup/ppp/files/eaptls-mppe-0.99.patch](https://github.com/Ihavee/overlay/blob/master/net-dialup/ppp/files/eaptls-mppe-0.99.patch)
