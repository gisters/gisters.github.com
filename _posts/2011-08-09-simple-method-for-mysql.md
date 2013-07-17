---
layout: post
title: "Mysql 简单操作 (笔记)"
description: "Mysql 简单操作 (笔记)"
keywords: mysql, root, mysqladmin, 操作
category: SQL
tags: [SQL, CLI]
---
{% include JB/setup %}

## 一、安装后

安装mysql后设置管理员root密码

    mysqladmin -u root password 123456

修改root密码

    mysqladmin -uroot -p password ‘newpassword’

<!-- more -->
重设root密码

    /etc/rc.d/mysqld stop
    mysqld_safe --skip-grant-tables &
    mysql -uroot mysql
    mysql> UPDATE user SET password=PASSWORD(“newpassword”) WHERE User=’root’;
    mysql> FLUSH PRIVILEGES;
    mysql> exit

## 二、登录数据库

    # 连接本地mysql
    mysql -uroot -p
    # 通过IP和端口连接远程mysql服务器。-h：指定数据库服务器IP -P：指定要连接的端口号。
    mysql -u root -p 123456 -h ip -P 3306
    # 通过TCP连接管理不同端口的多个MySQL（注意：MySQL4.1以上版本才有此项功能）
    mysql -u root --p 123456 --protocol=tcp --host=localhost --port=3307
    #通过socket套接字管理不同端口的多个MySQL
    mysql -u root -p --socket=/tmp/mysql3307.sock
    # 退出mysql.
    mysql> exit;

### 数据库操作

    # 显示有多少数据库
    mysql> show databases;

    # 创建与删除molyx数据库
    mysql> create database molyx;
    mysql> drop database molyx;

    # 选定molyx数据库
    mysql> use molyx;

    # 当前选择的数据库
    mysql> select database();

    # 查看状态
    mysql> show status;

    #查看进程
    mysql> show processlist;

    # 显示当前用户
    mysql> select user();

    # 显示所有用户
    mysql> select user,host,password from mysql.user;

    # 显示用户molyxuser权限
    mysql> show grants for molyxuser@localhost;

    # 查看名为molyx的数据库
    mysqlshow -uroot -p molyx

