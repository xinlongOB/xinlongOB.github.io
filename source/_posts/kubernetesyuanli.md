---
title: kubernetes服务原理
tags:
  - kubernetes
  - liunx
categories: 运维
date: 2020-06-06 14:22:05
---
## 容器编排系统的具体任务

    服务注册和服务发现
    负载均衡
    配置和存储管理
    健康状态监测
    自动扩容、缩容、重启
    0宕机部署

## 容器编排系统工具

    kubernetes
    docker swarm
    apache mesos and marathon

## 此篇文档主要介绍kubernetes
<br/>kubernetes是一个开源的平台、自动部署伸缩、自动运维容器化应用平台，支持跨主机的集群多节点。<br/>

kubernetes集群节点由master和node以及插件组成
<br/>多个master是为了冗余<br/>
而node节点就是工作节点

## master组成部分
  API server api入口 是一个数据  负责接受用户的请求 语法没问题 放到etcd
  scheduler	调度器  查看那台服务器适合执行node 会一直watch apiserver上是否有新建资源
  <br/>  controller	控制器（一直循环 也成控制循环器）  负责让调度器调用镜像启动容器，确保容器正常运行，自动重启 重启失败直接干掉  controller 会一直watch apiserver上的资源变动 如果有变动  会立即执行用户的请求 <br/> 
  还有个额外的etcd  会存储各种k v 规范数据   规范是apisever定义的

## node组成部分
  kubelet 一直watch apiserver  如果需要创建容器 会去调用docker docker去调用region（镜像仓库）
   <br/>  docker 容器  <br/> 
  pod   容器外壳，一个pod中可以存在多个容器
   <br/>  proxy  <br/> 

kubernetes 运行的核心基本单元是pod(原子单元)而不是容器

## 扩展
docker容器是利用内核的六种名称空间技术 来实现程序运行环境的隔离
<br/>pid  网络  文件系统  ipc  user uts（域名和主机名）<br/>

docker的四种网络模型  

    封闭网络   closed
    桥接网络   bridge
    联盟网络 joined（两个宿主机共享network 和 ipc uts） 
    共享宿主机  host

## kubernetes常用的资源类型
<br/>pod  service（服务）  namespace（名称空间） volume（存储卷）<br/>

## kubernetes的各种IP
service（服务）  客户端访问的不是pod_ip 而是访问的service_ip 这样pod宕机后被移除，新添加的pod的ip会是新分配的
<br/>pod_ip  每一个pod都有一个虚拟ip<br/>
service_ip  通过标签选择来管理pod_ip   
<br/>DNS  主要管理service_ip 如果 service被意外删除 或者其他情况无法使用  调度器会立即创建新的service_ip  dns动态获取A记录<br/>
node_ip  是节点网卡的ip

访问流程

      客户端访问  需要先访问service_ip  然后service_ip 访问pod组件 而pod组件是pod的管理器创建的
      例如：客户端访问nginx    会先访问service_ip 然后service_ip转到nginx的pod       nginx_pod 是由pod控制器的管理创建的   
          nginx在访问tomcat的service_ip    tomcat的service_ip 在转到tomcat tomcat_pod也有一个控制器

kubernetes有三种网络：

    节点网络
    pod网络  每个pod都是想通的
    service网络

部署

  测试环境
    
    可以使用单点master节点，单etcd实例，node节点按需而定，nfs或者glusterfs等存储系统

  生产环境

      高可用etcd，建立3、5或者7个节点
      高可用master
          kube-apiserver 无状态，可多实例
			        借助keepalived进行vip流动实现多实例冗余
			        或在多实例前端通过haproxy或者nginx反代，并借助于keepalived对代理服务器进行冗余
		  kubu-scheduler及kube-controller-manager各自只能有一个活动实例，但可以有多个备用
			        各自自带leader选举的功能，并且默认处于启动状态
        多node主机，数量越多，冗余能力越强
        ceph，glusterfs，iscsi， fc san及各种云存储等

  常用的部署环境

      iaas公有云环境：aws，gce，azure等
      iaas私有云或者公有云环境，OpenStack和vsphere等
      物理服务器或者独立的虚拟机等

  常用的部署工具

      kubeadm(官方工具)
      kops(aws专用工具)
      kubespray
      kontena Pharos

  其他二次封装的常用发行版

      Rancher
      Tectonic
      Openshift(redhat公司k8s发行版)

## 安装文档
[使用kubeadm安装kubernetes](https://xinlong.youare.ink/2020/05/22/kubeadm/)