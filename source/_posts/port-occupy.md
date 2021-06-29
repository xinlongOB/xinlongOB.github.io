---
title: 修改内核参数ip_local_reserved_ports避免tomcat端口占用
tags:
  - linux
categories: 运维
date: 2021-06-29 19:26:11
---
问题描述：
tomcat 重启时候 遇到这个情况，出现60080端口被占用而无法启动，需要等该端口释放后才启动成功。

问题分析：
60080端口被该服务器上的客户端(dubbo motan)随机选取源端口给占用掉了。

解决方案：
使用net.ipv4.ip_local_port_range参数，规划出一段端口段预留作为服务的端口，这种方法是可以解决当前问题，但是会有个问题，端口使用量减少了，当服务器需要消耗大量的端口号的话，比如反代服务器，就存在瓶颈了。
最好的做法是将服务监听的端口以逗号分隔全部添加到ip_local_reserved_ports中，TCP/IP协议栈从ip_local_port_range中随机选取源端口时，会排除ip_local_reserved_ports中定义的端口，因此就不会出现端口被占用了服务无法启动。
```bash
# vim /etc/sysctl.conf
net.ipv4.ip_local_reserved_ports = 18080-18087, 60080-60087
# sysctl -p
```
注意：内核版本要大于2.6.18-164，否则不支持该参数。 ubuntu12.04 内核版本3.8.0-29 所以ubuntu12.04以上版本是支持的