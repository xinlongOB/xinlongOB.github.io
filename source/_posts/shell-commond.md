---
title: shell命令行工具
tags:
  - linux
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

## ssh登录失败
购买云服务器vpc网络的时候,第一次购买后配置使用,使用后退租,再次购买可能会购买到相同的内网ip服务器,然后ssh 连接的时候就会报错,如下：
<br/>![](../1.png)<br/>
报错问题:这个ip已存在 .ssh/known_hosts 文件中,但是上次连接的秘钥,和这次不同,所以无法登陆
<br/>解决办法：<br/>
编辑 .ssh/known_hosts 文件 找到 有问题的ip,删除哪一行登陆信息就ok了
