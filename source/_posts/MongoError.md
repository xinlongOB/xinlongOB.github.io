---
title: Mongo常见报错
tags:
  - mongo
  - linux
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


锁文件报错

        2020-06-20T19:55:41.371+0800 W -        [initandlisten] Detected unclean shutdown - /data/lib/mongo/mongod.lock is not empty.

解决办法

        sudo  rm   /data/lib/mongo/mongod.lock

最大连接数报错

        [1592654142:807610][13609:0x7fb0fc34bdc0], file:collection-1898--8679891645894746372.wt, WT_SESSION.open_cursor: /data/lib/mongo//collection-1898--8679891645894746372.wt: handle-open: open: Too many open files
        2020-06-20T19:55:42.807+0800 I -        [initandlisten] Invariant failure: ret resulted in status UnknownError: 24: Too many open files at src/mongo/db/storage/wiredtiger/wiredtiger_session_cache.cpp 79

解决办法

        ulimit -n 4096


查看mongo连接数

    db.serverStatus().connections
    { "current" : 80, "available" : 52348, "totalCreated" : NumberLong(367) }


Current表示当前到实例上正在运行的连接数。
<br/>Available表示当前实例还可以支持的并发连接数。<br/>