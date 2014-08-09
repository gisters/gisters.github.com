---
layout: post
title: "OS X 上的 pip 使用 User Installs"
description: "pip 安装包到用户目录"
keywords: "pip, user, 用户目录"
category: Mac
tags: [pip,Python]
---

可能还是使用 Gentoo 后带来的洁癖使然，在 Mac OS X 上也想将 `pip install package` 默认安装到用户目录，随便一搜，都是 `sudo`，难道大家都习惯直接拿根目录开刀的？今天上午查询了下资料，搞定将 pip 安装的包默认安装在用户目录。

说起来很简单，只需跟参数 `--user` 即可，譬如

    python setup.py install --user

或

    python setup.py install --home=<dir>

<!-- more -->
同理使用 pip 安装时只需

    pip install package --user

当然，这样安装的包，不在用户路径变量中，需要 export 下 PATH。编辑 `~/.bash_profile`，最后加入

```sh
if [ -d $HOME/Library/Python/2.7/bin ]; then
    export PATH=$HOME/Library/Python/2.7/bin:$PATH
fi
```

使用之前别忘记 `source ~/.bash_profile`

顺便也配置下常规的 pip 参数

    $ mkdir -p ~/.pip
    $ touch ~/.pip/pip.conf

编辑 `~/.pip/pip.conf`

```cfg
[global]
default-timeout = 60
download-cache = ~/.pip/cache
log-file = ~/.pip/pip.log

[install]
index-url = http://pypi.douban.com/simple
```

如果你通过 homebrew 安装的 python，记得将 homebrew 下的 `site-packages` 路径加进去

    $ echo '/usr/local/lib/python2.7/site-packages' > ~/Library/Python/2.7/lib/python/site-packages/homebrew.pth

安装 `pip`

    $ curl -O https://bootstrap.pypa.io/get-pip.py
    $ python get-pip.py --user

随后你就可以随意的通过 pip 来安装了

    $ pip install --user Django
    $ pip install --user virtualenv
    ......

当然，每次输入 `--user` 都比较麻烦，可以创建文件 `~/.pydistutils.cfg`

    $ touch ~/.pydistutils.cfg

填入如下

```cfg
[install]
install_lib = ~/Library/Python/$py_version_short/lib/python/site-packages
```

完工。

参考：

- [https://docs.python.org/2/install/index.html#alternate-installation-the-user-scheme](https://docs.python.org/2/install/index.html#alternate-installation-the-user-scheme)
- [http://www.pip-installer.org/en/latest/user_guide.html#user-installs](http://www.pip-installer.org/en/latest/user_guide.html#user-installs)
