---
title: centos7 安装配置MySQL数据库
tags:
  - mysql
  - linux
date: 2020-07-11 16:32:43
---
## 安装MySQL
首先确保服务器可以连接网络,然后安装epel-release源
```bash
yum install epel-release   -y
```
安装mysql和mysql-server
```bash
 yum install mysql  mysql-server   -y
```
## MySQL数据库设置
首先启动MySQL
```bash
systemctl start mysql
```
初始化数据库设置数据库root密码
```bash
sudo mysqladmin -u root password 123456  

#root用户登陆数据库
mysql -u root -p123456       
```
## 开启mysql的远程访问
```bash
#授予zabbix用户zabbix数据库的所有权限，密码admin123    zabbix.* 代表zabbix库下的所有    写为.* 代表所有的库
GRANT all ON zabbix.* TO 'zabbix'@'%' IDENTIFIED BY 'admin123';  
#刷新权限
flush privileges;   
#退出数据库 
quit                
```
## 更改mysql的语言
首先重新登录mysql，然后输入status：
```bash
mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.48, for Linux (x86_64) using  EditLine wrapper

Connection id:          1066
Current database:
Current user:           root@192.168.1.122
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.48 MySQL Community Server (GPL)
Protocol version:       10
Connection:             192.168.1.122 via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 3 days 22 hours 52 min 12 sec

Threads: 33  Questions: 1915356  Slow queries: 0  Opens: 381  Flush tables: 1  Open tables: 276  Queries per second avg: 5.608
--------------

mysql> 
```
Server characterset 现在是 latin1 需要改为utf8
更改配置文件
```bash
 vim  /etc/my.cnf
    
    # 新增配置
    [mysqld]                  
    character_set_server=utf8 
    collation-server=utf8_general_ci

    [client]   # 没有这个字段需要添加
    default-character-set=utf8
```
保存更改后的my.cnf文件后,重启下mysql,然后输入status再次查看
```bash
[root@localhost ~]# systemctl  restart mysqld   

[root@localhost ~]# mysql -u root -p123456 -h 192.168.1.122
Warning: Using a password on the command line interface can be insecure.
^[[A^[[AWelcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 5.6.48 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.48, for Linux (x86_64) using  EditLine wrapper

Connection id:          4
Current database:
Current user:           root@192.168.1.122
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.48 MySQL Community Server (GPL)
Protocol version:       10
Connection:             192.168.1.122 via TCP/IP
Server characterset:    utf8
Db     characterset:    utf8
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 14 sec

Threads: 8  Questions: 79  Slow queries: 0  Opens: 108  Flush tables: 1  Open tables: 101  Queries per second avg: 5.642
--------------

mysql> exit
Bye
[root@localhost ~]# 
```