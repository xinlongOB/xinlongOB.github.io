---
title: EFK-日志收集系统
tags:
  - efk
  - linux
categories: 运维
date: 2020-07-04 18:44:11
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
![](../91.png)
## 安装准备
软件下载地址：https://www.elastic.co/cn/downloads/
<br/>![](../1.png)<br/>

## 安装Java JDK
Elasticsearch需要运行在Java 8 及以上，所以需要先安装Java8
```bash
[root@localhost bak]# rpm -ivh  jdk-8u251-linux-x64.rpm 
# 安装后查看
[root@localhost bak]# java -version
java version "1.8.0_251"
Java(TM) SE Runtime Environment (build 1.8.0_251-b08)
Java HotSpot(TM) 64-Bit Server VM (build 25.251-b08, mixed mode)
[root@localhost bak]# 
```
## 安装Elasticsearch
下载Elasticsearch,本文以elasticsearch-7.8.0为例
<br/>注意：Elasticsearch、Kibana、FileBeat一定要使用相同的版本<br/>

```bash
[root@localhost bak]# rpm -ivh  elasticsearch-7.8.0-x86_64.rpm 
警告：elasticsearch-7.8.0-x86_64.rpm: 头V4 RSA/SHA512 Signature, 密钥 ID d88e42b4: NOKEY
准备中...                          ################################# [100%]
Creating elasticsearch group... OK
Creating elasticsearch user... OK
正在升级/安装...
   1:elasticsearch-0:7.8.0-1          ################################# [100%]
### NOT starting on installation, please execute the following statements to configure elasticsearch service to start automatically using systemd
 sudo systemctl daemon-reload
 sudo systemctl enable elasticsearch.service
### You can start elasticsearch service by executing
 sudo systemctl start elasticsearch.service
Created elasticsearch keystore in /etc/elasticsearch/elasticsearch.keystore
```
修改配置
```bash
vim /etc/elasticsearch/elasticsearch.yml

    network.host: 192.168.1.90  # 如果这里填写ip 或者 0.0.0.0   必须开启 discovery.seed_hosts 这个字段 里面填写ip
    http.port: 9200
    # 第68行取消注释 并且填写ip
    discovery.seed_hosts: ["192.168.1.90"]
    # cluster.initial_master_nodes: ["node-1"]   # 这里的node-1为node-name配置的值 (可以不加)
```
启动elasticsearch
```bash
/etc/init.d/elasticsearch start
```
测试
```bash
[root@localhost ~]# curl 127.0.0.1:9200     # 会得到类似以下json
{
  "name" : "localhost.localdomain",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "0xnIbrkaTbKoKFvzEyopgA",
  "version" : {
    "number" : "7.8.0",
    "build_flavor" : "default",
    "build_type" : "rpm",
    "build_hash" : "757314695644ea9a1dc2fecd26d1a43856725e65",
    "build_date" : "2020-06-14T19:35:50.234439Z",
    "build_snapshot" : false,
    "lucene_version" : "8.5.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}

[root@localhost filebeat]# curl  http://192.168.1.90:9200   # 如果filebeat和es不是同一台服务器  需要 curl IP 可以访问
{
  "name" : "localhost.localdomain",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "96iE491zTf63NOGHpyityg",
  "version" : {
    "number" : "7.8.0",
    "build_flavor" : "default",
    "build_type" : "rpm",
    "build_hash" : "757314695644ea9a1dc2fecd26d1a43856725e65",
    "build_date" : "2020-06-14T19:35:50.234439Z",
    "build_snapshot" : false,
    "lucene_version" : "8.5.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
[root@localhost filebeat]# 
```

