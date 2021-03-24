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


报错
```bash
2020-11-04T09:00:19.923+0800 I CONTROL  [main] ERROR: Cannot write pid file to /var/run/mongodb/mongod.pid: No such file or directory
2020-11-04T09:13:58.322+0800 I CONTROL  [main] ***** SERVER RESTARTED *****
2020-11-04T09:13:58.329+0800 I CONTROL  [main] ERROR: Cannot write pid file to /var/run/mongodb/mongod.pid: No such file or directory
```
解决办法
```bash
 touch  /var/run/mongodb/mongod.pid
 chown -R mongodb:mongodb /var/run/mongodb/mongod.pid
```

mongo操作记录可以在log中查看
```bash
cat  /data/log/mongodb/mongod.log   |grep  dropDatabase
2021-03-11T22:56:41.750+0800 I COMMAND  [conn594] dropDatabase sgsm-game-1 starting
2021-03-11T22:56:42.261+0800 I COMMAND  [conn594] dropDatabase sgsm-game-1 finished
2021-03-11T22:56:42.261+0800 I COMMAND  [conn594] command sgsm-game-1 command: dropDatabase { dropDatabase: 1.0 } keyUpdates:0 writeConflicts:0 numYields:0 reslen:47 locks:{ Global: { acquireCount: { r: 2, w: 1, W: 1 } }, Database: { acquireCount: { W: 1 } } } protocol:op_command 511ms

# 或者
cat  /data/log/mongodb/mongod.log   |grep  find
2021-03-09T15:29:36.653+0800 I COMMAND  [conn34] command sgsm-game-1.gamedatas command: find { find: "gamedatas", filter: { dataID: 3 }, limit: 1, batchSize: 1, singleBatch: true } planSummary: IXSCAN { dataID: 1 } keysExamined:0 docsExamined:0 cursorExhausted:1 keyUpdates:0 writeConflicts:0 numYields:0 nreturned:0 reslen:127 locks:{ Global: { acquireCount: { r: 2 } }, Database: { acquireCount: { r: 1 }, acquireWaitCount: { r: 1 }, timeAcquiringMicros: { r: 119778 } }, Collection: { acquireCount: { r: 1 } } } protocol:op_query 119ms
```