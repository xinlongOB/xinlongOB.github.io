---
title: centos7 安装配置MySQL数据库
tags:
  - mysql
  - linux
categories: 运维
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
#授予zabbix用户zabbix数据库的所有权限,密码admin123    zabbix.* 代表zabbix库下的所有    写为.* 代表所有的库
GRANT all ON zabbix.* TO 'zabbix'@'%' IDENTIFIED BY 'admin123';  
#刷新权限
flush privileges;   
#退出数据库 
quit                
```
## 更改mysql的语言
首先重新登录mysql,然后输入status：
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
在这个配置段之内,将会看到与mysql守护进程相关的命令

Datadir=/var/lib/mysql
Mysql服务器把数据库存储在由datadir变量所定义的目录中

Socket=/var/lib/mysql/mysql.sock
Mysql套接字把数据库程序局部的或通过网络连接到mysql客户

[safe_mysqld]
这个配置段包含mysql启动脚本所引用的命令。如果使用mysql4.x或4.x以上版本,必须把这个配置段改成[mysqld_safe] 

err-log=/var/log/mysqld.log
这是MySQL所关联的错误被发送到的这个文件。如果使用MySQL4.X或4.X以上版本,必须使用log-error指令替换这条命令。

pid-file=/var/run/mysqld/mysqld.pid
最后,pid-file指令定义MySQL服务器在运作期间的进程标识符(PID)。如果MySQL服务器当前没有运行,这个文件应该不存在。

[client]
这个配置把指令传递给与mysql服务器相关的客户

Port=3306
Mysql所相关的标准TCP/IP端口是3306。如果需要修改这个端口号（可以增强安全）。必须确保用于mysql客户于服务器的所有相应配置文件中均修改这个号

socket=/var/lib/mysql/mysql.sock
正像默认的/etc/my.cnf文件中所定义的那样,这是控制MySQL客户与服务器间通信的标准套接字文件。
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

查看数据
```bash
mysql> use vfast
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> 
mysql> 
mysql> show tables;
+-----------------+
| Tables_in_vfast |
+-----------------+
| mei             |
+-----------------+
1 row in set (0.00 sec)

mysql> select * from mei;
+----+------+
| ID | NAME |
+----+------+
|  1 | long |
|  2 | xin  |
+----+------+
2 rows in set (0.00 sec)

mysql> 
```
插入数据
```bash
mysql> select * from mei;
+----+------+
| ID | NAME |
+----+------+
|  1 | long |
|  2 | xin  |
+----+------+
2 rows in set (0.00 sec)

mysql> insert into mei (ID,NAME) values ("10","long");  
Query OK, 1 row affected (0.02 sec)

mysql> select * from mei;
+----+------+
| ID | NAME |
+----+------+
|  1 | long |
|  2 | xin  |
| 10 | long |
+----+------+
3 rows in set (0.00 sec)

mysql> 
```
删除表中某一行数据
```bash
mysql> select * from mei;
+----+------+
| ID | NAME |
+----+------+
|  1 | long |
|  2 | xin  |
| 10 | long |
+----+------+
3 rows in set (0.00 sec)

mysql> delete from  mei   where  ID=1; 
Query OK, 1 row affected (0.01 sec)

mysql> 
mysql> select * from mei;
+----+------+
| ID | NAME |
+----+------+
|  2 | xin  |
| 10 | long |
+----+------+
2 rows in set (0.00 sec)
```
更新数据
```bash
mysql> select * from mei;
+----+------+
| ID | NAME |
+----+------+
|  2 | xin  |
| 10 | long |
+----+------+
2 rows in set (0.00 sec)

