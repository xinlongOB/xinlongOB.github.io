---
title: jenkins项目迁移
tags:
  - jenkins
  - linux
categories: 运维
date: 2019-12-24 17:20:58
---
	
	systemctl stop jenkins
	cp -rp /var/lib/jenkins /home/jenkins
	sed -i s'@/var/lib/jenkins@/home/jenkins@' /etc/sysconfig/jenkins #修改主目录
	systemctl start jenkins
	rm -rf /var/lib/jenkins

