---
title: Mongo常见报错
tags:
  - mongo
  - liunx
categories: 运维
date: 2020-05-14 15:04:47
---

mongod宕机常见报错

     /data/lib/mongo//WiredTiger.turtle: handle-open: open: Permission denied

  解决办法直接给权限
      
      sudo chown  mongod.mongod   ./*  -R

还有一种是非正常关闭mongo再次启动会失败  使用

    sudo  journalctl -xe

查看到报错

    Error starting mongod. /var/run/mongodb/mongod.pid exists.

是因为非正常关闭mongo的时候pid文件还存在，删除后启动就正常了


查看mongo连接数

    db.serverStatus().connections
    { "current" : 80, "available" : 52348, "totalCreated" : NumberLong(367) }


Current表示当前到实例上正在运行的连接数。
<br/>Available表示当前实例还可以支持的并发连接数。<br/>