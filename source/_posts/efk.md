---
title: EFK-日志收集系统(源码低版本)
tags:
  - efk
  - linux
categories: 运维
date: 2020-07-03 15:12:31
---
## 前言
在没有分布式日志的时候，每次出问题了需要查询日志的时候，需要登录到Linux服务器，使用命令cat -n xxxx|grep xxxx 搜索出日志在哪一行，然后cat -n xxx|tail -n +n行|head -n 显示多少行，这样不仅效率低下，而且对于程序异常也不方便查询，日志少还好，一旦整合出来的日志达到几个G或者几十G的时候，仅仅是搜索都会搜索很长时间了，当然如果知道是哪天什么时候发生的问题当然也方便查询，但是实际上很多时候有问题的时候，是不知道到底什么时候出的问题，所以就必须要在聚合日志中去搜索（一般日志是按照天来分文件的，聚合日志就是把很多天的日志合并在一起，这样方便查询），而搭建EFK日志分析系统的目的就是将日志聚合起来，达到快速查看快速分析的目的，使用EFK不仅可以快速的聚合出每天的日志，还能将不同项目的日志聚合起来，对于微服务和分布式架构来说，查询日志尤为方便，而且因为日志保存在Elasticsearch中，所以查询速度非常之快

## 认识EFK
EFK不是一个软件，而是一套解决方案，并且都是开源软件，之间互相配合使用，完美衔接，高效的满足了很多场合的应用，是目前主流的一种日志系统。EFK是三个开源软件的缩写，分别表示：Elasticsearch , FileBeat, Kibana , 其中ELasticsearch负责日志保存和搜索，FileBeat负责收集日志，Kibana 负责界面,当然EFK和大名鼎鼎的ELK只有一个区别，那就是EFK把ELK的Logstash替换成了FileBeat，因为Filebeat相对于Logstash来说有2个好处：
1、侵入低，无需修改程序目前任何代码和配置
2、相对于Logstash来说性能高，Logstash对于IO占用很大

当然FileBeat也并不是完全好过Logstash，毕竟Logstash对于日志的格式化这些相对FileBeat好很多，FileBeat只是将日志从日志文件中读取出来，当然如果你日志本身是有一定格式的，FileBeat也可以格式化，但是相对于Logstash来说，还是差一点

Elasticsearch
```bash
Elasticsearch是个开源分布式搜索引擎，提供搜集、分析、存储数据三大功能。它的特点有：分布式，零配置，自动发现，索引自动分片，索引副本机制，restful风格接口，多数据源，自动搜索负载等。
```
FileBeat
```bash
Filebeat隶属于Beats。目前Beats包含六种工具：
Packetbeat（搜集网络流量数据）
Metricbeat（搜集系统、进程和文件系统级别的 CPU 和内存使用情况等数据）
Filebeat（搜集文件数据）
Winlogbeat（搜集 Windows 事件日志数据）
Auditbeat（ 轻量型审计日志采集器）
Heartbeat（轻量级服务器健康采集器）
```
Kibana
```bash
Kibana可以为 Logstash 、Beats和 ElasticSearch 提供的日志分析友好的 Web 界面，可以帮助汇总、分析和搜索重要数据日志。
```
## EFK架构图
![](../9.png)
## 安装Java JDK
```bash
	环境：centos7.2
	软件包：jdk-8u60-linux-x64.tar.gz
```
首先关闭selinux和防火墙
<br/>![](../1.png)<br/>
更改时间 	 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;--可以写入计划任务中
<br/>![](../3.png)<br/>
创建目录   

	mkdir /application/

<br/>解压jdk包到创建的目录中<br/>

	tar xf jdk-8u60-linux-x64.tar.gz   -C /application/

<br/>做软连接<br/>
	
	ln -s  /application/jdk1.8.0_60/ /application/jdk

<br/>设置环境变量<br/>

	sed -i.ori '$a export  JAVA_HOME=/application/jdk\nexport PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH\nexport  CLASSPATH=.$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$JAVA_HOME/lib/tools.jar'  /etc/profile

<br/>source一下生效环境变量<br/>
![](../2.png)
<br/>![](../4.png)<br/>

## 安装Elasticsearch
下载Elasticsearch，本文以Elasticsearch6.2.4为例，当前Elasticsearch最新版本为Elasticsearch6.4.0
注意：Elasticsearch、Kibana、FileBeat一定要使用相同的版本
```bash
cd /opt/
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.4.tar.gz
#  解压
tar xf  elasticsearch-6.2.4.tar.gz
```
进入Elasticsearch主目录，修改配置
```bash
cd  elasticsearch-6.2.4
vim config/elasticsearch.yml
    # 添加以下配置，或者将对应的配置注释取消修改
    network.host: 0.0.0.0 
    http.port: 9200    
```
由于Elasticsearch不能使用root用户打开，所以需要专门创建一个用户来启动Elasticsearch
```bash
useradd  elastic     # 创建用户
echo password | passwd --stdin  elastic    # 修改密码
chown  elastic.elastic   /opt/elasticsearch-6.2.4    -R   # 授权
```
创建的用户名为elastic，其中/opt/elasticsearch-6.2.4为解压出来的Elasticsearch主目录

启动Elasticsearch
```bash
su - elastic  
cd   /opt/elasticsearch-6.2.4/  
 ./bin/elasticsearch    # 后台启动   nohup   ./bin/elasticsearch  &
```
启动后执行
```bash 
curl 127.0.0.1:9200  
```
会得到类似以下的json
```bash
{
  "name" : "tAerM69",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "9aSnOwH8S2ySy8F4BeSblw",
  "version" : {
    "number" : "6.2.4",
    "build_hash" : "ccec39f",
    "build_date" : "2018-04-12T20:37:28.497551Z",
    "build_snapshot" : false,
    "lucene_version" : "7.2.1",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

## 报错
如果遇到错误：[1]: max file descriptors [10240] for elasticsearch process is too low, increase to at least [65536]
```bash
vim /etc/security/limits.conf  
    # 如果存在就修改为65536   如果不存在直接添加
    * soft nofile 65536
    * hard nofile 65536
```
如果遇到错误： max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```bash
vim /etc/sysctl.conf
    # 添加配置
    vm.max_map_count=262144
```
需要执行 sysctl -p   立即生效

## 安装Kibana
```bash
cd /opt/
wget https://artifacts.elastic.co/downloads/kibana/kibana-6.2.4-linux-x86_64.tar.gz
# 解压
tar xf kibana-6.2.4-linux-x86_64.tar.gz
```
进入主目录，修改配置
```bash
cd   kibana-6.2.4-linux-x86_64/
vim config/kibana.yml 

    elasticsearch.url: "http://localhost:9200"   # Elasticsearch的地址
    server.host: "0.0.0.0"
    kibana.index: ".kibana"
```
其中elasticsearch.url为Elasticsearch的地址，server.host默认是localhost，如果只是本地访问可以默认localhost，如果需要外网访问，可以设置为0.0.0.0

启动Kibana
```bash
./bin/kibana   #  后台运行  nohup  ./bin/kibana  &
```