### 数据表操作

    # 显示molyx库中的数据表
    mysql> show tables from molyx;

    # 当前数据包含的表信息
    mysql> show tables;

    # 查看表结构
    mysql> describe user;
    or
    mysql> desc tablename;
    or
    mysql> show columns from tablename;

    # 建表
    mysql> create table tablename(<column1><type1>,<column2><type2>,<column3><type3>,….<columnN><typeN>);
    example:
    mysql> create table MyClass(
    > id int(4) NOT NULL primary key auto_increment,
    > name char(20) NOT NULL,
    > sex int(4) NOT NULL default ’0′,
    > degree double(16,2)) ENGINE = MYISAM;

    # 查询表中数据
    查询所有行
    mysql> select * from tablename;
    查询并排序
    mysql> select * from tablename order by id desc;
    查询前几行数据
    mysql> select * from tablename order by id limit 0,2;
    指定条件查询
    mysql> select * from tablename where name = ‘type’;
    查询唯一不重复记录
    mysql> select distinct name from tablename;

    # 插入数据
    mysql> insert into tablename (<column1>,<column2>,<column3>) values (type1,type2,type3);
    or
    mysql> insert into tablename values(1,’Tom’,96.45),(2,’Joan’,82.99), (2,’Wang’, 96.59);

    # 删除表中数据
    mysql> delete from tablename where id=N

    # 修改表中数据
    mysql> update tablename set column=’value’ where id=1;
    or
    mysql> update tablename set column=replace(column,’oldvalue’,'newvalue’);
    example:
    mysql> update article set content=concat(‘你好’,content);
    mysql> update user set pass=’123456′ where name=’name’;

    # 更改用户名
    update set user=”newname” where user=”oldname”;

    # 更改表名
    mysql> rename table tablenameold to tablenamenew;

    # 在表中增加字段
    mysql> alter table tablename add column type default ’0′;
    example:
    mysql> alter table user add column date char(8) after pass;

    # 在user表中修改字段名及字段数据类型
    mysql> alter table tablename change column <column1> <type1> char(10);
    example:
    mysql> alter table user change column pass password char(10);

    # 删除表中字段
    mysql> alter table tablename grop column <column1>;
    example:
    mysql> alter table user drop column date;

    # 删除用户molyxuser
    mysql> use mysql;
    mysql> DELETE FROM user WHERE User=’molyxuser’;
    or
    mysql> drop user ‘molyxuser’@'%’;

    # 删除表
    mysql> drop table tablename;

## 五、权限操作

    # 先创建个molyx库，再创建个可以操作molyx库的用户molyxuser，密码为123456
    mysql> create database molyx;
    mysql> grant select,insert,update,delete,create,alter,create temporary tables,create view,show view,drop on molyx.* to ‘molyxuser’@'localhost’ identified by “123456″;
    or
    mysql> grant all on molyx.* to ‘molyxuser’@localhost identified by “123456″;

    #收回molyxuser针对molyx库的所有权限
    mysql> revoke all on molyx.* from ‘molyxuser’@localhost identified by “123456″;

    #创建一个针对molyx库具有所有权限，可从任意IP登录的帐号molyxuser
    mysql> grant all on molyx.* to ‘molyxuser’@'%’ identified by ’123456′;

## 六、备份与还原

    # 备份molyx到文件molyx.sql
    mysqldump -uroot -p --databases molyx > /your/path/molyx.sql

    # 备份全部数据
    mysqldump -uroot -p -all -database > /your/path/backup.sql

    # 备份molyx并压缩
    mysqldump -u root -p --databases molyx | gzip > /your/path/molyx.sql.gz

    # 将molyx.sql导入到数据库
    mysqlimport -uroot -p < /your/path/molyx.sql

    # 将压缩文件molyx.sql.gz中数据恢复到molyx库
    gzip < molyx.sql.gz | mysql -uroot -p --database molyx

    # 将文本数据导入数据库:
    mysql> use test;
    mysql> load data local infile “filename” into table tablename;

    # 检查所有的.myi文件
    myisamchk /your/path/mysql/*.MYI

    # 修复所有的.myi
    myisamchk -r /your/path/mysql/*.MYI

## 七、其他

    #mysql 显示和使用的mysql数据库。前面已经简单的提过用法；比如登录等
    #mysqladmin 用来创建和维护mysql数据库的命令
    #isamchk 是用来修复、检查和优化.ism后缀的数据库文件
    #mysqldump 是用于备份数据库
    #myisamchk 用来修复.myi后缀的数据库文件

### 字段类型

- INT[(M)] 型： 正常大小整数类型
- DOUBLE[(M,D)] [ZEROFILL] 型： 正常大小(双精密)浮点数字类型
- DATE 日期类型：支持的范围是1000-01-01到9999-12-31。MySQL以YYYY-MM-DD格式来显示DATE值，但是允许你使用字符串或数字把值赋给DATE列
- CHAR(M) 型：定长字符串类型，当存储时，总是是用空格填满右边到指定的长度
- BLOB TEXT类型，最大长度为65535(2^16-1)个字符。
- VARCHAR型：变长字符串类型

安装升级

    # emerge --config =dev-db/mysql-5.1.53
    # mysql_upgrade tool

### mysqldump 命令的使用

备份和导出数据库

    mysqldump -h database_ip -u Username -p --opt databasename > backup-file.sql

只导出数据库表结构

    mysqldump -h database_ip -d -u Username -p databasename >database_structure.sql

只导出数据库中的某个表

    mysqldump --opt --add-drop-table -u Username -p databasename tablename > dump.sql

如果不想手工输入密码 请使用--password 参数

    mysqldump -h database_ip -u Username --password=123456 --opt databasename > backup-file.sql
    mysqldump -h database_ip -d -u Username --password=123456 databasename >database_structure.sql

### mysql 命令使用

将查询结果保存到文件

    select title from book into outfile ‘/tmp/outfile.txt’;

查找表中多余的重复记录，重复记录是根据某个字段（peopleId）来判断

    select * from people where peopleId in (select peopleId from people group by
    peopleId having count(peopleId) > 1);

查询表中不重复记录(排除重复记录)

    select * from phome_ecms_wma where title in (select distinct title from phome_ecms_wma);

删除表中重复记录,重复记录是根据某个字段（title）来判断

    select *,count(distinct title) INTO OUTFILE ‘/tmp/table.bak’ from phome_ecms_wma group by title;
    delete from phome_ecms_wma;
    LOAD DATA INFILE ‘/tmp/table.bak’ REPLACE INTO TABLE phome_ecms_wma character set utf8;

查询数据库当前编码

    mysql> show variables like “character_set%”;

修改表字段类型

    mysql> alter table table_name change last_action last_action datetime NOT NULL default ’0000-00-00 00:00:00′;

给表添加一个新字段

    mysql> ALTER TABLE host ADD ks_mac VARCHAR(100);

从表中删除一个字段

    mysql> ALTER TABLE table_name DROP field_name;

重命名表

    mysql>alter table t1 rename t2;

给字段加索引

    mysql> alter table tablename add index 索引名 (字段名1[，字段名2 …]);
    mysql> alter table tablename add index emp_name (name);

加主关键字的索引

    mysql> alter table tablename add primary key(id);

加唯一限制条件的索引

    mysql> alter table tablename add unique emp_name2(cardnumber);

删除某个索引

    mysql>alter table tablename drop index emp_name;

远程访问mysql 设置

    mysql> GRANT ALL PRIVILEGES ON database_test.* to root@192.168.1.9 IDENTIFIED BY ’123456′;
    mysql> FLUSH PRIVILEGES;

使用SHOW语句找出在服务器上当前存在什么数据库

    mysql> show databases;

创建一个数据库MYSQLDATA

    mysql> create database mydata;

选择你所创建的数据库

    mysql> use mydata;

查看现在的数据库中存在什么表

    mysql> show tables;

创建一个数据库表

    mysql> create table mytable (name varchar(20), sex char(1));

显示表的结构：

    mysql> describe mytable;

往表中加入记录

    mysql> insert into mytable values (“test”,”m”);

用文本方式将数据装入数据库表中（例如 d:\mysql.txt）

    mysql> load data local infile “d:/mysql.txt” into table mytable;

导入.sql文件命令（例如 d:\mysql.sql）

    mysql>use database;
    mysql>source d:/mysql.sql;

删除表

    mysql>drop table mytable;

清空表

    mysql>delete from mytable;

更新表中数据

    mysql>update mytable set sex=”f” where name=test;

刚安装好的MySQL包含一个含空密码的root帐户和一个匿名帐户，这是很大的安全隐患，对于一些重要的应用我们应将安全性尽可能提高，在这里应把匿名帐户删除、root帐户设置密码，可用如下命令进行：

    mysql> use mysql;
    mysql> delete from User where User=”";
    mysql> update User set Password=PASSWORD(newpassword) where User=root;

如果要对用户所用的登录终端进行限制，可以更新User表中相应用户的Host字段，在进行了以上更改后应重新启动数据库服务，此时登录时可用如下类似命令：

    shell> mysql -uroot -p;
    shell> mysql -uroot -pnewpassword;
    shell> mysql mydb -uroot -p;
    shell> mysql mydb -uroot -pnewpassword;

上面命令参数是常用参数的一部分，详细情况可参考文档。此处的mydb是要登录的数据库的名称。

在进行开发和实际应用中，用户不应该只用root用户进行连接数据库，虽然使用root用户进行测试时很方便，但会给系统带来重大安全隐患，也不利于管理技术的提高。我们给一个应用中使用的用户赋予最恰当的数据库权限。如一个只进行数据插入的用户不应赋予其删除数据的权限。MySQL的用户管理是通过User表来实现的，添加新用户常用的方法有两个，一是在User表插入相应的数据行，同时设置相应的权限；二是通过grant命令创建具有某种权限的用户。其中grant的常用用法如下：

    mysql> grant all on mydb.* to NewUserName@HostName identified by “password” ;
    mysql> grant usage on *.* to NewUserName@HostName identified by “password”;
    mysql> grant select,insert,update on mydb.* to NewUserName@HostName identified by “password”;
    mysql> grant update,delete on mydb.TestTable to NewUserName@HostName identified by “password”;

若要给此用户赋予他在相应对象上的权限的管理能力，可在grant后面添加with grant option选项。而对于用插入User表添加的用户，Password字段应用password函数进行更新加密，以防不轨之人窃看密码。对于那些已经不用的用户应给予清除，权限过界的用户应及时回收权限，回收权限可以通过更新User表相应字段，也可以使用revoke操作。以下是常用权限的解释：

全局管理权限

- FILE：在MySQL服务器上读写文件。
- PROCESS：显示或杀死属于其它用户的服务线程。
- RELOAD：重载访问控制表，刷新日志等。
- SHUTDOWN：关闭MySQL服务。

数据库/数据表/数据列权限

- ALTER：修改已存在的数据表（例如增加/删除列）和索引。
- CREATE：建立新的数据库或数据表。
- DELETE：删除表的记录。
- DROP：删除数据表或数据库。
- INDEX：建立或删除索引。
- INSERT：增加表的记录。
- SELECT：显示/搜索表的记录。
- UPDATE：修改表中已存在的记录。

特别的权限

- ALL：允许做任何事（和root一样）。
- USAGE：只允许登录--其它什么也不允许做。

MySQL常用操作基本操作，以下都是MySQL5.0下测试通过首先说明下，记住在每个命令结束时加上；（分号）

导出整个数据库

    mysqldump -u 用户名 -p --default-character-set=latin1 数据库名 > 导出的文件名(数据库默认编码是latin1)
    mysqldump -u wcnc -p smgp_apps_wcnc > wcnc.sql

导出一个表

    mysqldump -u 用户名 -p 数据库名 表名> 导出的文件名
    mysqldump -u wcnc -p smgp_apps_wcnc users> wcnc_users.sql

导出一个数据库结构

    mysqldump -u wcnc -p -d --add-drop-table smgp_apps_wcnc >d:wcnc_db.sql
    -d 没有数据 --add-drop-table 在每个create语句之前增加一个drop table

导入数据库
常用source 命令，进入mysql数据库控制台，如

    mysql -u root -p
    mysql>use 数据库

然后使用source命令，后面参数为脚本文件(如这里用到的.sql)

    mysql>source d:wcnc_db.sql

