---
title: 查看已删除空间却没有释放的进程
tags:
  - lsof
  - linux
categories: 运维
date: 2021-05-27 15:01:22
---
lsof -n / |grep deleted查看已删除空间却没有释放的进程

查看已经删除的文件，空间有没有释放，没有的话kill掉pid

lsof -n |grep deleted

lsof简介lsof(list open files)是一个列出当前系统打开文件的工具。



使用du -h -x --max-depth=1 查看哪个目录占用过高，对于过高目录中的内容适当删减腾出一些空间
du -lh --max-depth=1