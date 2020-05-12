---
title: subversion+jenkinks部署
date: 2019-12-03 17:20:03
tags: [jenkins,subversion]
categories: 运维
---
* [1.subversion+jenkins安装部署](#1)
	* [1.1配置环境](#2)
	* [1.2安装jenkins](#3)
	* [1.3安装subversion](#4)

#<h4 id="2">1.1配置环境</h2>
	环境：centos6.9
	软件包：jdk-8u60-linux-x64.tar.gz
首先关闭selinux和防火墙
<br/>![](../1.png)<br/>
更改时间 	 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;--可以写入计划任务中
<br/>![](../3.png)<br/>
创建目录   

	mkdir /application/

<br/>解压jdk包到创建的目录中<br/>

	tar xf jdk-8u60-linux-x64.tar.gz   -C /application/

<br/>做软连接<br/>
	
	ln -s  /application/jdk1.8.0_60/ /application/jdk

<br/>设置环境变量<br/>

	sed -i.ori '$a export  JAVA_HOME=/application/jdk\nexport PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH\nexport  CLASSPATH=.$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$JAVA_HOME/lib/tools.jar'  /etc/profile

<br/>source一下生效环境变量<br/>
![](../2.png)
<br/>![](../4.png)<br/>

<h4 id="3">1.2安装jenkins</h2>

下载yum源并且导入秘钥

<br/>

	wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo<br/>

	rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key

<br/>![](../5.png)<br/>

	yum install jenkins -y 

<br/>![](../6.png)<br/>

	如果安装失败就到官网下载jenkins的rpm包
	http://pkg.jenkins-ci.org/redhat-stable/

编辑配置文件更改端口启动jenkins

	vim /etc/sysconfig/jenkins

找到修改端口号：
<br/>JENKINS_PORT="8080"  # 此端口不冲突可以不修改<br/>

	service  jenkins  start

![](../7.png)
<br/>这里会报错 因为Jenkins默认找的jdk环境变量在/usr/bin下  我们需要更改下路径<br/>

	vim  /etc/init.d/jenkins

<br/>找到candidates="   这个配置项<br/>
![](../8.png)
<br/>可以使用这种方式找到路径<br/>
![](../9.png)
<br/>然后在次启动Jenkins    成功<br/>
![](../10.png)
<br/>在浏览器中访问<br/>
首次进入会要求输入初始密码如下图， 
<br/>![](../11.png)<br/>
初始密码在：/var/lib/jenkins/secrets/initialAdminPassword 
![](../12.png)
<br/>![](../13.png)<br/>
![](../14.png)
<br/>![](../15.png)<br/>
![](../16.png)
<br/>![](../17.png)<br/>
![](../18.png)
<br/>![](../19.png)<br/>
![](../20.png)


<h4 id="4">1.3安装subversion</h2>

配置好yum源 直接yum安装subversion 

	yum -y install subversion 
<br/>![](../21.png)<br/>
查看版本号

	svnserve --version

递归创建目录
	
	mkdir  /data/svn/program   -p

<br/>![](../22.png)<br/>
创建svn版本库

	svnadmin create /data/svn/program/

配置账号：

	vim /data/svn/program/conf/passwd
	
		[manager]
		xinlong = xinlong
<br/>![](../23.png)<br/>
配置权限：

	vim /data/svn/program/conf/authz

		[groups]
		manager = xinlong
 
		[program:/]
		@manager = rw
<br/>![](../24.png)<br/>
配置服务：

	vim /data/svn/program/conf/svnserve.conf
	
		anon-access = none ## 匿名用户可读(关闭)
		auth-access = write ## 授权用户可写
		password-db = /data/svn/program/conf/passwd ## 指定账号配置文件   绝对路径
		authz-db = /data/svn/program/conf/authz ## 指定权限配置文件  绝对路径
		realm = /data/svn/program ## 指定版本库的认证域，即在登录时提示的认证域名称。缺省值：一个UUID(Universal Unique IDentifier，全局唯一标示)。
<br/>![](../25.png)<br/>
启动subversion

开通HTTP协议 安装httpd及其svn模块
	
	yum -y install httpd mod_dav_svn
<br/>![](../26.png)<br/>
确认模块 dav/dav_svn 已加载
<br/>(Centos6  路径是/etc/httpd/conf/httpd.conf )<br/>

	grep -E "dav_module" /etc/httpd/conf.modules.d/00-dav.conf
<br/>![](../27.png)<br/>
( Centos6  路径是 /etc/httpd/conf.d/subversion.conf )

	grep -E "dav_svn_module" /etc/httpd/conf.modules.d/10-subversion.conf
<br/>![](../28.png)<br/>
SVN HTTP 配置

	vim /etc/httpd/conf/httpd.conf
		
		<Location /program>
    	DAV svn
    	SVNPath /data/svn/program
    	AuthType Basic
    	AuthName "SVN program repository"
    	AuthUserFile /data/svn/program/conf/svn-auth.htpasswd
    	AuthzSVNAccessFile /data/svn/program/conf/authz
    	# Authorization: Authenticated users only
    	# SVNListParentPath on
    	Satisfy all
    	Require valid-user
		</Location>

<br/>![](../29.png)<br/>
创建 SVN HTTP 用户

	-m 表示以 md5 加密密码

	touch  /data/svn/program/conf/svn-auth.htpasswd
<br/>![](../30.png)<br/>

	htpasswd -m  /data/svn/program/conf/svn-auth.htpasswd    xinlong
<br/>![](../31.png)<br/>
启动httpd服务
<br/>![](../32.png)<br/>
客户端验证(http://xxx)

Windows 下使用 Chrome 浏览器访问: http://ip/program/，输入用户名 chalres 及其密码，成功。
<br/>![](../33.png)<br/>
<br/>TortoiseSVN检测<br/>
右击  点击SVN checkout 
<br/>![](../34.png)<br/>
![](../35.png)
<br/>![](../36.png)<br/>
![](../37.png)
<br/>![](../38.png)<br/>
然后右击 点击SVN commit

![](../39.png)
<br/>![](../40.png)<br/>
![](../41.png)
<br/>![](../42.png)<br/>
访问网站也可以看到

![](../43.png)
<br/>![](../44.png)<br/>
### 进入Jenkins的主界面点击新建或创建一个新任务<br/>
<br/>输入项目的名字选择自由风格点击OK<br/>
![](../45.png)
<br/>![](../46.png)<br/>
选择源码管理中的Subversion(SVN) 填写第五步搭建SVN的地址(里面需要有代码)
<br/>![](../47.png)<br/>
![](../48.png)
<br/>![](../49.png)<br/>
![](../50.png)
<br/>![](../51.png)<br/>
![](../52.png)
<br/>![](../53.png)<br/>
![](../54.png)

	        #!/bin/bash
        date=`date +"%H:%M"`
        file=`ls -l  /data/program/  |grep db  |awk -F" " '{print $(NF-1)}'`
        if [ "$date" == "$file" ];then
        echo "no"
        else
        echo "checkout"
        svn  checkout  http://192.168.1.240/program/  /data/install/   --username  xinlong
        echo "OK" >/data/ok.txt
        echo "OK"
        fi
	

<br/>![](../55.png)<br/>
![](../56.png)
<br/>![](../57.png)<br/>
![](../58.png)
<br/>![](../59.png)<br/>
![](../60.png)
<br/>![](../61.png)<br/>
![](../62.png)
<br/>![](../63.png)<br/>
![](../64.png)
<br/>![](../65.png)<br/>
![](../66.png)
<br/>![](../67.png)<br/>
![](../68.png)
<br/>![](../69.png)<br/>


### 下面步骤可以更改http svn 为https
<br/>开通 HTTPS 协议<br/>
### 3.1 安装 ssl 模块
	yum -y install mod_ssl openssl

### 3.2 生成证书

	mkdir /etc/httpd/ssl
	cp nginx.key /etc/httpd/ssl/httpd.key
	cp nginx.crt /etc/httpd/ssl/httpd.crt

### 3.3 配置证书

	vim /etc/httpd/conf.d/ssl.conf
	SSLCertificateFile    /etc/httpd/ssl/httpd.crt
	SSLCertificateKeyFile /etc/httpd/ssl/httpd.key

如果要停用 https 改用 http，只需注释下面的 SSLRequireSSL 一行。

	vim /etc/httpd/conf/httpd.conf

	<Location /program>
    	## ......
	
    	Require valid-user
    	SSLRequireSSL
	</Location>

### 3.4 重启服务

	systemctl restart httpd

### 3.5 防火墙放行

	vim /etc/sysconfig/iptables
	-A INPUT -m state --state NEW -m tcp -p tcp --dport 443 -j ACCEPT

重启生效

	sudo systemctl restart iptables

### 3.6 客户端验证(https://xxx)
<br/>Windows 下使用 Chrome 浏览器访问: https://ip/program/，输入用户名 charles 及其密码，成功。此时只能使用 https 访问，http 已被禁用。<br/>
<br/><br/>
<br/><br/>


