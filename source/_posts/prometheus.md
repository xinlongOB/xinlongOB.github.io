---
title: prometheus+grafana监控搭建
tags:
  - prometheus
  - linux
categories: 运维
date: 2021-03-05 16:45:11
---
## 官网地址
源码包下载地址
prometheus: https://prometheus.io/
grafana：https://grafana.com/grafana/

监控模板--下载地址：https://grafana.com/grafana/dashboards/1860?pg=dashboards&plcmt=featured-sub1
![](../14.png)
## 监控端配置
下载源码包后上传到服务器
```bash
# 解压
tar xf prometheus-2.25.0.linux-amd64.tar.gz 
# 重命名
mv  prometheus-2.25.0.linux-amd64   prometheus
# 启动
cd prometheus
nohup  ./prometheus   &
# 查看端口是否被监听
lsof -i:9090
```
登录prometheus后台查看是否启动成功(默认端口9090)
![](../01.png)

没问题后安装grafana
```bash
# 下载源码包
wget https://dl.grafana.com/oss/release/grafana-7.4.3-1.x86_64.rpm
# 安装
yum install  grafana-7.4.3-1.x86_64.rpm   -y
# 启动
/etc/init.d/grafana-server  start
# 查看端口是否被监听
lsof -i:3000
```
登录grafana后台查看(默认端口3000)
![](../02.png)
添加数据来源
![](../03.png)
选择添加
![](../04.png)
选择prometheus然后点击select
![](../05.png)
输入名字以及prometheus的url
![](../06.png)
点击保存测试
![](../07.png)

## 被监控端配置
```bash
# 解压
tar xf prometheus-2.25.0.linux-amd64.tar.gz 
# 重命名
mv  prometheus-2.25.0.linux-amd64   prometheus
# 启动
cd prometheus
nohup  ./prometheus   &
# 查看端口是否被监听
lsof -i:9090
```
## 监控端添加被监控服务器
监控端配置文件添加被监控端
```bash
vim   prometheus.yml 
*******
      - job_name: 'Test'
    static_configs:
    - targets: ['192.168.1.104:9090']

# 添加被监控ip:端口  保存退出
```
![](../08.png)
重启prometheus
```bash
[root@centos6 prometheus]# ps aux | grep prometh
root     28752  0.0  0.7 1101676 61596 pts/1   Sl   00:56   0:00 ./prometheus
root     28777  0.0  0.0 103324   892 pts/1    S+   01:11   0:00 grep prometh
[root@centos6 prometheus]# kill  -9  28752 
[1]+  Killed                  nohup ./prometheus
[root@centos6 prometheus]# nohup   ./prometheus   &  
[1] 28778
[root@centos6 prometheus]# nohup: ignoring input and appending output to `nohup.out'
```
查看添加成功了并且状态是up
![](../09.png)


## grafana配置被监控端
选择import导入
![](../10.png)
选择上传JSON文件
![](../11.png)
选择之前下载好的json文件  
![](../12.png)
填写名字以及选择数据源
![](../13.png)
