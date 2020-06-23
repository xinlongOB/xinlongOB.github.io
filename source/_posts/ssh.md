---
title: centos下生成多份密钥对
tags:
  - ssh
  - Linux
categories: 运维
date: 2020-06-11 14:25:31
---
常用选项
```bash
-t  指定秘钥类型(默认rsa)
-f  指定秘钥文件路径(默认用户家目录.ssh下)
-P  指定密码(可不设置)
-c  注释内容一般填写邮件(可不指定)
```

常用语法
```bash
ssh-keygen  -t rsa  -f ~/.ssh/xxx   -P xxx
```

-f 选项的好处就是一台机器可以生成多份秘钥,因为默认的秘钥名为id_rsa和id_rsa.pub,在使用ssh-keygen生成就会覆盖

使用 -f 选项
```bash
[sgsm@iZ2ze53g8gh7cdxahhcv95Z .ssh]$ ssh-keygen  -t rsa  -f ~/.ssh/mihua   -P xxx
[sgsm@iZ2ze53g8gh7cdxahhcv95Z .ssh]$ ll
total 32
-rw------- 1 sgsm sgsm  2047 May 16 11:06 authorized_keys
-rw------- 1 sgsm users  816 May 16 11:03 authorized_keys.bak
-rw------- 1 sgsm users 1675 Apr  8 17:45 id_rsa
-rw-r--r-- 1 sgsm users  410 Apr  8 17:45 id_rsa.pub
-rw-r--r-- 1 sgsm users 4728 Apr 17 14:44 known_hosts
-rw------- 1 sgsm users 1766 Jun 11 09:42 mihua
-rw-r--r-- 1 sgsm users  410 Jun 11 09:42 mihua.pub
```
现在把mihua.pub 导入需要免密登录的机器中就可以实现免密登录