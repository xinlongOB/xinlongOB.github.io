---
title: Centos中shell脚本里面使用sudo报错
tags:
  - sudo
  - linux
categories: 运维
date: 2021-04-06 11:23:29
---
报错
```bash
sudo: sorry, you must have a tty to run sudo
```
在一个终端中调用另一个shell，始终是无法执行的，后来捕捉到报错信息为sudo: sorry, you must have a tty to run sudo
因为默认情况下需要终端，可以在配置文件关闭
```bash
sudo vim /etc/sudoers

    # Defaults    requiretty  # 把这行注释
```
