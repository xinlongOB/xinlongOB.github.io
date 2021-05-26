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

1、进入“控制面板”，进入“程序和功能“

2、选择“启用或关闭Windows功能”

3、在功能列表中确保选中“SMB1.0/CIFS文件共享支持”，然后确定安装，重新启动电脑即可生效。


## 多个用户有不同的权限

需求：研发组和其他人组能看到只有研发组内的用户可写
```bash
[yanfa]
        path = /home/samba/yanfa
        directory  mask = 0755
        create mask = 0644
        valid users = @yanfa @other
        guest ok = no
        write list = @yanfa
        browsable = yes
        security = user
```


参考大佬配置的各种情况
```bash
1。首先服务器采用用户验证的方式，每个用户可以访问自己的宿主目录，并且只有该用户能访问宿主目录，并具有完全的权限，而其他人不能看到你的宿主目录。

2。建立一个caiwu的文件夹，希望caiwu组和lingdao组的人能看到，network02也可以访问，但只有caiwu01有写的权限。

3。建立一个lindao的目录，只有领导组的人可以访问并读写，还有network02也可以访问，但外人看不到那个目录

4。建立一个文件交换目录exchange，所有人都能读写，包括guest用户，但每个人不能删除别人的文件。

5。建立一个公共的只读文件夹public，所有人只读这个文件夹的内容。

我们先来前期的工作

建立3个组：
#groupadd caiwu

#groupadd network

#groupadd lingdao


添加用户并加入相关的组当中：
#useradd caiwu01 -g caiwu

#useradd caiwu02 -g caiwu

#useradd network01 -g network

#useradd network02 -g network

#useradd lingdao01 -g lingdao

#useradd lingdao02 -g lingdao

然后我们使用smbpasswd -a caiwu01的命令为6个帐户分别添加到samba用户中

#mkdir /home/samba

#mkdir /home/samba/caiwu

#mkdir /home/samba/lingdao

#mkdir /home/samba/exchange

#mkdir /home/samba/public

我们为了避免麻烦可以在这里把上面所有的文件夹的权限都设置成777，我们通过samba灵活的权限管理来设置上面的5点要求。

以下是我的smb.conf的配置文件

[global]

workgroup = bmit 

#我的网络工作组

server string = Frank's Samba File Server

#我的服务器名描述

security = user

#使用用户验证机制

encrypt passwords = yes
smb passwd file = /etc/samba/smbpasswd
#使用加密密码机制，在win95和winnt使用的是明文

其他的基本上可以按照默认的来。

[homes]
comment = Home Directories
browseable = no
writable = yes
valid users = %S
create mode = 0664
directory mode = 0775

#homes段满足第1条件

[caiwu]
comment = caiwu
path = /home/samba/caiwu
public = no
valid users = @caiwu,@lingdao,network02
write list = caiwu01
printable = no

#caiwu段满足我们的第2要求

[lingdao]
comment = lingdao
path = /home/samba/lingdao
public = no
browseable = no
valid users = @lingdao,network02
printable = no

#lingdao段能满足我们的第3要求

[exchage]
comment = Exchange File Directory
path = /home/samba/exchange
public = yes
writable = yes

#exchange段基本能满足我们的第4要求，但不能满足每个人不能删除别人的文件这个条件，即使里设置了mask也是没用，其实这个条件只要unix设置一个粘着位就行

chmod -R 1777 /home/samba/exchange 

注意这里权限是1777，类似的系统目录/tmp也具有相同的权限，这个权限能实现每个人能自由写文件，但不能删除别人的文件这个要求

[public]
comment = Read Only Public
path = /home/samba/public
public = yes
read only = yes

#这个public段能满足我们的第5要求。

到此为止我们的设置已经能实现我们的共享文件要求，记得重启服务哦

#/etc/rc.d/init.d/smb restart
```