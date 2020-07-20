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
## 配置文件
```bash
[mysqld]
在这个配置段之内，将会看到与mysql守护进程相关的命令

Datadir=/var/lib/mysql
Mysql服务器把数据库存储在由datadir变量所定义的目录中

Socket=/var/lib/mysql/mysql.sock
Mysql套接字把数据库程序局部的或通过网络连接到mysql客户

[safe_mysqld]
这个配置段包含mysql启动脚本所引用的命令。如果使用mysql4.x或4.x以上版本，必须把这个配置段改成[mysqld_safe] 

err-log=/var/log/mysqld.log
这是MySQL所关联的错误被发送到的这个文件。如果使用MySQL4.X或4.X以上版本，必须使用log-error指令替换这条命令。

pid-file=/var/run/mysqld/mysqld.pid
最后，pid-file指令定义MySQL服务器在运作期间的进程标识符(PID)。如果MySQL服务器当前没有运行，这个文件应该不存在。

[client]
这个配置把指令传递给与mysql服务器相关的客户

Port=3306
Mysql所相关的标准TCP/IP端口是3306。如果需要修改这个端口号（可以增强安全）。必须确保用于mysql客户于服务器的所有相应配置文件中均修改这个号

socket=/var/lib/mysql/mysql.sock
正像默认的/etc/my.cnf文件中所定义的那样，这是控制MySQL客户与服务器间通信的标准套接字文件。
```
## 增删改查
顺序：库   ——表  ——列  ——记录  ——数据
创建数据库
```bash
mysql> create  database vfast;
Query OK, 1 row affected (0.03 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| jeecmsv9           |
| mysql              |
| performance_schema |
| vfast              |
| zabbix             |
+--------------------+
6 rows in set (0.00 sec)

mysql> 
```
删除数据库
```bash
mysql> drop database vfast;
Query OK, 0 rows affected (0.01 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| jeecmsv9           |
| mysql              |
| performance_schema |
| zabbix             |
+--------------------+
5 rows in set (0.00 sec)

mysql> 
```
进入数据库
```bash
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| jeecmsv9           |
| mysql              |
| performance_schema |
| zabbix             |
+--------------------+
5 rows in set (0.00 sec)

mysql> use zabbix;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> 
```
创建表(需要指定表结构)
```bash  
mysql> create  table  rhce(id int);
Query OK, 0 rows affected (0.12 sec)

mysql> show tables;
+-----------------+
| Tables_in_vfast |
+-----------------+
| rhce            |
+-----------------+
1 row in set (0.00 sec)

mysql> 
```
插入数据
```bash
mysql> insert   into  rhce (id) values (10);     
Query OK, 1 row affected (0.02 sec)

mysql> 
mysql> 
mysql> select  *  from rhce;
+------+
| id   |
+------+
|   10 |
+------+
1 row in set (0.00 sec)

mysql> 
mysql> 
```
建表参数   aotu_increment   自动增长,例如：
```bash
mysql> create table mei(ID int(3) auto_increment  primary key ,NAME char(30));
Query OK, 0 rows affected (0.14 sec)

mysql> insert   into  mei (ID,NAME) values (1,"long");      
Query OK, 1 row affected (0.03 sec)

mysql> 
mysql> insert   into  mei (NAME) values ("xin");     
Query OK, 1 row affected (0.02 sec)

mysql> 
mysql> select * from  mei;
+----+------+
| ID | NAME |
+----+------+
|  1 | long |
|  2 | xin  |
+----+------+
2 rows in set (0.00 sec)
```
修改表名
```bash
mysql> alter  table  rhce  rename  cbd;
Query OK, 0 rows affected (0.07 sec)

mysql> show tables;
+-----------------+
| Tables_in_vfast |
+-----------------+
| cbd             |
| mei             |
+-----------------+
2 rows in set (0.00 sec)

mysql> 
```
增加字段 sex
```bash
mysql> alter  table  cbd  add  sex enum ('Y','N');   
Query OK, 0 rows affected (0.29 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> 
mysql> desc vfast.cbd;   # 查看字段
+-------+---------------+------+-----+---------+-------+
| Field | Type          | Null | Key | Default | Extra |
+-------+---------------+------+-----+---------+-------+
| id    | int(11)       | YES  |     | NULL    |       |
| sex   | enum('Y','N') | YES  |     | NULL    |       |
+-------+---------------+------+-----+---------+-------+
2 rows in set (0.00 sec)

mysql> 
```
删除字段 sex
```bash
mysql> alter table  cbd drop  sex;
Query OK, 0 rows affected (0.26 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc vfast.cbd;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id    | int(11) | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
1 row in set (0.00 sec)

mysql> 
```
修改字段的类型
```bash
mysql> desc vfast.cbd;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id    | int(11) | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
1 row in set (0.00 sec)

mysql> alter  table  cbd  modify   id   char (16);
Query OK, 1 row affected (0.42 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> 
```
改变列名(格式：change 原名称  新名称)
```bash
mysql> desc vfast.cbd;
+-------+----------+------+-----+---------+-------+
| Field | Type     | Null | Key | Default | Extra |
+-------+----------+------+-----+---------+-------+
| id    | char(16) | YES  |     | NULL    |       |
+-------+----------+------+-----+---------+-------+
1 row in set (0.01 sec)

mysql> alter  table  cbd   change  id   mingzi  char(26);     
Query OK, 1 row affected (0.33 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> 
mysql> desc vfast.cbd;
+--------+----------+------+-----+---------+-------+
| Field  | Type     | Null | Key | Default | Extra |
+--------+----------+------+-----+---------+-------+
| mingzi | char(26) | YES  |     | NULL    |       |
+--------+----------+------+-----+---------+-------+
1 row in set (0.00 sec)
```
显示表cbd的创建过程
```bash
mysql> show  create  table   cbd;
+-------+--------------------------------------------------------------------------------------------+
| Table | Create Table                                                                               |
+-------+--------------------------------------------------------------------------------------------+
| cbd   | CREATE TABLE `cbd` (
  `mingzi` char(26) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+-------+--------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> 
```
删除表
```bash
mysql> drop  table  cbd;
Query OK, 0 rows affected (0.07 sec)

mysql> show tables;
+-----------------+
| Tables_in_vfast |
+-----------------+
| mei             |
+-----------------+
1 row in set (0.00 sec)

mysql> 
```