mysql> update mei set ID=9  where  NAME="long";
Query OK, 1 row affected (0.02 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from mei;
+----+------+
| ID | NAME |
+----+------+
|  2 | xin  |
|  9 | long |
+----+------+
2 rows in set (0.00 sec)

mysql> 
```
删除表中所有数据
```bash
# truncate   删除所有数据（打碎表后重建新表） 删除速度快于dalete
mysql> select * from mei;
+----+------+
| ID | NAME |
+----+------+
|  2 | xin  |
|  9 | long |
+----+------+
2 rows in set (0.00 sec)

mysql> 
mysql> truncate  table mei;
Query OK, 0 rows affected (0.13 sec)

mysql> select * from mei;  
Empty set (0.00 sec)

mysql> 
# delete    逐行删除数据
mysql> select * from mei;
+----+------+
| ID | NAME |
+----+------+
|  1 | xin  |
| 10 | long |
+----+------+
2 rows in set (0.00 sec)

mysql> delete from mei;
Query OK, 2 rows affected (0.02 sec)

mysql> select * from mei;
Empty set (0.00 sec)

mysql> 
```
## Mysql函数
  sum   求和
  max   取最大值
  min    取最小值
  avg    去平均值
  count   统计
```bash
mysql> create table  web_url  (id int(10)  auto_increment  primary key  ,NAME char(50),url char(90));
Query OK, 0 rows affected (0.12 sec)
mysql> show tables;
+-----------------+
| Tables_in_vfast |
+-----------------+
| mei             |
| url             |
| web_url         |
+-----------------+
3 rows in set (0.00 sec)
mysql> 
mysql> insert into  web_url (id,NAME,url) values (1,"baidu","www.baidu.com");
Query OK, 1 row affected (0.02 sec)
mysql> 
mysql> insert into  web_url (NAME,url) values ("zhihu","www.zhihu.com");     
Query OK, 1 row affected (0.02 sec)
mysql> 
mysql> insert into  web_url (NAME,url) values ("coding","www.coding.com");              
Query OK, 1 row affected (0.03 sec)
mysql> 
mysql> insert into  web_url (NAME,url) values ("jingdong","www.jingdong.com");               
Query OK, 1 row affected (0.02 sec)
mysql> select * from web_url;
+----+----------+------------------+
| id | NAME     | url              |
+----+----------+------------------+
|  1 | baidu    | www.baidu.com    |
|  2 | zhihu    | www.zhihu.com    |
|  3 | coding   | www.coding.com   |
|  4 | jingdong | www.jingdong.com |
+----+----------+------------------+
4 rows in set (0.00 sec)
```
sum   求和
```bash
mysql> select * from web_url;
+----+----------+------------------+
| id | NAME     | url              |
+----+----------+------------------+
|  1 | baidu    | www.baidu.com    |
|  2 | zhihu    | www.zhihu.com    |
|  3 | coding   | www.coding.com   |
|  4 | jingdong | www.jingdong.com |
|  5 | jingdong | www.jingdong.com |
|  6 | coding   | www.coding.com   |
|  7 | coding   | www.coding.com   |
|  8 | coding   | www.coding.com   |
+----+----------+------------------+
8 rows in set (0.00 sec)

mysql> select sum(id) from  web_url;
+---------+
| sum(id) |
+---------+
|      36 |
+---------+
1 row in set (0.00 sec)

mysql> 
```
max   取最大值
```bash
mysql> select max(id) from  web_url;        
+---------+
| max(id) |
+---------+
|       8 |
+---------+
1 row in set (0.00 sec)
```
min    取最小值
```bash
mysql> select min(id) from  web_url;  
+---------+
| min(id) |
+---------+
|       1 |
+---------+
1 row in set (0.00 sec)
```
avg    取平均值
```bash
mysql> select avg(id) from  web_url;    
+---------+
| avg(id) |
+---------+
|  4.5000 |
+---------+
1 row in set (0.00 sec)
```
count   统计
```bash
mysql> select count(*) from  web_url;       
+----------+
| count(*) |
+----------+
|        8 |
+----------+
1 row in set (0.00 sec)

mysql> 
```
## 查看某个字段出现的次数
```bash
mysql> select * from web_url;            
+----+----------+------------------+
| id | NAME     | url              |
+----+----------+------------------+
|  1 | baidu    | www.baidu.com    |
|  2 | zhihu    | www.zhihu.com    |
|  3 | coding   | www.coding.com   |
|  4 | jingdong | www.jingdong.com |
|  5 | jingdong | www.jingdong.com |
|  6 | coding   | www.coding.com   |
|  7 | coding   | www.coding.com   |
|  8 | coding   | www.coding.com   |
+----+----------+------------------+
8 rows in set (0.00 sec)

mysql> 
mysql> select url,count(*) AS count  from  web_url   group by url order by count DESC limit 5;       
+------------------+-------+
| url              | count |
+------------------+-------+
| www.coding.com   |     4 |
| www.jingdong.com |     2 |
| www.baidu.com    |     1 |
| www.zhihu.com    |     1 |
+------------------+-------+
4 rows in set (0.00 sec)

mysql> select NAME,count(*) AS count  from  web_url   group by url order by count DESC limit 5;   
+----------+-------+
| NAME     | count |
+----------+-------+
| coding   |     4 |
| jingdong |     2 |
| zhihu    |     1 |
| baidu    |     1 |
+----------+-------+
4 rows in set (0.00 sec)
```
分组排序
```bash
mysql> select * from  shop;
+--------+-------+------+
| NAME   | MONEY | NUM  |
+--------+-------+------+
| apple  |    10 |  100 |
| banana |    15 |   80 |
| orange |     8 |   70 |
| litchi |    20 |  150 |
+--------+-------+------+
4 rows in set (0.00 sec)

# 以NUM 数值 从小到大排序
mysql> select * from  shop order by NUM;
+--------+-------+------+
| NAME   | MONEY | NUM  |
+--------+-------+------+
| orange |     8 |   70 |
| banana |    15 |   80 |
| apple  |    10 |  100 |
| litchi |    20 |  150 |
+--------+-------+------+
4 rows in set (0.00 sec)

mysql> 
```
```bash
# 以NUM 数值 从大到小排序
mysql> select * from  shop order by NUM desc;
+--------+-------+------+
| NAME   | MONEY | NUM  |
+--------+-------+------+
| litchi |    20 |  150 |
| apple  |    10 |  100 |
| banana |    15 |   80 |
| orange |     8 |   70 |
+--------+-------+------+
4 rows in set (0.00 sec)

mysql> 
```
```bash
# 以NUM 数值为例 找出最多的两个
mysql> select * from  shop order by NUM desc limit 2;
+--------+-------+------+
| NAME   | MONEY | NUM  |
+--------+-------+------+
| litchi |    20 |  150 |
| apple  |    10 |  100 |
+--------+-------+------+
2 rows in set (0.00 sec)

mysql> select * from  shop;
+--------+-------+------+
| NAME   | MONEY | NUM  |
+--------+-------+------+
| apple  |    10 |  100 |
| banana |    15 |   80 |
| orange |     8 |   70 |
| litchi |    20 |  150 |
+--------+-------+------+
4 rows in set (0.00 sec)

mysql> 
```
按组排序
```bash
mysql> select * from  shop group by MONEY;
+--------+-------+------+
| NAME   | MONEY | NUM  |
+--------+-------+------+
| orange |     8 |   70 |
| apple  |    10 |  100 |
| banana |    15 |   80 |
| litchi |    20 |  150 |
+--------+-------+------+
4 rows in set (0.00 sec)

mysql> 
mysql> select count(*) from  shop group by MONEY;     
+----------+
| count(*) |
+----------+
|        1 |
|        1 |
|        1 |
|        1 |
+----------+
4 rows in set (0.00 sec)

mysql> 
```
```bash
# 查看MONEY 相同的次数 并且大于 1的数据
mysql> select count(*) from  shop group by MONEY having count(*) > 1;
+----------+
| count(*) |
+----------+
|        2 |
+----------+
1 row in set (0.00 sec)

mysql> 
```
注意 group by 不能和条件where共同使用,需要用having

## 查看默认引擎 
```bash
mysql> show variables like  '%storage_engine%';
+----------------------------+--------+
| Variable_name              | Value  |
+----------------------------+--------+
| default_storage_engine     | InnoDB |
| default_tmp_storage_engine | InnoDB |
| storage_engine             | InnoDB |
+----------------------------+--------+
3 rows in set (0.00 sec)

mysql> 
```
创建表时指定引擎
```bash
mysql> create  table num (id int(10)) engine=myisam; 
Query OK, 0 rows affected (0.02 sec)

mysql> show create table num ;
+-------+---------------------------------------------------------------------------------------+
| Table | Create Table                                                                          |
+-------+---------------------------------------------------------------------------------------+
| num   | CREATE TABLE `num` (
  `id` int(10) DEFAULT NULL
) ENGINE=MyISAM DEFAULT CHARSET=utf8 |
+-------+---------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> 
```
## MySql引擎 
Mysql一共包含两种引擎：myisam  and  innodb
```bash
    rhe15之前系统自带的mysql  engine  is  myisam
                  之后engine   is   innodb
```
两者不同： 
```bash
     Innodb是事务型日志,它是先记录日志在操作的
      保证了数据库的合理性,持久性,一致性。

日志存在/var/lib/mysql目录下,（lib logfile0,ib_logfile1）这两个日志文件都很小,他们是采用循环结构存储日志的,只记录上一次的操作,下一个事务开始即覆盖上一个事务的日志
```
Myisam和innodb引擎的区别
```bash
    MYISAM是mysql的默认存储引擎,MYISAM不支持事务、也不支持外键,但其访问速度快,对事务完整性没有要求。
   Innodb存储引擎提供了具有提交、回滚和崩溃回复能力的事务安全。但是比起MYISAM存储引擎,innodb写的处理效率差一些并且会占有更多的磁盘空间以保留数据和索引。
```
事务  
```bash
  事务都应该具备ACID特征,所谓ACID是Atomic（原子性）,consistent（一致性）,Iolated（隔离性）,Durable（持续性）四个词的首字母所写,下面以“银行转账”为例分别来说明一下他们的含义：

  原子性：组成事务处理的语句形成了一个逻辑单元,不能只执行其中的一部分,换句话说,事务是不可分割的最小单元,比如：银行转账过程中,必须同时从一个账户减去转账金额,并加到另一个账户中,只改变一个账户是不合理的

  一致性：在事务处理执行前后,数据库是一致的,也就是说,事务应该正确的转换系统状态,比如：银行转账过程中,要么转账金额从一个账户转入另一个账户,要么两个账户都不变,没有其他的情况

  隔离性：一个事务处理对另一个事务处理没有影响,就是说任何事务都不可能看到一个处在不完整状态下的事务。比如说,银行转账过程中,转账后账户的状态要能够保存下来。
   
  持续性：事务处理的效果能够被永久保存下来。反过来说,事务应当能够承受所有的失败,包括服务器、进程、通信以及媒体失败等等,比如：银行转账过程中,转账后账户的状态要能被保存下来。
  假设你在ATM提款机上存钱,当你操作完毕后,机器在写入数据库数据（往你账户中写入你存的数额）的时候。机器宕机了。如果没事务是不是就悲剧了
```

## 简单介绍事务回滚实例
用begin、rollback、commit来实现
```bash
  begin  开始一个事务
  rollback   事务回滚
  commit   事务确认
```
实例
```bash
mysql> create database vfast;      # 创建数据库
Query OK, 1 row affected (0.00 sec)

mysql> use vfast;                 # 进入数据库
Database changed

# centos6 可以写为 TYPE=INNODB
#mysql> create table test (a int) TYPE=INNODB;
#ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'TYPE=INNODB' at line 1


mysql> create table test (a int) engine=INNODB;     # 创建表 指定引擎为innodb
Query OK, 0 rows affected (0.13 sec)

mysql> begin;                                   # 开始事务日志
Query OK, 0 rows affected (0.00 sec)

mysql> insert into test values(3);              # 插入数据
Query OK, 1 row affected (0.00 sec)

mysql>  insert into test values(4);             # 插入数据
Query OK, 1 row affected (0.00 sec)

mysql>  select * from test;                     # 查看
+------+
| a    |
+------+
|    3 |
|    4 |
+------+
2 rows in set (0.00 sec)                      

mysql> rollback;                              # 回滚
Query OK, 0 rows affected (0.03 sec)  

mysql>  select * from test;                   # 再次查看无数据
Empty set (0.00 sec)

mysql>  insert into test values(3);           # 再次插入数据
Query OK, 1 row affected (0.02 sec)

mysql> commit;                                # 提交
Query OK, 0 rows affected (0.00 sec)

mysql>  rollback;                             # 再次回滚
Query OK, 0 rows affected (0.00 sec)

mysql> select * from test;                    # 查看 还是有数据
+------+
| a    |
+------+
|    3 |
+------+
1 row in set (0.00 sec)

mysql> 
```
## 关于数据锁
mysql在执行每一个操作的时候都会先申请锁,申请到锁后才会执行操作,如果手动备份某个表的时候一定要对该表手动加读锁,为了保持数据一致性,防止在备份的时候又在写东西,备份后在执行unlock命令解锁就行了
如你在对一个表插入1000条数据的时候,用另一个终端更新其中一条,结果是发现更新会一直等待到第一个操作结束后再更新
```bash
read   只读
write  谁加的写锁,谁能读写,其他的不能读写
刷新立即生效
flush  privilege
```
实例
```bash
mysql>  select * from test;
+------+------+
| a    | sex  |
+------+------+
|    3 | NULL |
+------+------+
1 row in set (0.00 sec)

mysql> 
mysql> alter  table test  add  liu  char(10);    

Query OK, 0 rows affected (0.31 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> 
mysql>  select * from test;
+------+------+------+
| a    | sex  | liu  |
+------+------+------+
|    3 | NULL | NULL |
+------+------+------+
1 row in set (0.00 sec)

mysql> 
mysql> show  OPEN  tables; 
+--------------------+----------------------------------------------------+--------+-------------+
| Database           | Table                                              | In_use | Name_locked |
+--------------------+----------------------------------------------------+--------+-------------+
| zabbix             | proxy_dhistory                                     |      0 |           0 |
| jeecmsv9           | jc_site_company                                    |      0 |           0 |
| zabbix             | interface                                          |      0 |           0 |
| performance_schema | events_waits_history_long                          |      0 |           0 |
| zabbix             | sysmap_element_url                                 |      0 |           0 |
| performance_schema | mutex_instances                                    |      0 |           0 |
| zabbix             | maintenances_windows                               |      0 |           0 |
| vfast              | test                                               |      1 |           0 |
+--------------------+----------------------------------------------------+--------+-------------+

mysql>  unlock   tables;  
Query OK, 0 rows affected (0.00 sec)

mysql> show  OPEN  tables;     
+--------------------+----------------------------------------------------+--------+-------------+
| Database           | Table                                              | In_use | Name_locked |
+--------------------+----------------------------------------------------+--------+-------------+
| zabbix             | proxy_dhistory                                     |      0 |           0 |
| jeecmsv9           | jc_site_company                                    |      0 |           0 |
| zabbix             | interface                                          |      0 |           0 |
| performance_schema | events_waits_history_long                          |      0 |           0 |
| zabbix             | sysmap_element_url                                 |      0 |           0 |
| performance_schema | mutex_instances                                    |      0 |           0 |
| zabbix             | maintenances_windows                               |      0 |           0 |
| vfast              | test                                               |      0 |           0 |
+--------------------+----------------------------------------------------+--------+-------------+
```
## 数据库备份及恢复
备份
```bash
mysqldump  -h   IP   -uroot -pxxxx  vfast > /tmp/mydump.sql 

[sgsm@localhost test3]$ mysqldump -u root -p123456   vfast  > vfast.sql
Warning: Using a password on the command line interface can be insecure.
[sgsm@localhost test3]$ 
[sgsm@localhost test3]$ ll
总用量 4
-rw-r--r-- 1 sgsm users 1900 8月   2 21:59 vfast.sql
[sgsm@localhost test3]$ 


```
还原--命令行
```bash
# 恢复数据之前 库需要存在 并且无数据
mysql  -uroot  -pxxx  tdlinux  </tmp/mydump.sql  

mysql> create database  vfast; 
Query OK, 1 row affected (0.00 sec)

mysql> use vfast;
Database changed
mysql> show tables;
Empty set (0.00 sec)

mysql> exit
Bye
[sgsm@localhost test3]$ mysql -u root -p123456  vfast < vfast.sql 
Warning: Using a password on the command line interface can be insecure.

[sgsm@localhost test3]$ mysql -u root -p123456  

mysql> use vfast;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-----------------+
| Tables_in_vfast |
+-----------------+
| test            |
+-----------------+
1 row in set (0.00 sec)

mysql> 
```
还原--SQL语句
```bash
source  /tmp/mydump.sql 

mysql> source  /home/sgsm/test3/vfast.sql
Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.16 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 1 row affected (0.01 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.01 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

mysql> 
```