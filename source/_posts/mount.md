---
title: 服务器无法卸载磁盘
tags:
  - mount
  - linux
categories: 运维
date: 2020-10-21 10:59:28
---
原因：服务器突然无法访问，检查发现mongo数据库服务未开启，尝试start失败，错误提示：
```bash
Read-only file system 
```
检查发现 / 目录下所有文件都无法写入或者使用
尝试卸载
```bash
umount  / 
```
没有报错但是df -h 查看还是挂载状态

关机重新插磁盘接口以及主板接口,启动后还是显示挂载状态并且无法卸载

最后尝试重新挂载
```bash
mount -o  remount  /
```
> 网上有文章说使用 mount -o  remount  rw /    加上rw 表示读写   但是尝试无果

重新挂载后可以使用其他命令,修改/etc/fstab  重启服务器恢复正常状态
