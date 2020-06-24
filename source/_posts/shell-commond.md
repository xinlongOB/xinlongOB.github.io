---
title: shell命令行工具
tags:
  - liunx
categories: 运维
date: 2020-06-22 18:16:00
---
## sed
```shell
# 在第一个匹配行到第二个匹配行后各加 test     然后删除匹配到的第一个test
cat  aa  | sed   "/path/,/-->/a test"  |sed '0,/test/{/test/d}'     
```

docker build 失败(如果可以导入镜像但是除去FROM第一个指令就报错 那就是linux与docker版本的兼容性问题)

```bash
sudo yum remove docker  docker-common docker-selinux dockesr-engine
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce
sudo systemctl start docker
```