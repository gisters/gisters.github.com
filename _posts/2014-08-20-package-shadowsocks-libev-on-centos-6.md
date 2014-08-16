---
layout: post
title: "CentOS 6 下打包 shadowsocks-libev"
description: "什么要打 rpm 包，因为不打包就要自己去编译，特讨厌在 CentOS 上 blablabla... 的一篇又一篇自编译教程，一点都不环保，一点都不利于扩散。"
keywords: "centos, shadowsocks, libev, rpm, 打包"
category: Linux
tags: [Packager, Shadowsocks]
---

为什么要打 rpm 包，因为不打包就要自己去编译，特讨厌在 CentOS 上 blablabla... 的一篇又一篇自编译教程，一点都不环保，而且一点都不利于扩散嘛。

#### 一、准备

rpm 打包需要特定的目录结构，准备工作：

    $ echo "%_topdir %(echo $HOME)/rpmbuild" >> ~/.rpmmacros
    $ mkdir -p ~/rpmbuild/{BUILD,RPMS,S{OURCE,PEC,RPM}S}

<!-- more -->
你也需要特定的一些工具

    # yum install rpm-build rpmdevtools

目录结构

```
$ tree ~/rpmbuild
/home/havanna/rpmbuild
├── BUILD
├── BUILDROOT
├── RPMS
├── SOURCES
├── SPECS
└── SRPMS
```

打包 rpm 的核心就是 spec 文件，可以通过模板操作，可以通过其他工具来书写。给出 `shadowsocks-libev.spec`

```spec
%global commit e9a530f9dcd3d94e8dcbd341b5e0ccd5bc71cd95
%global shortcommit %(c=%{commit}; echo ${c:0:7})

Name:           shadowsocks-libev
Version:	1.4.6
Release:	1%{?dist}
License:	GPL-3
Summary:	a lightweight secured scoks5 proxy for embedded devices and low end boxes.
Url:		https://github.com/madeye/%{name}
Group:		Applications/Internet
Source0:	%{url}/archive/%{commit}/%{name}-%{version}-%{shortcommit}.tar.gz
Source1:	%{name}.json
Source2:	%{name}
Packager:	Havanna <registerdedicated(at)gmail.com>
BuildRequires:	autoconf libtool gcc openssl-devel
BuildRoot: 	%(mktemp -ud %{_tmppath}/%{name}-%{version}-%{release}-XXXX)

%description
shadowsocks-libev is a lightweight secured scoks5 proxy for embedded devices and low end boxes.

%prep
%setup -q %{name}-%{commit}

%build
%configure --prefix=%{_prefix}
make %{?_smp_mflags}

%install
rm -rf %{buildroot}
make DESTDIR=%{buildroot} install

install -d %{buildroot}%{_sysconfdir}
install -m 644 %{SOURCE1} %{buildroot}/%{_sysconfdir}

install -d %{buildroot}%{_initddir}
install -m 755 %{SOURCE2} %{buildroot}/%{_initddir}

%files
%defattr(-,root,root)
%doc Changes README.md COPYING LICENSE
%config %{_sysconfdir}/shadowsocks-libev.json
%config %{_initddir}/shadowsocks-libev
%{_bindir}/ss-local
%{_bindir}/ss-redir
%{_bindir}/ss-server
%{_bindir}/ss-tunnel
%{_mandir}/man8/shadowsocks.8.gz

%changelog
```

#### 二、打包

- **rpm 源码包**：[shadowsocks-libev-1.4.6-1.e16.src.rpm]({{ site.qiniudn }}/images/2014/08/shadowsocks-libev-1.4.6-1.el6.src.rpm)
- **GitHub**: [https://github.com/Ihavee/ihavee-rpm](https://github.com/Ihavee/ihavee-rpm)

##### 打包法一

具体的下载上面的 rpm 源码包，可以通过以下命令解压来查看

    $ rpm2cpio /path/shadowsocks-libev-1.4.6-1.el6.src.rpm | cpio -div
    shadowsocks-libev
    shadowsocks-libev-1.4.6-e9a530f.tar.gz
    shadowsocks-libev.json
    shadowsocks-libev.spec
    1888 blocks

将下面三个文件放入文件夹 `~/rpmbuild/SOURCES`

- `shadowsocks-libev`
- `shadowsocks-libev.json`
- `shadowsocks-libev-1.4.6-e9a530f.tar.gz`

安装一些必要的依赖

    # yum install autoconf libtool gcc openssl-devel

在 Centos 6.5 之前的版本，可能需要升级下默认的 gcc

随后执行打包操作

    $ rpmbuild -bb shadowsocks-libev.spec

具体用法可以 `rpmbuild --help` 查看。

若版本号变更，修改 spec 文件的版本号以及 commit 后，通过 spectool 重下源码

    $ spectool -R -g shadowsocks-libev.spec

##### 打包法二

直接通过 rpm 源码包来制作两进制包

    $ rpmbuild --rebuild /path/shadowsocks-libev-1.4.6-1.el6.src.rpm

#### 三、安装

然后直接安装两进制包

    # rpm -ivh /path/shadowsocks-libev-1.4.6-1.el6.i686.rpm
    or
    # yum install /path/shadowsocks-libev-1.4.6-1.el6.i686.rpm

参考：

- [https://fedoraproject.org/wiki/Archive:BuildingPackagesGuide?rd=Docs/Drafts/BuildingPackagesGuide](https://fedoraproject.org/wiki/Archive:BuildingPackagesGuide?rd=Docs/Drafts/BuildingPackagesGuide)
