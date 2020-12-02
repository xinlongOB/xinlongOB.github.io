---
title: 自建mongo数据库迁移至云数据库MongoDB
tags:
  - mongo
  - linux
categories: 运维
date: 2020-09-11 16:45:18
---
## 创建云数据库MongoDB
首先 在云数据库MongoDB控制台创建实例  （建议是副本集群）

副本集和分片集的区别
```bash
    副本集相当于把一份数据被保存到N个机器上（相当于mysql数据的读写分离）
		分片集相当于把一份数据拆分为多份保存到N个机器上，合起来后一个份完整的数据（相当于raid阵列里的raid0）
```

选择实例（根据实际需求选择规格）

创建完云库后停止服务器

备份  
```bash
mongodump --host <mongodb_host> --port <port>  -u <username>  --authenticationDatabase  <database>
```

## 迁移至阿里云数据库MongoDB
 获取单节点实例primary节点的连接地址
    登录控制台
    选择目标实例所在地域
    在左侧导航栏，单机副本集实例列表
    单机目标实例ID
    在左侧导航栏，单击数据库连接，查看数据库连接信息
  操作到这一步就可以看到primary节点
 
在自建数据库服务器上执行以下语句将数据库数据全部迁移至阿里云数据库MongoDB
```bash
  mongorestore --host <Primary_host>  -u <username> --authenticationDatabase admin   -d  <database>    <Backup directory> 

说明：
    <Primary_host>：副本集实例中 Primary 节点的连接地址。
    <username>：登录阿里云MongoDB数据库的数据库账号，默认为 root。
    <database>：对登录阿里云MongoDB数据库的账号和密码，进行认证的鉴权数据库，默认为 admin 。
    <Backup directory>：备份文件存储目录，默认为 dump。（/data/backup/mongodb/`data`/）
```
实例：
```bash
   mongorestore --host   Primary 节点的连接地址     -u root --authenticationDatabase admin  -d  database_name    -o   bashdirname
   mongorestore  --host   Primary 节点的连接地址   -u root  --authenticationDatabase  admin    -d  云服务器数据库名     /backup/mongo/
```

输入密码后数据开始迁移



## Mongo Shell 连接云服务器

前提条件
```bash
    安装与MongoDB实例版本相对应的Mongo Shell版本
    已将客户端的IP地址加入到MongoDB实例的白名单中
```
命令
```bash
mongo --host <host> -u <username> -p --authenticationDatabase <database>
```
实例
```bash
mongo --host  Primary 节点的连接地址    -u root -p --authenticationDatabase admin    
```