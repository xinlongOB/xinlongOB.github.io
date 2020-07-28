---
title: 常用端口总结
tags:
  - shell
  - linux
categories: 运维
date: 2020-07-24 13:59:23
---
## 共享工具类
```bash
Samba的端口是137、138,（UDP）139和445（TCP）  （共享目录,文件共享）  445（存储端口）

ftp的端口是20 21  20端口是传输数据的 21 是发起链接
   21端口用于控制连接
   20端口用于上传和下载数据

nfs基于rpc 的111端口     rpc（远程过程调用）SUN公司的RPC服务所有端口 常见RPC服务有rpc.mountd、NFS、rpc.statd、rpc.csmd、rpc.ttybd、amd等
```
## 数据库
```bash
mysql的端口是：3306

amoeba的端口是：8066

redis的端口是：6379

mongodb的端口是：27017

Oracle的端口是：1521 

SQLServer的端口是：1433 
```
## 远程工具
```bash
SSH的端口是：22 （远程链接）

Telnet的端口是：23   (以前是远程登陆       现在是远程测试端口是否开启    telnet  IP    端口 )
 
vnc的端口是：5900

远程桌面的端口是：3389 

QQ客户端的端口是：4000

QQ服务器的端口是：8000
```
## web网站
```bash
IIS(HTTP)的端口是：80

https的端口是：443

tomcat的端口是：8080

DNS的端口是：53
```



## 其他
```bash
邮件(SMTP)端口是：25 (公有云默认是禁止的,安全组放行后还需工单申请解封25端口)

Dhcp的端口是 （upd）67 68   67是用来接受客户请求分配IP 68 是向客户发请求成功或失败回应      管理IP  减少管理员工作  方便管理

RSYNC的端口是：873 

memcache的端口是：11211

zabbix的端口是：zabbix_server 默认10051 ,zabbix_agent默认 10050

Wingate的端口是：8010

WebLogic的端口是：7001
```