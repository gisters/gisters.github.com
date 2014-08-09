---
layout: post
title: "Steam for Linux 问题与解决"
description: "Vala 正式发布了其 Linux 版本的平台 Steam。悲催的我在其打折前一晚购买了几款游戏。"
keywords: Steam, Linux, Gentoo, 游戏
category: Linux
tags: [Steam, Gentoo, Game]
---

Vala 正式发布了其 Linux 版本的平台 Steam。悲催的我在其打折前一晚购买了几款游戏。

![Steam for Linux]({{ site.qiniudn }}/images/2013/02/steam.png "Steam for Linux")

Gentoo 用户可以通过添加 steam overlay 来安装

<!-- more -->

    # layman -f -a steam
    # emerge -av steam-meta

安装完成后，运行 `steam` 有一些小问题：

### 无法输入字符

此问题是最近的更新引起的

>The system locale affects all X input so it's critical to your usage. We recently made a change to Steam to stop using local that X doesn't support, but it's not clear if that's what is hitting you.

解决方法是

    $ export LC_CTYPE="en_US.UTF-8" && steam

### 游戏中无声音

这个很简单，修改 `/etc/portage/make.conf` 在 `USE` 行添加 `pulseaudio`，然后 `# emerge -avuDN @world` 即可。

在22天之前，开发者说已经添加了 `alsa` 的支持，只需 `SDL_AUDIODRIVER=alsa` 即可，不过经过我测试，无效。不存在 `~/.steam/root/ubuntu12_32/libsdl2-2.0.so.0`。也许后面会更新吧

### 黑屏问题

`GL_EXT_texture_compression_s3tc`

64位 gentoo 用户运行游戏 `counter-strike:source` 可能会出现的提示

>Required OpenGL extension "GL_EXT_texture_compression_s3tc" is not supported. Please install S3TC texture support.

添加 `games-util/steam-games-meta` 的use flag `s3tc`

```
# echo "games-util/steam-games-meta s3tc" >> /etc/portage/package.use
# echo "media-libs/jasper abi_x86_32" >> /etc/portage/package.use
# emerge --oneshot media-libs/jasper
# emerge --oneshot games-util/steam-games-meta
```

不过，intel 显示芯片的用户杯具了，启动 counter-strike:source 后黑屏。目前还没找到解决办法。

>As far as I know from the "open" forums and some tests I did there (hi @play3man ;-) ) it seems is a problem related with the drivers (at least for my Intel integrated 4 series) since they only support OpenGL Shading Language 1.20 and Team Fortress 2 requires 1.30.

>Solutions? Valve rewrite the shaders for OGSL 1.20, or we fill the Intel driver mailing list with messages requesting support for OGDL 1.30, just that.
