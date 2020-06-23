---
title: 史上最全breeze安装k8s文档
tags:
  - kubernetes
  - Linux
categories: 运维
date: 2020-05-21 15:34:40
---
在我们的实验环境中准备了四台服务器，配置与角色如下（如果需要增加 Minion/Worker 节点请自行准备即可）：
![](../1.png)


## 步骤：
## 一、准备部署主机（deploy / 192.168.9.10）
（1）以标准 Minimal 方式安装 CentOS 7.6 (1810) x64 之后(7.4 和 7.5 也支持)，登录 shell 环境，执行以下命令关闭防火墙：		
setenforce 0

    systemctl stop firewalld.service

    systemctl stop iptables.service

    systemctl disable firewalld.service

    systemctl disable iptables.service

（2）安装 docker-compose 命令

    curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose

    chmod +x /usr/local/bin/docker-compose

（3）安装 docker

    yum install docker
    systemctl enable docker && systemctl start docker

（4）建立部署主机到其它所有服务器的 ssh 免密登录途径
	a) 生成秘钥，执行：

    	ssh-keygen

	b) 针对目标服务器做 ssh 免密登录，依次执行：

	    ssh-copy-id 192.168.1.100
	    ssh-copy-id 192.168.1.101
	    ssh-copy-id 192.168.1.102
	    ssh-copy-id 192.168.1.103
	    ssh-copy-id 192.168.1.104
	
## 二、获取针对 K8S 某个具体版本的 Breeze 资源文件并启动部署工具，例如此次实验针对刚刚发布的 K8S v1.13.1

	  curl -L https://raw.githubusercontent.com/wise2c-devops/breeze/v1.13.1/docker-compose.yml -o docker-compose.yml

  如果无法访问此网站，复制一下内容到文件中命名为   docker-compose.yml

      vim   docker-compose.yml

        version: '2'
        services:
          deploy:
            container_name: deploy-main
            image: registry.cn-shenzhen.aliyuncs.com/breeze-project/pagoda:v1.2.0
            restart: always
            entrypoint: sh
            command:
            - -c
            - "/root/pagoda -logtostderr -v 4 -w /workspace"
            ports:
            - 88:80
            - 8088:8080
            volumes:
            - $HOME/.ssh:/root/.ssh
            - $PWD/deploy:/deploy
            volumes_from:
            - playbook
          ui:
            container_name: deploy-ui
            image: registry.cn-shenzhen.aliyuncs.com/breeze-project/deploy-ui:v1.8
            restart: always
            network_mode: "service:deploy"
          playbook:
            container_name: deploy-playbook
            image: registry.cn-shenzhen.aliyuncs.com/breeze-project/playbook:v1.18.2
            volumes:
            - playbook:/workspace
          yum-repo:
            container_name: deploy-yumrepo
            image: registry.cn-shenzhen.aliyuncs.com/breeze-project/yum-repo:v1.18.2
            ports:
            - 2009:2009 
            restart: always
        volumes:
          playbook:
            external: false
	
## 三、访问部署工具的浏览器页面(部署机 IP 及端口 88)，开始部署工作

  http://192.168.1.199:88