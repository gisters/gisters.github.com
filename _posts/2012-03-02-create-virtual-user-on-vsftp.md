---
layout: post
title: "Vsftpd 建立虚拟用户"
description: "Vsftpd 建立虚拟用户"
keywords: vsftpd, virtual, 虚拟, 用户
category: Linux
tags: [Vsftpd, Virtual]
---

centos 需要安装一下两个软件包

    # yum install vsftpd db4-utils

添加虚拟用户

    # vim /root/vsftpd
    user1
    passwd1
    user2
    passwd2

<!-- more -->
生成虚拟用户

    # rm -rf /etc/vsftpd/vsftpd.db
    # db_load -T -t hash -f /root/vsftpd /etc/vsftpd/vsftpd.db
    # chmod 600 /etc/vsftpd/vsftpd.db

编辑 `/etc/pam.d/vsftpd.vu` 文件

    # vim
    auth required /lib/security/pam_userdb.so db=/etc/vsftpd/vsftpd crypt=hash
    account required /lib/security/pam_userdb.so db=/etc/vsftpd/vsftpd crypt=hash

添加宿主虚拟用户

    # useradd -d /var/ftp -s /sbin/nologin ftpuser
    # chown -R ftpuser:ftp /var/ftp
    # passwd ftpuser

虚拟用户配置

    # mkdir -p /etc/vsftpd/vsftpd_user_conf
    # vim /etc/vsftpd/vsftpd_user_conf/user1
    local_root=/var/ftp/pub1
    # vim /etc/vsftpd/vsftpd_user_conf/user2
    local_root=/var/ftp/pub2

vsftpd 全局设置

```ini
# vim /etc/vsftpd/vsftpd.conf
listen_port=21002
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
#local_root=/var/ftp/pub
anon_upload_enable=NO
anon_mkdir_write_enable=NO
no_anon_password=YES
#anon_max_rate=30000
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
#chown_uploads=YES
#chown_username=whoever
xferlog_file=/var/log/vsftpd.log
xferlog_std_format=YES
#dual_log_enable=YES
#idle_session_timeout=600
#data_connection_timeout=120
#nopriv_user=ftpsecure
#async_abor_enable=YES
#ascii_upload_enable=YES
#ascii_download_enable=YES
ftpd_banner=Welcome to Havanna's FTP service.
#deny_email_enable=YES
#banned_email_file=/etc/vsftpd.banned_emails
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
guest_enable=YES
guest_username=ftpuser
virtual_use_local_privs=YES
#ls_recurse_enable=YES
listen=YES
listen_ipv6=NO
use_localtime=YES
tcp_wrappers=YES
userlist_enable=YES
pasv_min_port=210003
pasv_max_port=210005
local_charset=UTF-8
remote_charset=CP1251
user_config_dir=/etc/vsftpd/vsftpd_user_conf
pam_service_name=vsftpd.vu
```

重启 vsftpd

    # service vsftpd restart

修改虚拟用户只需要

    # vim /root/vsftpd
    # rm -rf /etc/vsftpd/vsftpd.db
    # db_load -T -t hash -f /root/vsftpd /etc/vsftpd/vsftpd.db

然后 `/etc/vsftpd/vsftpd_user_conf` 目录下虚拟用户的配置文件修改下即可。
