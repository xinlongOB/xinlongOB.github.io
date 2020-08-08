---
title: fail2ban防暴力破解
tags:
  - fail2ban
  - linux
categories: 运维
date: 2020-08-06 15:16:27
---
## 关于Fail2ban
Fail2ban可以监视你的系统日志,然后匹配日志的错误信息（正则式匹配）执行相应的屏蔽动作（一般情况下是调用防火墙屏蔽）,如:当有人在试探你的HTTP、SSH、SMTP、FTP密码,只要达到你预设的次数,fail2ban就会调用防火墙屏蔽这个IP,而且可以发送e-mail通知系统管理员,是一款很实用、很强大的软件
功能和特性
1、支持大量服务,如ssh、apache、qmail、proftpd、sasl等
2、支持多种动作,如iptables、tcp-weapper,shorewall（iptables第三方工具）,mail notifications（邮件通知）等
3、在logpath（日志路径）选择中支持通配符
4、需要Gamin支持（注：Gamin是用于监视文件和目录是否更改的服务工具）
5、需要安装python、iptables、tcp-wrapper、shorewall、Gamin。如果想要发邮件,那必须安装postfix或sendmail
核心原理：其实fail2ban就是用来监控,具体是调用iptables来实现动作

## 安装Fail2ban
从CentOS7开始,官方的标准防火墙设置软件从iptables变更为firewalld。 为了使Fail2ban与iptables联动,需禁用自带的firewalld服务,同时安装iptables服务。因此,在进行Fail2ban的安装与使用前需根据博客CentOS7安装和配置iptables防火墙进行环境配置

首先需要到Fail2ban官网下载程序源码包
```bash
wget https://codeload.github.com/fail2ban/fail2ban/tar.gz/0.8.14
```
成功下载之后,解压源码包并进行安装
```bash
tar -xf 0.8.14
cd fail2ban-0.8.14
python setup.py install
```
安装完成后要手动生成一下程序的启动脚本
```bash
cp files/redhat-initd /etc/init.d/fail2ban
chkconfig --add fail2ban
```

## 配置Fail2ban
安装完成后,服务配置目录为:
action.d       #动作文件夹,内含默认文件,iptables和mail等动作配置
fail2ban.conf  #定义了fail2ban日志级别、日志位置及sock文件位置
filter.d       # 条件文件夹,内含默认文件。过滤日志关键内容设置
jail.conf      #主配置文件。模块化,主要设置启用ban动作的服务及动作阀值

![](../1.png)
新建jail.local来覆盖Fail2ban在jail.conf的默认配置
```bash
cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
vim /etc/fail2ban/jail.local

    [ssh-iptables]
    enabled  = true
    filter   = sshd   #监控服务名称
    action   = iptables[name=SSH, port=22, protocol=tcp]
    logpath  = /var/log/secure  # 日志文件
    maxretry = 3     # 错误最高次数
    findtime  = 300
```
>注意：logpath要写成/var/log/secure,这个系统登录日志,不能随意设置

启动fail2ban
```bash
systemctl   start  fail2ban 
```

