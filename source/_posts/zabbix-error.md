---
title: zabbix报错解决
tags:
  - liunx
categories: 运维
date: 2020-07-02 15:55:35
---
## 下载yum源报错
```bash
[sgsm@localhost yum.repos.d]$ sudo  rpm -ivh http://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm 
	获取http://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm
	准备中...                          ################################# [100%]
        file /etc/yum.repos.d/zabbix.repo from install of zabbix-release-4.0-1.el7.noarch conflicts with file from package zabbix-release-3.2-1.el7.noarch
```
这是因为服务器上已经部署了zabbix,卸载原来的zabbix就可以了 
```bash
sudo yum  remove   zabbix-release-3.2-1.el7.noarch   -y      
```

## 后台登录密码忘记
```bash
mysql> use zabbix
	Reading table information for completion of table and column names
	You can turn off this feature to get a quicker startup with -A

	Database changed
mysql> update users set passwd='5fce1b3e34b520afeffb37ce08c7cd66' where userid='1';
	Query OK, 1 row affected (0.01 sec)
	Rows matched: 1  Changed: 1  Warnings: 0
		
# 由于密码是md5加密的，我们可以查看默认的zabbix密码的md5
mysql> use zabbix;

mysql> update users set passwd='5fce1b3e34b520afeffb37ce08c7cd66' where userid='1';
```
重新设置密码为zabbix,然后重新登陆 用户：Admin   密码：zabbix

## zabbix设置中文出现乱码
zabbix语言设置为中文后,有乱码如下：
<br/>![](../1.png)<br/>

1.从 windows 下控制面板->字体->选择一种中文字库例如“楷体”
<br/>![](../2.png)<br/>
![](../3.png)
<br/><br/>
2.将字体上传至/usr/share/zabbix/assets/fonts (根据zabbix的安装位置 可以使用find查找一下路径) 目录下
<br/>![](../99.png)<br/>
注意：查找到zabbix有两个fonts目录 就去配置文件看下使用的那个目录(版本不同 路径就不同)

```bash
[sgsm@localhost fonts]$ cat ../include/defines.inc.php   |grep  path
define('ZBX_FONTPATH',                          realpath('assets/fonts')); // where to search for font (GD > 2.0.18)
[sgsm@localhost fonts]$ 
```

<br/>使用rz 拉取到服务器<br/>
![](../7.png)

3.修改 zabbix 页面管理的中文字体设置
```bash
	[root@zabbix-server zabbix-2.4.5]# vim /usr/share/zabbix/include/defines.inc.php
	#修改如下 2 行
	define('ZBX_FONT_NAME', 'simkai');
	define('ZBX_GRAPH_FONT_NAME', 'simkai');
```

修改后的 zabbix 界面
<br/>![](../5.png)<br/>
如果还不行就给字体权限

```bash
sudo  chmod   777    simkai.ttf
```