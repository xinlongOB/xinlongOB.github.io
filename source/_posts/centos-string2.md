---
title: centos去掉^M的方法
tags:
  - shell
  - linux
date: 2020-07-20 11:37:54
---
cat -A filename 就可以看到windows下的断元字符 ^M,要去除他,最简单用下面的命令
第一种方法：
```bash
sudo yum install dos2unix -y   # 安装软件
dos2unix filename.txt   # 删除^M
```
第二种方法：
```bash
sed -i 's/^M//g' filename
```
第三种方法
```
vi filename
    :1,$ s/^M//g
```
注意：^M的输入方式是 Ctrl + v ，然后Ctrl + M