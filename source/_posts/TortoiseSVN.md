---
title: TortoiseSVN使用
tags:
  - subversion
  - linux
categories: 运维
date: 2020-06-08 14:07:17
---
## 部署subversion服务器
[subversion安装部署](https://xinlong.youare.ink/2019/12/03/test/#4)

## TortoiseSVN 安装
下载地址：https://tortoisesvn.net/downloads.html, 页面里有语言包补丁的下载链接。

## TortoiseSVN检出代码
<br/>![](../5.png)<br/>
![](../6.png)

## 更新代码
<br/>![](../7.png)<br/>

## 提交代码
<br/>![](../8.png)<br/>
![](../9.png)

## 切新分支
<br/>![](../10.png)<br/>
![](../14.png)
<br/>切出后查看内容<br/>
![](../15.png)
<br/>主线内容<br/>
![](../16.png)

## 查看提交记录
<br/>![](../18.png)<br/>
![](../19.png)
<br/>![](../20.png)<br/>
![](../21.png)
<br/>![](../22.png)<br/>

## 合并分支
一般公司都是在分支开发合并到主线，因为版本比较多所以我们公司是在主线开发合并到各个版本的分支
<br/>合并分支案例<br/>
![](../33.png)
<br/>![](../34.png)<br/>
![](../35.png)
<br/>![](../36.png)<br/>
![](../37.png)
<br/>![](../38.png)<br/>
![](../39.png)
<br/>![](../40.png)<br/>
![](../41.png)
<br/>![](../42.png)<br/>
![](../43.png)
<br/>![](../44.png)<br/>
![](../45.png)


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

