---
title: TortoiseSVN使用
tags:
  - subversion
  - liunx
categories: 运维
date: 2020-06-08 14:07:17
---
## 部署subversion服务器
[subversion安装部署](https://xinlong.youare.ink/2019/12/03/test/#4)

## TortoiseSVN 安装
下载地址：https://tortoisesvn.net/downloads.html, 页面里有语言包补丁的下载链接。

## TortoiseSVN基础操作
检出代码
<br/>![](../5.png)<br/>
![](../6.png)

更新代码
<br/>![](../7.png)<br/>

提交代码
<br/>![](../8.png)<br/>
![](../9.png)

切新分支
<br/>![](../10.png)<br/>
![](../14.png)
<br/>切出后查看内容<br/>
![](../15.png)
<br/>主线内容<br/>
![](../16.png)

查看提交记录
<br/>![](../18.png)<br/>
![](../19.png)
<br/>![](../20.png)<br/>
![](../21.png)
<br/>![](../22.png)<br/>



## 报错解决：
提交报错：could not begin a transaction  
<br/>![](../1.png)<br/>
解决办法：
```bash
cd  /data/svn/program
sudo chown -R apache:apache  ./
```
修改之前：
<br/>![](../2.png)<br/>
修改之后：
<br/>![](../3.png)<br/>
再次提交：
<br/>![](../4.png)<br/>

