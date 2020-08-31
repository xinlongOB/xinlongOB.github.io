---
title: Nginx 和 Apache 各有什么优缺点
tags:
  - nginx
  - apache
  - linux
categories: 运维
date: 2020-08-10 14:44:55
---
nginx 相对 apache 的优点：
```bash
轻量级,同样起web 服务,比apache 占用更少的内存及资源抗并发,
nginx 处理请求是异步非阻塞的,而apache 则是阻塞型的,在高并发下nginx 能保持低资源低消耗高性能
高度模块化的设计,编写模块相对简单
社区活跃,各种高性能模块出品迅速啊
nginx适合静态和反向代理
```
apache 相对nginx 的优点：
```bash
rewrite ,比nginx 的rewrite 强大
模块超多,基本想到的都可以找到
少bug ,nginx 的bug 相对较多
超稳定
apache处理动态请求
```
存在就是理由,一般来说,需要性能的web 服务,用nginx 。如果不需要性能只求稳定,那就apache 吧