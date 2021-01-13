---
title: centos安装samba文件共享--隐藏目录
tags:
  - samba
  - linux
categories: 运维
date: 2020-05-14 15:24:46
---
## samba软件构成
1、samba软件包的构成
    在光盘的安装包中，可以找到与samba相关的几个软件包，主要包括服务端软件samba，客户端软件samba-client，用于提供服务端和客户端程序的公共组件samba-common

2、samba服务的程序组件
    samba服务器提供smbd、nmbd两个服务程序
    smbd负责为客户机提供服务器中共享资源的访问（目录文件等）
    nmbd负责提供基于NetBIOS协议的名字解析、浏览服务
    NetBIOS协议：由IBM公司开发，使用户软件能使用局域网的资源，自从诞生，NetBIOS成为许多其他网络应用程序的基础，严格意义上，NetBIOS是接入网络服务的接口标准

3、使用netstat查看状态(-atunp)(-a可以查看所有连线中的socket)
    smbd：负责监听TCP协议的139（SMB协议）和445（CIFS协议）端口
    nmbd：负责监听UDP协议的137和138（NetBIOS协议）端口
    
## 配置环境--关闭防火墙和selinux
<br/>centos6<br/>
   
    service  iptables  stop
    service  ip6tables  stop

centos7

    systemctl stop  firewalld

永久关闭

    chkconfig  iptables  off   
    chkconfig  ip6tables  off   

临时关闭selinux

    setenforce 0

永久关闭selinux

    vim  /etc/selinux/config
        SELINUX=disabled

重启生效

## 安装samba服务

    yum  install samba  samba-client  samba-common  samba-doc  -y 

## 配置samba服务

    cp  /etc/samba/smb.conf  /etc/samba/smb.conf_bak
    vim /etc/samba/smb.conf
    [global]     #定义全局策略
        workgroup = MYGROUP   #定义工作组
        server string = Samba Server Version %v #服务器提示字符，默认显示samba版本
        log file = /var/log/samba/log.%m    #定义日志文件
        max log size = 50      #定义日志文件单个文件最大容量为50KB
        security = user        #security选项将会影响客户端访问方式       #可以设置user、share、server、domain。User代表用户名和密码验证；share代表匿名访问；server代表基于验证身份的访问，账户信息在另一台SMB服务器上；domain:同样基于验证身份验证，账户信息在活动目录中    
        passdb backend = tdbsam    #账户与密码存储方式，smbpasswd使用老的明文格式存储账户及密码；tdbsam代表基于TDB的密文格式存储；ldapsam代表使用LDAP存储账户资料。
        load printers = yes        #客户端在10分钟内没有打开任何Samba资源，服务器将自动关闭回话。
        cups options = raw       #打印属性

        config file = /etc/samba/%U.smb.conf   #指定扩展文件


    [dome]       #共享名称为dome
        comment = Common share
        path = /common        #指定共享目录
        valid users = tom jerry    #有效账户列表
        create mask = 0750        #客户端上传文件的默认权限
        directorymask = 0775       #客户端创建目录的默认权限 
        browseable = yes       #客户端是否对所有人可见    
        writable= no          #是否允许写入
        write list = tom       #写权限账户列表
        admin users = tom       #该共享的管理员，具有完全权限
        invalid users = root bin    #禁止root与bin访问common共享
          guest ok = no       #是否允许匿名访问
    
    
    [server]
        path = /share/samba/server
        directory  mask = 0755
        create mask = 0644
        valid users = yanfa
        browseable = no

    [meishu]
        path = /share/samba/meishu
        directory  mask =0755
        create mask =0644
        valid users = meishu
        browseable = no

    [yunyingmeishu]
        path = /share/samba/yunyingmeishu
        directory  mask =0755
        create mask =0644
        valid users = yunying
        browseable = no


## 创建扩展文件

    cd /etc/samba/

    vim   yanfa.smb.conf  
        [share]
        security = user
        path = /share/samba/yanfa
        valid users = @yanfa
        read list = @yanfa
        write list = @yanfa
        writable = yes
        create mask = 0644
        directory mask = 0755 


    vim   meishu.smb.conf   
        [meishu]
        security = user
        path = /share/samba/meishu
        valid users = @meishu
        read list = @meishu
        write list = @meishu
        writable = yes
        create mask = 0644
        directory mask = 0755


    vim   yunying.smb.conf 
        [yunying]
        security = user
        path = /share/samba/yunyingmeishu
        valid users = @yunying
        read list = @yunying
        write list = @yunying
        writable = yes
        create mask = 0644
        directory mask = 0755


## 创建共享文件夹

  mkdir /share/samba/{yanfa,meishu,yunyingmeishu}    -p


## 创建登录用户

    useradd  yanfa
    useradd  meishu
    useradd  yunying

## 创建samba用户--需要交互式输入密码，此密码和系统用户密码无关

    pdbedit -a  yanfa
    pdbedit -a  meishu
    pdbedit -a  yunying

pdbedit常用参数

    pdbedit -L  ：查看samba用户
    pdbedit -Lv：列出Samba用户列表详细信息
    pdbedit -a  -u  user：添加samba用户
    pdbedit -r  -u  user：修改samba用户信息
    pdbedit -x  -u  user： 删除samba用户
    
## 共享文件夹更改权限

    cd  /share/samba/
    chown  meishu.meishu  meishu/ -R
    chown   yunying.yunying  yunyingmeishu/ -R
    chown  yanfa.yanfa  yanfa/ -R

## 启动服务就可以访问了

    /etc/init.d/smb  start
    /etc/init.d/nmb  start


## 好礼大放送--wind客户端清理已保存的samba用户和密码

    net  use      # 查看已保存的用户和密码

    net use  *  /del   /y     # 清除所有账号密码

## Win10无法访问Samba共享文件夹【解决方案】
1. 进入“控制面板”，进入“程序和功能“

2、选择“启用或关闭Windows功能”

3、在功能列表中确保选中“SMB1.0/CIFS文件共享支持”，然后确定安装，重新启动电脑即可生效。