## 安装Kibana
```bash
[root@localhost ~]# rpm -ivh kibana-7.8.0-x86_64.rpm 
警告：kibana-7.8.0-x86_64.rpm: 头V4 RSA/SHA512 Signature, 密钥 ID d88e42b4: NOKEY
准备中...                          
################################# [100%]

正在升级/安装...
   1:kibana-7.8.0-1                   ########                          ( 25%)
```
修改配置
```bash
vim /etc/kibana/kibana.yml 
    # 添加配置
    elasticsearch.hosts: ["http://localhost:9200/"]
    server.host: "192.168.1.90"
    server.port: 5601
    kibana.index: ".kibana"

    # 最后一行配置语言
    # Supported languages are the following: English - en , by default , Chinese - zh-CN .
    i18n.locale: "zh-CN"  # zh-CN 为中文     默认的  en  英文
```
启动kibana
```bash
 /etc/init.d/kibana start      # 这个版本的kibana的restart 和stop   不好使   重启的时候看下进程是否被杀掉
 # 查看端口是否被监听
 [root@localhost bak]# lsof -i:5601                                       
COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
node    6223 kibana   18u  IPv4  51993      0t0  TCP localhost.localdomain:esmagent (LISTEN)
```
![](../2.png)
## 安装FileBeat
```bash
[root@localhost bak]# rpm  -ivh  filebeat-7.8.0-x86_64.rpm 
警告：filebeat-7.8.0-x86_64.rpm: 头V4 RSA/SHA512 Signature, 密钥 ID d88e42b4: NOKEY
准备中...                          ################################# [100%]
正在升级/安装...
   1:filebeat-7.8.0-1                 ################################# [100%]
[root@localhost bak]# 
```
修改配置
```bash
vim  /etc/filebeat/filebeat.yml 
    # 找到以下配置取消注释
filebeat.inputs:   # filebeat 配置项
    - type: log
        enabled: true # 默认为false  改为true

        paths:
          - /etc/httpd/logs/*.log  # 定义日志位置

        # 兼容多行日志的情况
        multiline.negate: false
        multiline.match: after
        path: ${path.config}/modules.d/*.yml   # 配置仪表盘需要开启这个选项
        reload.enabled: false

setup.kibana:      # 连接kibana的配置项
    host: "192.168.1.90:5601"

output.elasticsearch:     # 连接es的配置项
    hosts: ["192.168.1.90:9200"]
```
启动filebeat
```bash
 /etc/init.d/filebeat start    
```
## 后台访问(192.168.1.90:5601)
<br/>![](../2.png)<br/>
在HOME中点击链接 elasticsearch 索引
<br/>![](../3.png)<br/>
里面会有filebeat-版本号的索引   输入filebeat 然后点击next
<br/>![](../4.png)<br/>
格式选择 timestamp  最后点击创建
<br/>![](../7.png)<br/>
创建后的界面
<br/>![](../8.png)<br/>
点击
<br/>![](../9.png)<br/>
选择时间和索引
<br/>![](../12.png)<br/>
点击添加message
<br/>![](../13.png)<br/>
格式显示
<br/>![](../15.png)<br/>
筛选日志 先把这个Kibana 查询语言(KQL) 关闭
<br/>![](../33.png)<br/>
![](../16.png)
<br/>![](../17.png)<br/>


## 报错
如果在创建索引的时候没有filebeat这个索引,就重启下filebeat 、 elasticsearch   和  kibana(如果kibana 不重启会读取elasticsearch 失败    正常启动的时候 也要先启动elasticsearch )
<br/>elasticsearch 的日志会有添加 模板的日志<br/>
![](../99.png)

首次配置filebeat后 启动的时候 查看 elasticsearch的日志  会显示
<br/>![](../88.png)<br/>

## 附件(配置文件)

```bash
[root@localhost bak]# cat /etc/kibana/kibana.yml |grep -v ^[[:blank:]]*#  |grep -v ^$
elasticsearch.hosts: ["http://192.168.1.90:9200"]
server.host: "192.168.1.90"
server.port: 5601
i18n.locale: "zh-CN"
```
```bash
[root@localhost bak]# cat /etc/elasticsearch/elasticsearch.yml |grep -v ^[[:blank:]]*#  |grep -v ^$                          
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
network.host: 0.0.0.0
http.port: 9200
discovery.seed_hosts: ["192.168.1.90"]
```
```bash
[root@localhost bak]# cat /etc/filebeat/filebeat.yml |grep -v ^[[:blank:]]*#  |grep -v ^$                                   
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /etc/httpd/logs/*.log
  multiline.pattern: ^\[
  multiline.negate: false
  multiline.match: after
filebeat.config.modules:
  path: ${path.config}/modules.d/*.yml
  reload.enabled: false
setup.template.settings:
  index.number_of_shards: 1
setup.kibana:
  host: "192.168.1.90:5601"
output.elasticsearch:
  hosts: ["192.168.1.90:9200"]
processors:
  - add_host_metadata: ~
  - add_cloud_metadata: ~
  - add_docker_metadata: ~
  - add_kubernetes_metadata: ~
```