## 测试功能
开启后在防火墙会出现一个自定义链
```bash
[root@localhost fail2ban-0.8.14]# iptables -nL
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
fail2ban-SSH  tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:22

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination         

Chain fail2ban-SSH (1 references)
target     prot opt source               destination         
RETURN     all  --  0.0.0.0/0            0.0.0.0/0  
```
ssh登录测试
```bash
[sgsm@ipason monitorlog]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 00:e0:4c:13:7c:87 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.100/24 brd 192.168.1.255 scope global eth0
    inet6 fe80::2e0:4cff:fe13:7c87/64 scope link 
       valid_lft forever preferred_lft forever
3: wlan0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether ac:67:5d:95:40:75 brd ff:ff:ff:ff:ff:ff
[sgsm@ipason monitorlog]$ 
[sgsm@ipason monitorlog]$ ssh  192.168.1.89
The authenticity of host '192.168.1.89 (192.168.1.89)' can't be established.
RSA key fingerprint is 8f:a5:4d:da:e7:be:b4:ca:4f:6a:f9:75:85:b4:f7:e1.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.1.89' (RSA) to the list of known hosts.
sgsm@192.168.1.89's password: 
Permission denied, please try again.
sgsm@192.168.1.89's password: 
Permission denied, please try again.
sgsm@192.168.1.89's password: 
Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).
[sgsm@ipason monitorlog]$ ssh  192.168.1.89
ssh: connect to host 192.168.1.89 port 22: Connection refused
```
fail2ban状态  阻挡了此ip的连接
```bash
[root@localhost fail2ban-0.8.14]# fail2ban-client status ssh-iptables
Status for the jail: ssh-iptables
|- filter
|  |- File list:        /var/log/secure 
|  |- Currently failed: 0
|  `- Total failed:     3
`- action
   |- Currently banned: 1
   |  `- IP list:       192.168.1.100 
   `- Total banned:     1
[root@localhost fail2ban-0.8.14]# 
```

##  Fail2ban命令
查看被ban的IP
```bash
fail2ban-client status ssh-iptables
```
从黑名单删除
```bash
/usr/bin/fail2ban-client  set  ssh-iptables   unbanip  IP地址
```
添加白名单
```bash
fail2ban-client set ssh-iptables addignoreip IP地址 
```
删除白名单
```bash
fail2ban-client set ssh-iptables delignoreip IP地址
```

## 阻止恶意扫描
新增[nginx-dir-scan]模块,配置信息如下。此处,port和logpath应按照实际情况填写
```bash
 [nginx-dir-scan]

 enabled = true
 filter = nginx-dir-scan
 action   = iptables[name=nginx-dir-scan, port=443, protocol=tcp]
 logpath = /path/to/nginx/access.log
 maxretry = 1
 bantime = 172800
 findtime  = 300
```
然后在filter.d目录下新建nginx-dir-scan.conf
```bash
cp /etc/fail2ban/filter.d/nginx-http-auth.conf /etc/fail2ban/filter.d/nginx-dir-scan.conf
vim /etc/fail2ban/filter.d/nginx-dir-scan.conf
    [Definition]

    failregex = <HOST> -.*- .*Mozilla/4.0* .* .*$
    ignoreregex =
```
此处的正则匹配规则是根据nginx的访问日志进行撰写,不同的恶意扫描有不同的日志特征。

本文采用此规则是因为在特殊的应用场景下有绝大的把握可以肯定Mozilla/4.0是一些老旧的数据采集软件使用的UA,所以就针对其做了屏蔽。不可否认Mozilla/4.0 这样的客户端虽然是少数,但仍旧存在。因此,此规则并不适用于任何情况。

使用如下命令,可以测试正则规则的有效性
```bash
fail2ban-regex /path/to/nginx/access.log /etc/fail2ban/filter.d/nginx-dir-scan.conf
```
Fail2ban已经内置很多匹配规则,位于filter.d目录下,包含了常见的SSH/FTP/Nginx/Apache等日志匹配,如果都还无法满足需求,也可以自行新建规则来匹配异常IP。总之,使用Fail2ban+iptables来阻止恶意IP是行之有效的办法,可极大提高服务器安全

变更iptables封禁策略

Fail2ban的默认iptables封禁策略为 REJECT --reject-with icmp-port-unreachable,需要变更iptables封禁策略为DROP。

在/etc/fail2ban/action.d/目录下新建文件iptables-blocktype.local。
```bash
cd /etc/fail2ban/action.d/
cp iptables-blocktype.conf iptables-blocktype.local
vim iptables-blocktype.local

    [INCLUDES]

    after = iptables-blocktype.local

    [Init]

    blocktype = DROP
```
最后,别忘记重启fail2ban使其生效。
```bash
systemctl restart fail2ban
```