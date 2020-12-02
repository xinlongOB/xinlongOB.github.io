---
title: centos中history命令显示时间以及用户
tags:
  - history
  - linux
categories: 运维
date: 2020-09-14 10:32:30
---
在 /etc/profile 文件中添加
```bash
USER_IP=`who -u am i 2>/dev/null| awk '{print $NF}'|sed -e 's/[()]//g'`  
export HISTTIMEFORMAT="[%F %T][`whoami`][${USER_IP}]"
```