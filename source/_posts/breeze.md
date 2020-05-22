---
title: 史上最全breeze安装k8s文档
tags:
  - kubernetes
  - liunx
date: 2020-05-21 15:34:40
---
在我们的实验环境中准备了四台台服务器，配置与角色如下（如果需要增加 Minion/Worker 节点请自行准备即可）：
主机名   	  IP地址      	  角色                  OS                   组件                       
deploy   	 192.168.9.10 	BreezeDeploy 		  CentOS7.6 x64    docker / docker-compose / Breeze
master01 	 192.168.9.11 	K8S Master Node 	CentOS7.6 x64    K8S Master /etcd /HAProxy /Keepalived
master02 	 192.168.9.12 	K8S Master Node 	CentOS7.6 x64    K8S Master /etcd /HAProxy /Keepalived
master03 	 192.168.9.13 	K8S Master Node 	CentOS7.6 x64    K8S Master /etcd /HAProxy /Keepalived
worker01 	 192.168.9.21 	K8S Worker Node 	CentOS7.6 x64    K8S Worker / Prometheus
harbor 	 	 192.168.9.20 	Harbor 		  		  CentOS7.6 x64    Harbor 1.7.0
		       192.168.9.30 	VIP 								               HA 虚 IP 地址在 3 台 K8S Master 浮动  