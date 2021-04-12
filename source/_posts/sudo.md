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
因为默认情况下需要终端，可以在配置文件关闭
```bash
sudo vim /etc/sudoers

    # Defaults    requiretty  # 把这行注释
```
