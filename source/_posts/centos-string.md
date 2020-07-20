---
title: centos删除乱码名文件
tags:
  - shell
  - linux
date: 2020-07-16 14:46:14
---
文件上传到centos服务器上后,发现文件名出现乱码,使用下面命令删除乱码文件
```bash
# 查看乱码文件的inode节点   # ls -li
[sgsm@iZ2zef138im1g6dx5cxwrfZ ~]$  ls -li
total 178096
135040 -rw-r--r-- 1 sgsm users         0 Mar 13 20:00 ?
135042 -rw-r--r-- 1 sgsm users         0 Mar 13 20:00 ???A??????^??=
134281 -rw-r--r-- 1 sgsm users 181238643 Apr 15  2019 jdk-8u60-linux-x64.tar.gz
# 使用find 搜索inode节点id  并且删除    # {} 代表前面查找到的内容    \;是固定格式
find ./ -inum 135040 -exec rm -rf {} \;
```