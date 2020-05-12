---
title: jenkins添加普通用户设置权限
tags:
  - jenkins
categories: 运维
date: 2019-12-24 16:20:58
---
### jenkins创建普通用户并配置权限
<br/>1、首先在Manage Jenkins --> 用户管理  创建用户<br/>
2、然后在Manage Jenkins --> 全局设置 授权策略选择： 
<br/>项目矩阵授权策略  添加用户或者用户组 选择权限<br/>
![](../1.png)
<br/>3、找到需要授权的项目点击配置<br/>
![](../2.png)
<br/>启用项目安全<br/>
![](../3.png)
<br/>添加admin用户以及其他用户<br/>
![](../4.png)
<br/>最后登录测试<br/>
<br/><br/>
