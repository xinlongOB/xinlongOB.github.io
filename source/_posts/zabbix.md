---
title: zabbix监控搭建及配置邮件报警
tags:
  - liunx
categories: 运维
date: 2020-06-29 08:55:27
---
## 环境配置(server端和agent端)
升级系统组件到最新的版本
```bash
sudo  yum -y update
```
关闭selinux
```bash
setenforce 0       #临时关闭命令
vi /etc/selinux/config    #将SELINUX=enforcing改为SELINUX=disabled 设置后需要重启才能生效
getenforce         #检测selinux是否关闭，Disabled 为关闭
```
关闭防火墙
```bash
firewall-cmd --state    #查看默认防火墙状态，关闭后显示not running，开启后显示running
systemctl stop firewalld.service    #临时关闭firewal
systemctl disable firewalld.service #禁止firewall开机启动
```

## zabbix服务端配置(server端)
zabbix需要借助LAMP或者LNMP环境,LAMP比较方便配置所以先搭建LAMP环境
```bash
# 安装软件包和其他工具包
 yum install -y httpd mariadb-server mariadb php php-mysql php-gd libjpeg* php-ldap php-odbc php-pear php-xml php-xmlrpc php-mhash
 rpm -qa httpd php   mariadb   
 # 或者  
 rpm -qa httpd php mysql-community-server   
```
添加首页支持格式　
```bash
vim  /etc/httpd/conf/httpd.conf
     DirectoryIndex index.html index.php  
```
配置时区  
```bash
vim /etc/php.ini
      date.timezone = PRC
```
启动并加入开启自启动
```bash
systemctl start httpd   #启动并加入开机自启动httpd
systemctl enable httpd
systemctl start mysqld  #启动并加入开机自启动mysqld
systemctl enable mysqld

ss -anplt | grep httpd   #查看httpd启动情况，80端口监控表示httpd已启动
ss -naplt | grep mysqld  #查看mysqld启动情况，3306端口监控表示mysqld已启动　
```
创建一个测试页测试
```bash
sudo  sh -c 'echo "<?php echo phpinfo();?>"  > index.php '  
# 直接使用sudo echo 会提示权限不足   例如：sudo echo "<?php echo phpinfo();?>"  > index.php 

```
![](../1.png)
## 数据库配置(server端)
初始化数据库设置数据库root密码
```bash
sudo mysqladmin -u root password 123456  

#root用户登陆数据库
mysql -u root -p123456       
#创建zabbix数据库（中文编码格式）
CREATE DATABASE zabbix character set utf8 collate utf8_bin;   
#授予zabbix用户zabbix数据库的所有权限，密码admin123
GRANT all ON zabbix.* TO 'zabbix'@'%' IDENTIFIED BY 'admin123';  
#刷新权限
flush privileges;   
#退出数据库 
quit                
```
![](../2.png)

数据库连接测试页
```bash
sudo vim /var/www/html/index.php 
    <?php
    $link=mysql_connect('172.18.20.224','zabbix','admin123'); 
    if($link) echo "<h1>Success!!</h1>";   #显示Success表示连接数据库成功
    else echo "Fail!!";
    mysql_close();
    ?>
```
![](../3.png)

## 安装zabbix(server端)
安装依赖包和组件
```bash
sudo  yum -y install net-snmp net-snmp-devel curl curl-devel libxml2 libxml2-devel libevent-devel.x86_64 javacc.noarch  javacc-javadoc.noarch javacc-maven-plugin.noarch javacc*
# 安装php支持zabbix组件
sudo  yum install php-bcmath php-mbstring -y 
# 会自动生成yum源文件，保证系统可以上网
sudo  rpm -ivh http://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm  
# 清理yum缓存
sudo yum clean all 
# 安装zabbix组件
sudo  yum install zabbix-server-mysql zabbix-web-mysql -y    
```
安装zabbix后会有一个数据库文件,需要把这个文件恢复到数据库中
```bash
cd   /usr/share/doc/zabbix-server-mysql-4.0.21/
#导入数据到数据库zabbix中(最后一个zabbix是数据库zabbix)，且因为用户zabbix是%(任意主机)，所以登录时需要加上当前主机ip(-h 192.168.1.122),密码是用户zabbix登陆密码admin123
sudo  zcat  create.sql.gz | mysql -uzabbix -p -h 192.168.1.122 zabbix   
```

在配置文件中配置数据库用户及密码
```bash
sudo vim  /etc/zabbix/zabbix_server.conf 

    DBHost=192.168.1.122
    DBName=zabbix
    DBUser=zabbix
    DBPassword=admin123
```
确定数据库用户及密码
```bash
grep -n '^'[a-Z] /etc/zabbix/zabbix_server.conf
```

修改时区
```bash
sudo  vim /etc/httpd/conf.d/zabbix.conf  
# 将# php_value date.timezone Europe/Riga 变更成php_value date.timezone Asia/Shanghai

    php_value date.timezone Asia/Shanghai

```
启动并加入开机自启动zabbix-server
```bash
systemctl enable zabbix-server 
systemctl start zabbix-server
#   监听在10051端口上,如果没监听成功，可重启zabbix-server服务试试
netstat -anpt | grep zabbix        
```
默认用户和密码
```bash
	默认账号Admin
	默认密码为zabbix  密码经过MD5加密后为5fce1b3e34b520afeffb37ce08c7cd66
```
## welcom zabbix(后台)
如果以上步骤无误，现在可以使用web打开   
```bash
http://192.168.1.122/zabbix　  # 注意这里IE浏览器打不开,使用其他浏览器
```

