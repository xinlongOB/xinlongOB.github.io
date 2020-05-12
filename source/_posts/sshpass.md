---
title: sshpass
tags:
  - linux
  - sshpass
categories: 运维
date: 2019-12-27 15:09:20
---
### 使用前提：对于未连接过的主机。而又不输入yes进行确认。需要sshd服务的优化：
	
	# vim /etc/ssh/ssh_config   
	StrictHostKeyChecking no
	GSSAPIAuthentication no
	UseDNS no

	# service sshd restart
	
### sshpass 命令安装：
	
	# yum -y install sshpass

### sshpass的用法举例

	sshpass -p password ssh -o StrictHostKeyChecking=no lius@192.168.33.56 "ls /tmp"

	-p: 指定密码
	-o: ssh或scp的一个选项, StrictHostKeyChecking=no表示在第一次主机认证的时候, 自动接收远端主机密钥.

### 常用案例

	#!/bin/bash
	sshpass  -p password  ssh  -o  StrictHostKeyChecking=no  xxxx@IP  << restartserver
	cd   /subverison/data/
	svn update

	restartserver
