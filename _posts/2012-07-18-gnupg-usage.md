---
layout: post
title: "GnuPG 介绍"
description: "GnuPG 是实现安全通信和数据存储的一系列工具集，可以做加密数据和做数字签名之用"
keywords: "gnupg, pgp, 密钥, 公钥"
category: Linux
tags: [GnuPG, Keys, Public]
---

#### 一、介绍

![Mutt GnuPG]({{ site.qiniudn }}/images/2012/07/mutt-gnupg.png "Mutt GnuPG")

PGP（Pretty Good Privacy），是一个基于 RSA 公钥加密体系的邮件加密软件。它不但可以对用户的数据保密以防止非授权者阅读，还能对用户的邮件加上数字签名从而使收 信人确信邮件是由该用户发出。让人们可以安全地和从未见过的人们通信，而事先不需要任何保密的渠道用来传递密钥。PGP 采用了审慎的密钥管理，一种 RSA 和传统加密的杂合算法，用于数字签名的邮件文摘算法，加密前压缩等。它功能强大，速度很快。

<!-- more -->
GnuPG 是实现安全通信和数据存储的一系列工具集，可以做加密数据和做数字签名之用。在功能上，它和 PGP 是 一样的。由于 PGP 使用了 IDEA 专利算法，所以使用 PGP 会有许可证的麻烦。但是 GnuPG 并没有使用这个算法，所以对用户来说使用 GnuPG 没有任何 限制。GnuPG 使用非对称加密算法，安全程度比较高。GnuPG 主要有以下特点：

1. 完全兼容 PGP
2. 没有使用任何专利算法，没有专利问题
3. 遵循 GNU 公共许可证
4. 与 OpenPGP 兼容
5. 使用广泛，安全性高于 PGP2，可以加密校验和 PGP5.x 格式的信息
6. 支持多种加密算法
7. 支持扩展模块
8. 用户标识遵循标准结构
9. 多语言支持（尚未支持中文）
10. 支持匿名信息接收
11. 支持HKP密钥服务

#### 二、命令

##### 创建密钥对

    $ gpg --gen-key                                     # 生成密钥对
    $ gpg --gen-revoke ID                               # 为 ID 生成吊销密钥

##### 公钥处理

    $ gpg --output filename.asc --export Havanna        # 导出公钥
    $ gpg -o filename.asc --export Havanna
    $ gpg -o filename.asc -a --export Havanna           # -a 选项是以 ASSCII 码方式导出
    $ gpg --list-public-keys                            # 显示所有公钥
    $ gpg -k
    $ gpg --check-sig                                   # 监测公钥环中公钥的签名信息
    $ gpg --fingerprint Havanna                         # 查看公钥指纹信息

##### 私钥处理

    $ gpg -o filename --export-secret-keys Havanna      # 导出用户标志为 Havanna 的私钥，不加用户标志，则导出所有私钥
    $ gpg -o filename -a --export-secret-keys Havanna   # 以 ASSCII 码方式导出
    $ gpg --list-secret-keys                            # 显示所有私钥
    $ gpg -K

##### 其他

    $ gpg --import Havanna.asc                          # 导入私钥或公钥
    $ gpg --delete-secret-and-public-key Havanna        # 删除私钥和公钥
    $ gpg --delete-secret-key Havanna                   # 删除私钥
    $ gpg --delete-key Havanna                          # 删除公钥


    $ gpg --edit-key Havanna  # 编辑密钥，要帮助输入 help
           1）list:列出密钥和用户标识
           2）addkey:添加一个子密钥
           3）delkey:删除选中的子密钥
           4）key N：选中第N个子密钥（基于1），若是0的话清除所有
           5）fpr：显示密钥指纹
           6）uid N:选中第N个用户（基于1），若是0的话清除所有
           7）adduid：添加一个用户标识
           8）deluid: 删除选中的用户标识
           9）passwd: 修改私钥的密码
           10）trust: 修改此密钥的信任度
           11）enable:启用此密钥
           12）disable:禁用此密钥
           13）lsign 为所选用户标识添加本地签名
               lsign -u other   //使用other用户的私钥对所选的用户标识进行签名
                                   //此签名可导出，不加-u选项的话使用默认用户进行签名
           14）tsign 为所选用户标识添加信任签名
               tsign -u other   //同上，只不过此签名被标记为不可导出，一般用于本地环境
                                   //信任这个用户
           15）check 检测选中用户的签名16）delsig 删除选中的用户的签名
           17）expire 修改私钥的失效时间
           18）save 保存修改

#### 三、应用

##### 加密与解密

    $ gpg -e -r Havanna.asc filename                    # 使用 Havanna 的公钥对 filename 加密，生成二进制 filename.pgp
    $ gpg -ea -r Havanna filename -o filename.asc       # 同上，不过已 ASCII 方式输入结果，并输出 asc 文件
    $ gpg -d filename.pgp -o filename                   # 对 filename.pgp 解密，保存为 filename

##### 打包方式进行签名与验证

    $ gpg -s filename                                   # 使用默认的用户对 filename 进行打包方式的签名
    $ gpg -u Havanna -s filename -o filename.sig        # 使用指定的用户 Havanna 对 filename 进行签名
    $ gpg --verify filename.gpg                         # 验证签名
    $ gpg -d filename.gpg -o filename                   # 解包并验证签名

##### 分离方式进行签名与验证

    $ gpg -sb filename
    $ gpg -u Havanna -sb filename
    $ gpg -u Havanna -o filename.sig -sb filename
    $ gpg --verify filename.gpg filename

##### 签名并加密

    $ gpg -es -r Havanna -u Havanna -o filename.gpg filename  # 使用 Havanna 的公钥进行加密，并使用 Havanna 的私钥进行签名，生成的二进制文件是 filename.gpg
    $ gpg -esa -r Havanna -u Havanna -o filename.asc filename # 同上，加上以 ASCII 编码

##### mutt 中的使用

编辑 $HOME/.gnupg/gpg.conf，注销下面两行

    ……
    keyserver hkp://keys.gnupg.net
    ……
    keyserver-options auto-key-retrieve
    ……

复制 mutt 示例配置文件至 mutt 配置目录

    $ cp /usr/share/doc/mutt/examples/gpg.rc ~/.mutt/

并在其最后加入

    set pgp_autosign=yes
    set pgp_replysign=yes
    set pgp_replyencrypt=yes
    set pgp_sign_as=BB34888A                            #你自己的公钥标志 ID
    set pgp_timeout=60
    set pgp_verify_sig=yes

编辑 $HOME/.mutt/muttrc，加入如下行

    $ source ~/.mutt/gpg.rc

thunderbird 中只需安装 Enigmail 扩展即可，支持 gnupg，可视化的配置就不多说了。

参考：

- [http://www.gnupg.org/documentation/guides.en.html](http://www.gnupg.org/documentation/guides.en.html)
- [http://codesorcery.net/old/mutt/mutt-gnupg-howto](http://codesorcery.net/old/mutt/mutt-gnupg-howto)
