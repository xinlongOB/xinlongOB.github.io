---
title: 编译安装Nginx报错
tags:
  - nginx
  - linux
categories: 运维
date: 2021-05-06 18:13:22
---
nginx安装成功后，输入命令nginx，启动成功。
但输入命令nginx -s reload 和 nginx -s stop都提示   无效的pid号
```bash
nginx: [error] invalid PID number "" in"/usr/local/nginx/logs/nginx.pid"
```

解决办法：
```bash
ps -aux | grep nginx
root     10140  0.0  0.1  46600  2288 ?        Ss   Apr26   0:00 nginx: master process ./nginx
sgsm     24477  0.0  2.4  90616 45940 ?        S    10:11   0:00 nginx: worker process
sgsm     24478  0.0  2.4  90616 45944 ?        S    10:11   0:00 nginx: worker process
root     24528  0.0  0.0 112808   968 pts/1    R+   10:16   0:00 grep --color=auto nginx
echo 10140 > /usr/local/nginx/logs/nginx.pid
```
此时可分别重新输入 nginx -s reload 、 nginx -s stop命令，重启成功、停止成功