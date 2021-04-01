---
title: Nginx如何识别多个conf配置文件
tags:
  - nginx
  - linux
categories: 运维
date: 2021-04-01 19:30:58
---
在主配置文件添加include
```bash
http {
	server {
		......省略......
	}
	include /etc/nginx/conf.d/*.conf;  # 正则识别
	include /etc/nginx/sites-enabled/*;
}
```