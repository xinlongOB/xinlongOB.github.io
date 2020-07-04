---
title: EFK-日志收集系统
tags:
  - liunx
categories: 运维
date: 2020-07-04 18:44:11
---
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