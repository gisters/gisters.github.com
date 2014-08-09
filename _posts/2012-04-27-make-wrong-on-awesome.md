---
layout: post
title: "Awesome 编译时 glib.h 报错"
description: "好久没升级了，不过为了 wps office，硬是将 profile 从 no-mutilib 切换到了 mutilib，顺便升级了下系统。发现 awesome 怎么也编译不过去"
keywords: awesome, glib.h, wps, office
category: Linux
tags: [Awesome, WM]
---

好久没升级了，不过为了 wps office，硬是将 profile 从 no-mutilib 切换到了 mutilib，顺便升级了下系统。发现 awesome 怎么也编译不过去….

报错：

```make
In file included from /var/tmp/portage/x11-wm/awesome-3.4.11/work/awesome-3.4.11/spawn.c:27:0:
/usr/include/glib-2.0/glib/gspawn.h:22:2: error: #error "Only <glib.h> can be included directly."
In file included from /usr/include/glib-2.0/glib/gspawn.h:28:0,
```

<!-- more -->
查了下 bugs.gentoo.org，嗯，自己给 awesome 源码打个补丁即可

````diff
diff --git a/spawn.c b/spawn.c
index 8f6a149..62be784 100644
--- a/spawn.c
+++ b/spawn.c
@@ -24,7 +24,7 @@
 #include <sys/types.h>
 #include <sys/wait.h>
 
-#include <glib/gspawn.h>
+#include <glib.h>
 
 #include "spawn.h"
 #include "screen.h"
````
