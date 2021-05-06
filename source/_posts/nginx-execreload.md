---
title: 编译安装Nginx配置开机自启动
tags:
  - nginx
  - linux
categories: 运维
date: 2021-04-26 14:53:40
---
## Yum安装
如果使用yum安装的nginx 可以直接使用下面方法添加到开机自启动
```bash
chkconfig --level 235 nginx on 
# 或者
systemctl enable nginx
```
编译安装需要手动添加脚本

## 编译安装第一种方法
利用rc.local脚本   
    rc.local是启动加载文件,在linux中要把一个程序加入开机启动，一般可以通过修改rc.local来完成，这个文件时开机就要加载的文件，所以我们就可以利用linux这个文件设置nginx开机自启动
```bash
vim  /etc/rc.local 
# 在最后添加
    /usr/local/nginx/sbin/nginx
```
## 编译安装第二种方法
设置系统服务
    在/usr/lib/systemd/system路径下添加nginx.service文件
```bash
vim  /usr/lib/systemd/system/nginx.service 

    [Unit]
    Description=nginx       # 描述
    After=network.target    # 描述服务类别
    
    [Service] 
    Type=forking          # 设置运行方式，后台运行
    PIDFile=/usr/local/nginx/logs/nginx.pid   # 设置PID文件
    ExecStart=/usr/local/nginx/sbin/nginx     # 启动命令
    ExecReload=/usr/local/nginx/sbin/nginx reload   # 重启命令
    ExecStop=/usr/local/nginx/sbin/nginx quit   # 关闭命令
    PrivateTmp=true       # 分配独立的临时空间
    
    [Install] 
    WantedBy=multi-user.target   # 服务安装的相关设置，可设置为多用户
```
注意：此文件需要754的权限

设置开启自启动
```bash
chmod 754   nginx.service 
systemctl enable nginx
```
## 编译安装第三种方法
init.d设置开机启动
    init启动方式在centos7系统版本已经不推荐使用了,在/etc/init.d目录中创建启动文件nginx

```bash
#!/bin/bash
# chkconfig: 345 80 20    //启动顺序
# description: start the nginx deamon    //说明
# Source function library
. /etc/rc.d/init.d/functions

prog=nginx
# 根据自己的路径改写CATALANA_HOME
CATALANA_HOME=/usr/local/nginx
export CATALINA_HOME

case "$1" in
start)
echo "Starting nginx..."
$CATALANA_HOME/sbin/nginx
;;

stop)
echo "Stopping nginx..."
$CATALANA_HOME/sbin/nginx -s stop
;;

restart)
echo "Stopping nginx..."
$CATALANA_HOME/sbin/nginx -s stop
sleep 2
echo
echo "Starting nginx..."
$CATALANA_HOME/sbin/nginx
;;
*)
echo "Usage: $prog {start|stop|restart}"
;;
esac
exit 0
```
```bash
# chmod +x /etc/init.d/nginx
chmod +x /etc/init.d/nginx
```
在centos7中init.d中的服务默认也会在system目录中