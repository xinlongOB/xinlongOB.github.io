---
title: cacti创建设备error解决
tags:
  - cacti
  - linux
categories: 运维
date: 2020-08-08 16:53:27
---
## 添加设备报错
如何解决cacti的snmp error
第一,确定cacti所有的主机能ping通被监控主机；如果不能ping通,请确认网络配置和被监控主机的ip设置是否正确。

第二,如果能ping通,那么确认被监控主机是否启用snmpd服务：
```bash
ps -ef | grep snmp
```
或者直接重启被监控主机的snmp服务：
```bash
systemctl restart snmpd.service 
```
然后到cacti服务器上,用root用户：
```bash
snmpwalk -c public -v 2c 192.168.124.14    --> (这个ip为被监控主机的ip)
```
如果能够接收到被监控机器的数据信息,则表示被监控主机的snmp配置已经完成,没有错误。如果没有接收到被监控主机的数据信息,那么进行第三步操作。

第三,用root登录被监控主机,修改snmp的配置文件：
```bash
vi /etc/snmp/snmpd.conf

# 最后配置如下：

syslocation Server Room

syscontact Sysadmin (root@localhost)

rocommunity public 127.0.0.1

agentaddress 161

rocommunity public

rwcommunity private

trapsink 192.168.124.14 public 162     --> 这里的ip=192.168.124.14为被监控主机ip
```
然后,再执行第二步操作即可。

## unkuow状态
```bash
/usr/bin/php /usr/share/cacti/poller.php  --force
```