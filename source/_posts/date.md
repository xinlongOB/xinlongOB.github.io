---
title: centos下时间戳转换
tags:
  - date
  - linux

date: 2020-12-01 16:29:08
---
当前时间戳转换
```bash
date +%s
```
指定时间戳转换
```bash
date -d "2008-01-01 00:00:00" +%s  
```

修改时间
```bash
date -s "2020-12-01 10:00:00"
```

修改时区
```bash
#   CST时区
echo $passwd  |  sudo -S rm  /etc/localtime
echo $passwd  |  sudo -S ln -s /usr/share/zoneinfo/Asia/Shanghai    /etc/localtime
echo "当前时间是`date`"

#  JST时区
echo $passwd  |  sudo -S rm  /etc/localtime
echo $passwd  |  sudo -S ln -s /usr/share/zoneinfo/Asia/Tokyo    /etc/localtime
```