![](../5.png)
<br/>这里必须全部都是OK<br/>
![](../6.png)
<br/>![](../7.png)<br/>
![](../8.png)
<br/>![](../9.png)<br/>
安装成功
<br/>![](../10.png)<br/>
进入界面后设置语言
<br/>![](../11.png)<br/>
选择Chinese
<br/>![](../12.png)<br/>

## Agent端配置(agent端)
安装依赖包和组件
```bash
sudo  yum -y install net-snmp net-snmp-devel curl curl-devel libxml2 libxml2-devel libevent-devel.x86_64 javacc.noarch  javacc-javadoc.noarch javacc-maven-plugin.noarch javacc*
# 安装php支持zabbix组件
sudo  yum install php-bcmath php-mbstring -y 
# 会自动生成yum源文件，保证系统可以上网
sudo  rpm -ivh http://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm  
# 清理yum缓存
sudo yum clean all 
# 安装zabbix-agent 
sudo   yum install zabbix-agent  -y   
```
修改zabbix-agent的配置文件
```bash
vim   /etc/zabbix/zabbix_agentd.conf

    # 指定zabbix服务器的IP
    Server=192.168.1.122    
    # 指定zabbix服务器的IP
    ServerActive=192.168.1.122  
    # 指定后台显示名称
    Hostname=test     
    # 是否支持自定义key  默认为 0  不支持
    UnsafeUserParameters=1   
			
    # 自定义key  监控项
    UserParameter=prod.redis,ps -ef|grep 'redis' |grep -v 'grep'|wc -l     
    # 自定义key  监控项
    UserParameter=prod.mongo,ps -ef|grep 'mongo' |grep -v 'grep'|wc -l   
    # 自定义key  监控项  
    UserParameter=prod.node,ps -ef|grep 'node' |grep -v 'grep'|wc -l       
```
启动agent端
```bash
/usr/sbin/zabbix_agentd  -c /etc/zabbix/zabbix_agentd.conf     # 启动agent端
systemctl  restart   zabbix-agent   # 重启
```

## zabbix服务器上测试(server端)
需要下载 zabbix-get
```bash
sudo yum install  zabbix-get   -y
```
```bash  
[sgsm@localhost zabbix-server-mysql-4.0.21]$ zabbix_get -s 192.168.1.220 -p 10050 -k prod.redis     # 显示数值 代表成功
1  
[sgsm@localhost zabbix-server-mysql-4.0.21]$ 
```

## zabbix后台配置监控项(后台)
创建群组
<br/>![](../13.png)<br/>
设置组名
<br/>![](../14.png)<br/>
![](../15.png)
<br/>创建主机<br/>
![](../16.png)
<br/>![](../18.png)<br/>
![](../19.png)
<br/>![](../20.png)<br/>
![](../21.png)
<br/>创建监控项<br/>
![](../22.png)
<br/>![](../23.png)<br/>
![](../24.png)
<br/>创建触发器<br/>
![](../25.png)
<br/>![](../26.png)<br/>
![](../27.png)
<br/>![](../28.png)<br/>
可以在最新数据查看当前值
<br/>![](../29.png)<br/>
![](../30.png)

修改状态测试
<br/>![](../31.png)<br/>
![](../32.png)
<br/>![](../33.png)<br/>
![](../34.png)
至此监控配置完成,下面需要配置邮件服务,当有服务宕机发邮件告警

## 配置媒介邮件(server端)
首先需要在邮件获取授权码
<br/>![](../40.png)<br/>
![](../41.png)
<br/>![](../42.png)<br/>

本次测试使用mailx服务
```bash
# 关闭当前postfix邮件
sudo  systemctl stop postfix
chkconfig  postfix  off
# 安装mailx
sudo yum install mailx  -y
```
配置邮件服务
```bash
sudo  vim /etc/mail.rc

    # 发件人地址
    set from=xxxxxx@qq.com smtp=smtp.qq.com    
    # 收件人地址                       授权码(邮箱IMAP/SMTP服务的授权码)
    set smtp-auth-user=xxxxxx@qq.com smtp-auth-password=xxxxxx      
    set smtp-auth=login
```
测试发送邮件
```bash
echo "zabbix test mail" |mail -s "zabbix" xxxxxx@qq.com
```

## 配置发送邮件(后台)
管理—示警介类型—创建媒体类型
<br/>创建报警媒介类型 (脚本参数分别对应：收件人地址、主题、详细内容)<br/>
![](../50.png)
<br/>配置用户 选择admin用户<br/>
![](../51.png)
<br/>添加报警媒介<br/>
![](../52.png)
<br/>创建报警动作 配置-动作-创建动作,新建动作<br/>
![](../53.png)
<br/>新建操作<br/>
![](../54.png)
<br/>![](../55.png)<br/>
添加恢复操作
<br/>![](../56.png)<br/>

配置完成后测试(修改触发器或者关闭进程)
<br/>![](../57.png)<br/>
![](../58.png)
<br/>![](../59.png)<br/>
![](../60.png)
<br/>![](../61.png)<br/>
![](../62.png)
<br/>邮件内容以及在动作日志中查看发送记录<br/>
![](../63.png)
