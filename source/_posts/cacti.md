---
title: CentOS7下安装搭建Cacti 详解
tags:
  - cacti
  - linux
categories: 运维
date: 2020-08-07 14:39:52
---
## 什么是Cacti
Cacti 在英文中的意思是仙人掌的意思,Cacti是一套基于PHP,MySQL,SNMP及RRDTool开发的网络流量监测图形分析工具。它通过snmpget来获取数据,使用 RRDtool绘画图形,而且你完全可以不需要了解RRDtool复杂的参数。它提供了非常强大的数据和用户管理功能,可以指定每一个用户能查看树状结构、host以及任何一张图,还可以与LDAP结合进行用户验证,同时也能自己增加模板,功能非常强大完善。Cacti 的发展是基于让 RRDTool 使用者更方便使用该软件,除了基本的 Snmp 流量跟系统资讯监控外,Cacti 也可外挂 Scripts 及加上 Templates 来作出各式各样的监控图。

cacti是用php语言实现的一个软件,它的主要功能是用snmp服务获取数据,然后用rrdtool储存和更新数据,当用户需要查看数据的时候用rrdtool生成图表呈现给用户。因此,snmp和rrdtool是cacti的关键。Snmp关系着数据的收集,rrdtool关系着数据存储和图表的生成。

Mysql配合PHP程序存储一些变量数据并对变量数据进行调用,如：主机名、主机ip、snmp团体名、端口号、模板信息等变量。

snmp抓到数据不是存储在mysql中,而是存在rrdtool生成的rrd文件中（在cacti根目录的rra文件夹下）。rrdtool对数据的更新和存储就是对rrd文件的处理,rrd文件是大小固定的档案文件（Round Robin Archive）,它能够存储的数据笔数在创建时就已经定义。

## 什么是SNMP
nmp(Simple Network Management Protocal, 简单网络管理协议)在架构体系的监控子系统中将扮演重要角色。大体上,其基本原理是,在每一个被监控的主机或节点上 (如交换机)都运行了一个 agent,用来收集这个节点的所有相关的信息,同时监听 snmp 的 port,也就是 UDP 161,并从这个端口接收来自监控主机的指令(查询和设置)。

如果安装 net-snmp,被监控主机需要安装 net-snmp(包含了 snmpd 这个 agent),而监控端需要安装 net-snmp-utils,若接受被监控端通过trap-communicate发来的信息的话,则需要安装net-snmp,并启用trap服务。如果自行编译,需要 beecrypt(libbeecrypt)和 elf(libraryelf)的库

## 工作原理
Cacti首先要做的工作就是收集数据,cacti使用poller（轮询器）收集数据,poller是操作系统scheduler的扩展,如在unix系统中的crontab。现在的IT设施中会有许多不同的设备,如服务器、网络设备等,cacti主要使用SNMP协议来从远端的设备上收集数据,所有可以使用SNMP协议的设备都可以被cacti监控

