---
title: Centos 6无法使用yum解决办法
tags:
  - yum
  - linux
categories: 运维
date: 2020-12-17 19:48:16
---
## 文章借鉴[链接](https://www.xmpan.com/944.html)

12月后Centos 6 系统无法使用yum出现错误

相信已经有一部分朋友今天连接到CentOS 6的服务器后执行yum后发现报错，那么发生了什么？

CentOS 6已经随着2020年11月的结束进入了EOL（Reaches End of Life），不过有一些老设备依然需要支持，CentOS官方也给这些还不想把CentOS 6扔进垃圾堆的用户保留了最后一个版本的镜像，只是这个镜像不会再有更新了

官方便在12月2日正式将CentOS 6相关的软件源移出了官方源，随之而来逐级镜像也会陆续将其删除。

不过有一些老设备依然需要维持在当前系统，CentOS官方也给这些还不想把CentOS 6扔进垃圾堆的用户保留了各个版本软件源的镜像，只是这个软件源不会再有更新了。


一键修复
```bash
sed -i "s|enabled=1|enabled=0|g" /etc/yum/pluginconf.d/fastestmirror.conf
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
curl -o /etc/yum.repos.d/CentOS-Base.repo https://www.xmpan.com/Centos-6-Vault-Aliyun.repo 
yum clean all
yum makecache
```

手动修复教程:
```bash
# 首先把fastestmirrors关了
# 编辑
vi /etc/yum/pluginconf.d/fastestmirror.conf
# 修改
enable=0
# 或者执行以下命令
sed -i "s|enabled=1|enabled=0|g" /etc/yum/pluginconf.d/fastestmirror.conf
```

先把之前的repo挪到备份
```bash
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
```

镜像二选一
```bash
# 替换为官方Vault源(海外服务器用)
curl -o /etc/yum.repos.d/CentOS-Base.repo https://www.xmpan.com/Centos-6-Vault-Official.repo
# 或者替换为阿里云Vault镜像(国内服务器用)
curl -o /etc/yum.repos.d/CentOS-Base.repo https://www.xmpan.com/Centos-6-Vault-Aliyun.repo
```


报错详情
```bash
[sgsm@ipason yum.repos.d]$ sudo yum  makecache
Loaded plugins: fastestmirror, security
Determining fastest mirrors
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
http://mirrors.aliyun.com/centos/6/os/x86_64/repodata/repomd.xml: [Errno 14] PYCURL ERROR 22 - "The requested URL returned error: 404 Not Found"
Trying other mirror.
To address this issue please refer to the below knowledge base article 

https://access.redhat.com/articles/1320623

If above article doesn't help to resolve this issue please open a ticket with Red Hat Support.



http://mirrors.aliyuncs.com/centos/6/os/x86_64/repodata/repomd.xml: [Errno 12] Timeout on http://mirrors.aliyuncs.com/centos/6/os/x86_64/repodata/repomd.xml: (28, 'connect() timed out!')
Trying other mirror.
http://mirrors.cloud.aliyuncs.com/centos/6/os/x86_64/repodata/repomd.xml: [Errno 14] PYCURL ERROR 6 - "Couldn't resolve host 'mirrors.cloud.aliyuncs.com'"
Trying other mirror.
Error: Cannot retrieve repository metadata (repomd.xml) for repository: base. Please verify its path and try again
[sgsm@ipason yum.repos.d]$ 
```

```bash
[sgsm@ipason yum.repos.d]$ sudo  yum makecache
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
YumRepo Error: All mirror URLs are not using ftp, http[s] or file.
 Eg. Invalid release/repo/arch combination/
removing mirrorlist with no valid mirrors: /var/cache/yum/x86_64/6/base/mirrorlist.txt
Error: Cannot find a valid baseurl for repo: base
```

