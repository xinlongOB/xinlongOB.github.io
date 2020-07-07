---
title: docker--容器创建后添加端口映射
tags:
  - docker
  linux
categories: 运维
date: 2020-05-13 15:02:36
---

标注：[hash_of_the_container] 为容器id
  
    vim /var/lib/docker/containers/[hash_of_the_container]/hostconfig.json

在 hostconfig.json 里有 "PortBindings":{} 这个配置项，

改成 
  
    "PortBindings":{"9001/tcp":[{"HostIp":"","HostPort":"900"}]}
          前者为容器端口，后者为宿主机端口

如果容器内端口从没有暴露，需要在修改config.v2.json

    vim /var/lib/docker/containers/[hash_of_the_container]/config.v2.json

在 config.v2.json 里面添加一个配置项 

    "ExposedPorts":{"80/tcp":{}} ,
<font color=#FF0000 >必须将这个配置项添加到 "Tty": true, 前面</font>

最后重启 docker的守护进程 systemctl restart  docker
<br/>启动容器id   docker start   ID<br/>

使用docker ps  查看容器端口是否映射
![](../1.png)