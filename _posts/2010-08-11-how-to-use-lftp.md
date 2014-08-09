---
layout: post
title: "lftp 使用方法"
description: "lftp 使用方法"
keywords: "lftp, 使用, 方法"
category: Linux
tags: [Lftp, Usage, Configuration, CLI]
---

首先，所有的 lftp 终端内的命令，都可以用

    help command

的方式来查看帮助信息。

另外，lftp 支持 tab 自动补全，记不得命令是，多打两个 tab，就可以看到可能的选项了。

#### 登录ftp

    lftp ftp://user:password@ip:port

也可以先不带用户名登录，然后在接口界面下用login命令来用指定账号登录，密码不显示。

<!-- more -->
#### 查看文件与改变目录

    ls
    cd ftpdir

嘿嘿，很简单吧？其实，在 lftp 终端中，前面带一个 l 的命令例如 lcd，指的是 local，就是在本机的操作，而对应的没有这个 l 的，都是对 ftp site 的操作。还有就是要执行本地终端命令，也可以用前面带一个!的方式。这样，配合起来，终端，本地的操作都很放遍。
例如，查看 ftp 上所有的以 mp3 为扩展名的文件：

    find . -name "*.mp3"

#### 下载

get 当然是可以的，还可以

    mget -c a.pdf

把所有的 pdf 文件以允许断点续传的方式下载。m 代表 multi

    mirror aaa/

将 aaa 目录整个的下载下来，子目录也会自动复制

    pget -c -n 10 file.dat

以最多10个线程以允许断点续传的方式下载 file.dat
可以通过设置 pget:default-n 的值而使用默认值。

#### 上传

同样的 put，mput，都是对文件的操作，和下载类似。

    mirror -R localdir

将本地目录以迭代（包括子目录）的方式反向上传到 ftp site。

#### 模式设置。

    set ftp:charset gbk

远程 ftp site 用 gbk 编码，对应的要设置为 utf8,只要替换 gb k为 utf8 即可。

    set file:charset utf8

本地的 charset 设定为 utf8,如果你是 gbk，相应改掉。

    set ftp:passive-mode 1

使用被动模式登录，有些 site 要求必须用被动模式或者主动模式才可以登录，这个开关就是设置这个的。0 代表不用被动模式。

#### 书签

其实命令行也可以有书签，在 lftp 终端提示符下：

    bookmark add linuxbbs

就可以把当前正在浏览的 ftp site 用 ustc 作为标签储存起来。以后在 shell 终端下，直接

    lftp linuxbbs

就可以自动填好用户名，密码，进入对应的目录了。

    bookmark edit

会调用编辑器手动修改书签。当然，也可以看到，这个书签其实就是个简单的文本文件。密码，用户名都可以看到。

#### 配置文件 /etc/lftp.conf

一般，我会添加这几行：

    set ftp:charset gbk
    set file:charset utf8
    set pget:default-n 5

这样，就不用每次进入都要打命令了。其他的 set 可以自己 tab 然后 help 来看。

lftp 的上传下载的速度限制

于 `lftp.conf` 中加入

    set net:limit-rate 10000,10000

lftp 缺省不会显示 ftp 服务器的欢迎信息和错误信息，这在很多时候不方便，因为你有可能想知道这个服务器到底是因为没开机连不上，还是连接数已满。如果是这样，你可以在 `~/.lftprc` 里写入一行

    debug 3

下载文件命令：续传，下载多个文件，多线程

    lftp> get -c filename
    lftp> mget *.file
    lftp> pget -n 4 filename

下载/上传/多线程 文件夹

    lftp> mirror dir
    lftp> mirror -R dir
    lftp> mirror --parallel=3 dir

纠正乱码显示中文及显示登录消息设置，修改 `/etc/lftp.conf`，加入一段

    #myset begin
    set ftp:charset "gbk"
    set file:charset "UTF-8"
    #end
    debug 3

更改本地下载目录

    ftp> lcd ldir

默认为 `/home/user`
比如改成 `lcd /home/user/download`

队列命令（不必等到下载完毕再输入命令）

    > queue

查看后台队列

    > jobs

加入队列

    > queue get file
    > jobs
    > queue start
    > jobs

ctrl+z 后台运行

退出

    ctrl+d


    $lftp
    > help lftp

可以查看 lftp 的更多命令，其中尤其是 put, mput, 和 get，mget完全对应。

例如

上传单个 html 文件到服务器一级目录下

    > put localdir/index.html

上传多个html文件到服务器一级目录下

    > mput localdir/*.html


转自[ubuntu中文社区](http://forum.ubuntu.org.cn/viewtopic.php?f=73&t=59102)（稍作修改）

下面是我个人的配置

```ini
#debug 3
set ftp:charset "gbk"
set sftp:charset "UTF-8"
set file:charset "UTF-8"
alias gbk "set ftp:charset gbk; set file:charset UTF-8"
alias utf8 "set ftp:charset UTF-8; set file:charset UTF-8"
alias passive "set ftp:passive-mode"
set ftp:ssl-auth TLS-P
set ftp:ssl-protect-data yes
set ftp:use-feat no
set ssl:verify-certificate no
set ftp:ssl-allow no
set ftp:ssl-force no
set ftp:passive-mode yes
set pget:default-n 1
#set persist-retries 5
set reconnect-interval-base 999
set reconnect-interval-multiplier 1
set net:limit-rate 61440,1024000
set prompt "\[\e[1;36m\]lftp\[\e[m\] \[\e[0;32m\]\S\? \u\@\h:\w>\[\e[m\] "
set ftp:list-empty-ok yes
```
