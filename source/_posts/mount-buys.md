---
title: centos下mount挂载报错buys
tags:
  - mount
  - linux
date: 2021-01-05 17:18:33
---
## 在单用户模式下无法修改文件--提示只读
```bash
mount -o remount ,rw /
```
这样把根目录磁盘重新挂载下就可以修改了 



## 查看设备被占用的进程
```bash
fuser -m  /dev/sdb1  # 查到进程pid   没有命令的话  yum install psmisc  -y

ps -aux  |grep  $pid  

kill -9 $pid   # 结束进程

mount  /dev/sdb1  /test  # 重新挂载
```


## 修复磁盘
```bash
fsck -t ext4  -r /dev/sdb1
```