## 安装部署及配置
### 安装lamp环境
升级系统组件到最新的版本
```bash
sudo  yum  update -y
```
关闭selinux
```bash
setenforce 0       #临时关闭命令
vi /etc/selinux/config    #将SELINUX=enforcing改为SELINUX=disabled 设置后需要重启才能生效
getenforce         #检测selinux是否关闭,Disabled 为关闭
```
关闭防火墙
```bash
firewall-cmd --state    #查看默认防火墙状态,关闭后显示not running,开启后显示running
systemctl stop firewalld.service    #临时关闭firewal
systemctl disable firewalld.service #禁止firewall开机启动
```
安装lamp环境
```bash
yum install  epel-release  -y
yum  install  httpd  mariadb-server mysql-devel    php php-mysql php-gd php-pear  -y 
```
启动并设置开机自启
```bash
systemctl start httpd   && systemctl enable httpd
systemctl start mariadb  && systemctl enable mariadb
```
配置php
```bash
vi /etc/php.ini 
    date.timezone =PRC      # 修改时区
```
添加测试页并重启httpd
```bash
echo "<?php echo phpinfo();?>"  >  /var/www/html/index.php
systemctl restart httpd
```
配置mariadb
```bash
# 初始化数据库设置密码
mysqladmin -u root password 123456  
#root用户登陆数据库
mysql -u root -p123456
# 创建用于测试php和mariadb连通性的用户
grant all privileges on *.* to test@localhost identified by 'RedHat'; 
# 立即生效
flush privileges;
```
如果是线上服务器不可以关闭防火墙可以添加规则
```bash
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
firewall-cmd --permanent --add-port=3306/tcp
firewall-cmd --reload
```
### 安装配置cacti
下载cacti
```bash
cd /usr/local/src/
wget http://www.cacti.net/downloads/cacti-0.8.8f.tar.gz
tar zxvf cacti-0.8.8f.tar.gz
mv cacti-0.8.8f /var/www/html/cacti
```
创建cacti数据库和cacti用户,赋予权限
```bash
mysql -u root -p123456
create database cacti default character set utf8;
grant all privileges on cacti.* to cacti@localhost identified by 'redhat';
flush privileges;
```
把cacti.sql导入数据库
```bash
mysql -ucacti -predhat cacti < /var/www/html/cacti/cacti.sql
```
编辑config.php和global.php
```bash
vim /var/www/html/cacti/include/config.php
    $database_type = "mysql";
    $database_default = "cacti";
    $database_hostname = "localhost";
    $database_username = "cacti";
    $database_password = "redhat";
    $database_port = "3306";
    $database_ssl = false;

vim /var/www/html/cacti/include/global.php
    $database_type = "mysql";
    $database_default = "cacti";
    $database_hostname = "localhost";
    $database_username = "cacti";
    $database_password = "redhat";
    $database_port = "3306";
    $database_ssl = false;
```
安装rrdtool以生成图像
```bash
yum  install rrdtool rrdtool-devel rrdtool-php rrdtool-perl  -y 
yum  install gd gd-devel php-gd   -y   ---rrdtool绘制图像需要的图形库
```
安装snmp服务
```bash
yum -y install net-snmp net-snmp-utils php-snmp net-snmp-libs
```
编辑配置文件
```bash
vi   /etc/snmp/snmpd.conf
    # 41行
    com2sec notConfigUser  127.0.0.1      public
    # 62行
    access  notConfigGroup ""  any    noauth    exact  all none none
    # 85行
    view all    included  .1          80
    # 最后
    syslocation Server Room
    syscontact Sysadmin (root@localhost)
    rocommunity public 127.0.0.1
    agentaddress 161
    rocommunity public
    rwcommunity private
    trapsink 192.168.124.14 public 162     # --> 这里的ip=192.168.124.14为被监控主机ip
```
启动snmp并加入自启动
```bash
systemctl restart snmpd.service
systemctl enable snmpd.service
```
授权目录权限
```bash
# 创建cacti系统账号 不需要建立登录目录
useradd -r -M cacti
chown -R cacti /var/www/html/cacti/rra/
chown -R cacti /var/www/html/cacti/log/
```
创建抓图的计划任务
```bash
crontab -e
      */1 * * * * /usr/bin/php  /var/www/html/cacti/poller.php >> /tmp/cacti_rrdtool.log
```

### 后台管理配置
浏览器访问cacti管理页面进行安装
http://cacti_ip/cacti  首次登录用户名和密码均为admin,之后可以设置新密码
![](../1.png)
![](../2.png)
![](../3.png)
默认账号密码为admin
![](../4.png)
修改密码输入新密码,再次输入确认密码
![](../5.png)

创建监控本机设备
![](../6.png)
![](../7.png)
创建成功会显示详细信息
![](../8.png)
![](../9.png)
![](../10.png)
![](../11.png)
刚创建完没有图像
![](../12.png)
过一会就可以刷新出来图像了
![](../13.png)
再等一会就可以看到流量了
![](../19.png)
## 配置被监控端
```bash
yum  install net-snmp lm_sensors  -y
```
修改被监控端的snmpd.conf
```bash
vi  /etc/snmp/snmpd.conf 

    #允许谁可以读取数据  第41行
    com2sec notConfigUser  172.16.55.144       public
    #修改访问权限改成all   第62行
    access  notConfigGroup ""      any       noauth    exact  all none none
    #去掉注释   第85行
    view all    included  .1 
```
启动服务
```bash
systemctl start  snmpd
```
![](../21.png)
![](../22.png)
![](../23.png)
![](../24.png)
![](../25.png)
![](../26.png)
![](../27.png)
![](../28.png)
![](../29.png)
等一会刷新就有流量了
![](../30.png)