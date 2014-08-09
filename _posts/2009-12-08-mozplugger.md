---
layout: post
title: "MozPlugger 使用"
description: "mozplugger 是一款 linux 下的 browser 插件"
keywords: "mozplugger, linux, browser"
category: Linux
tags: [Mozplugger, Browser, Usage]
---

首先 mozplugger 是一款 linux 下的 browser 插件，可以在浏览器中欣赏媒体流，优势就是小巧以及高度的可定制性。安装是很简单，直接下源码包编译即可

arch用户得益于aur，直接

    $ yaourt -S mozplugger

<!-- more -->
不过呢，使用下来发现，播放在线音频，譬如 `mp3`，它都是下载完成后再播放的，于是看了下 mozplugger 的配置文件，很简单的脚本嘛
直接 cp 了一份到 `~/.mozplugger/mozpluggerrc`，vim 或其他编辑器打开该文件

其中把

    define(MP_CMD,[mplayer -really-quiet -nojoystick -nofs -zoom -vo xv,x11 -ao esd,alsa,oss,arts,null -osdlevel 0 $1 </dev/null])

修改为

    define(MP_CMD,[mplayer -really-quiet -cache 2048 -nojoystick -nofs -zoom -vo xv,x11 -ao esd,alsa,oss,arts,null -osdlevel 0 $1 </dev/null])

把

    define(MP_AUDIO,[mplayer -really-quiet -nojoystick $1 </dev/null])

修改为

    define(MP_AUDIO,[mplayer -really-quiet -cache 128 -nojoystick $1 </dev/null])

把

    define(MP_AUDIO_STREAM,[controls stream noisy ignore_errors: mplayer -really-quiet -nojoystick $1 "$file" </dev/null])

修改为

    define(MP_AUDIO_STREAM,[controls stream noisy ignore_errors: mplayer -really-quiet -cache 128 -nojoystick $1 "$file" </dev/null])

OK 了，现在音频缓冲 128KB 即可开始播放，视频缓冲 2048KB 即可开始播放，再也用不到等他下载完才播放了。可能国外的开发人员都是大水管，用不到缓冲，像我等国内的小水管还是设置下缓冲比较好

另外，由于我看媒体是 mplayer 走遍天下，so，mozplugger 默认没有 mplayer 来支持 m3u 的播放
查找

    audio/mpeg-url:m3u:MPEG music resource locator
    audio/x-mpeg-url:m3u:MPEG music resource locator
    audio/mpegurl:m3u:MPEG music resource locator
    audio/x-mpegurl:m3u:MPEG music resource locator
    audio/x-scpls:pls:Shoutcast Playlists

下面第一行添加

    controls: mplayer -playlist “$file”

嘿嘿，这样浏览媒体m3u列表的时候也能调用 mplayer 播放了，当然你也可以随机播放 m3u 列表中的曲目，上面那行修改成这样

    controls: mplayer -playlist “$file” -shuffle

其项目主页地址：[mozplugger][src]
[src]: http://mozplugger.mozdev.org/
