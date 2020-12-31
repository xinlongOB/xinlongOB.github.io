---
title: centos下svn创建多个目录
tags:
  - subversion
  - linux
categories: 运维
date: 2020-12-31 18:26:20
---
部署svn之后默认只有一个跟分支(/)
例如：
```bash
[groups]
group = long,xin

[program:/]
@group = rw
* = 
```
这样可以把根分支检出，
![](../34.png)
如果想多要几个分支，在根目录创建目录提交就好，然后在authz文件中配置权限


```bash
[groups]
group = long,xin
test = join

[program:/]
@group = rw
* = 

[program:/program]  # 新创建并提交的目录
@group = rw
@test = rw
* = 

[program:/server]  # 新创建并提交的目录
@group = rw
@test = rw
* = 
```
![](../35.png)
<br/>![](../36.png)<br/>
之后test分组里面的成员就可以使用  http://192.168.1.230/program/server  检出分支了
对于根目录没有权限
