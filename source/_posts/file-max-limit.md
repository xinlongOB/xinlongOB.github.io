---
title: file_max_limit
tags:
  - python
  - linux
date: 2021-06-29 19:28:11
---


如出现 "too many open files" 提示, 可能是单进程文件句柄限制 或者 inotify文件系统事件监控

## 单进程文件句柄限制
Linux 是有文件句柄限制的, 默认不高, 一般是 1024。作为生产环境的话很容易超出这个数值，因此大多数时候都需要调高该限制数。

文件句柄数限制是针对：

    针对单个进程的限制
    修改该值时不影响当前运行中的程序

查看当前句柄数限制： ulimit -n

```bash
# 临时修改句柄数限制
ulimit -n  4096
# 写入配置文件 (永久生效)
vim /etc/security/limits.conf
    
    *   hard    nofile  65535
    *   soft    nofile  65535
# hard 是硬限制，即实际限制 ；soft 是软限制，即warning
```

分析句柄数
```bash

（1）统计各进程打开句柄数：lsof -n|awk '{print $2}'|sort|uniq -c|sort -nr

（2）统计各用户打开句柄数：lsof -n|awk '{print $3}'|sort|uniq -c|sort -nr

（3）统计各命令打开句柄数：lsof -n|awk '{print $1}'|sort|uniq -c|sort -nr
```

## inotify 文件系统事件监控
报错： Caught exception: Error: watch EMFILE

项目使用 pomelo，”reloadHandlers会监听handler文件的修改，导致了这个fs.watch这个问题

inotify 是linux的文件系统时间监控机制。

在/proc/sys/fs/inotify目录下有三个文件，对inotify机制有一定的限制

    max_user_watches:设置inotifywait或inotifywatch命令可以监视的文件数量（单进程）
    max_user_instances:设置每个用户可以运行的inotifywait或inotifywatch命令的进程数
    max_queued_events:设置inotify实例事件（event）队列可容纳的事件数量。


临时修改该参数配置：
```bash
echo 8192 > /proc/sys/fs/inotify/max_user_instances
```
参数持久化
```bash
echo "fs.inotify.max_user_instance=8192" >> /etc/sysctl.conf
sysctl -p
```


## 系统总文件句柄限制
除了对单进程可打开的文件句柄数量限制外, linux还会对系统级别的能够打开的文件句柄的数量进行限制, 即是对整个系统的限制, 而非针对单用户或单进程.

查看当前系统级别能够打开的文件句柄数量:
```bash
cat /proc/sys/fs/file-max   # 794168 Centos7 的默认值
```

系统级打开最大文件句柄的数量永久生效的修改方法:
```bash
# /etc/sysctl.conf 文件末尾追加

vim  /etc/sysctl.conf
  fs.file-max = 2000000

# 执行 sysctl -p ，使修改配置立即生效
# -p 从指定的文件加载系统参数，如不指定即从/etc/sysctl.conf中加载
```
查看系统已分配句柄数
```bash
cat /proc/sys/fs/file-nr
```