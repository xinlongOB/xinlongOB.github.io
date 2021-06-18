---
title: centos离线安装软件
tags:
  - linux
categories: 运维
date: 2021-06-18 11:15:28
---
```bash
# 找有外网的机器执行
yum install --downloadonly --downloaddir=/opt/http httpd

# 把/opt/http这个目录打包  上传    安装这个目录里面的所有包


localinstall
```
