---
title: Tomcat简介和安装部署
tags:
  - tomcat
  - linux
categories: 运维
date: 2020-07-09 10:04:21
---
## 简介
Tomcat是Apache 软件基金会（Apache Software Foundation）的Jakarta 项目中的一个核心项目,由Apache、Sun 和其他一些公司及个人共同开发而成。由于有了Sun 的参与和支持,最新的Servlet 和JSP 规范总是能在Tomcat 中得到体现,Tomcat 5支持最新的Servlet 2.4 和JSP 2.0 规范,因为Tomcat 技术先进、性能稳定,而且免费,因而深受Java 爱好者的喜爱并得到了部分软件开发商的认可,成为目前比较流行的Web 应用服务器
Tomcat 服务器是一个免费的开放源代码的Web 应用服务器,属于轻量级应用服务器,在中小型系统和并发访问用户不是很多的场合下被普遍使用,是开发和调试JSP 程序的首选。对于一个初学者来说,可以这样认为,当在一台机器上配置好Apache 服务器,可利用它响应HTML（标准通用标记语言下的一个应用）页面的访问请求。实际上Tomcat是Apache 服务器的扩展,但运行时它是独立运行的,所以当你运行tomcat 时,它实际上作为一个与Apache 独立的进程单独运行的。诀窍是,当配置正确时,Apache 为HTML页面服务,而Tomcat 实际上运行JSP 页面和Servlet。另外,Tomcat和IIS等Web服务器一样,具有处理HTML页面的功能,另外它还是一个Servlet和JSP容器,独立的Servlet容器是Tomcat的默认模式。不过,Tomcat处理静态HTML的能力不如Apache服务器

## Tomcat目录详解
```bash
tomcat
|---bin Tomcat：存放启动和关闭tomcat脚本；
|---confTomcat：存放不同的配置文件（server.xml和web.xml）；
|---doc：存放Tomcat文档；
|---lib/japser/common：存放Tomcat运行需要的库文件（JARS）；
|---logs：存放Tomcat执行时的LOG文件；
|---src：存放Tomcat的源代码；
|---webapps：Tomcat的主要Web发布目录（包括应用程序示例）；
|---work：存放jsp编译后产生的class文件；
```
## 安装Java JDK
```bash
	环境：centos7.2
	软件包：jdk-8u60-linux-x64.tar.gz
```
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

## 安装Tomcat
下载地址：https://tomcat.apache.org
选择版本然后点击发行版本下载
![](../77.png)
下载完成后使用lrzsz 或者 ftp 工具拉取到服务器
```bash
tar -zvxf apache-tomcat-8.0.0-RC10.tar.gz -C  /usr/local/
mv  /usr/local/apache-tomcat-8.0.0-RC10  /usr/local/tomcat
```
创建测试页
```bash
cd /usr/local/tomcat/webapps/ROOT/
echo '<%= new java.util.Date() %>' > test.jsp
```
启动tomcat
```bash
cd  /usr/local/tomcat/bin
./startup.sh 
```
设置密码
```bash
	vim /usr/local/tomcat/conf/tomcat-users.xml

	<role rolename="manager-gui"/>
	<user username="tomcat" password="123" roles="manager-gui"/>
```
重启tomcat
```bash
./shutdown.sh    # 停止
./startup.sh     # 启动
```
## Tomcat 重启脚本
```bash
vim /etc/init.d/tomcatd

#!/bin/sh
#chkconfig: 2345 10 90
# description: Starts and Stops the Tomcat daemon.
#
##############################################
#Startup script for Tomcat on Linux

#filename tomcat.sh

#Make sure the java and the tomcat installation path has been added to the PATH
JAVA_HOME=/application/jdk                 #JDK安装目录
CATALINA_HOME=/usr/local/tomcat           #tomcat安装目录
export JAVA_HOME
export CATALINA_HOME

###############################################
start_tomcat=$CATALINA_HOME/bin/startup.sh                  #tomcat启动文件
stop_tomcat=$CATALINA_HOME/bin/shutdown.sh                  #tomcat关闭文件
start() {                                                              
        echo -n "Starting tomcat: "
        ${start_tomcat}
        echo "tomcat start ok."
}
stop() {
        echo -n "Shutting down tomcat: "
        ${stop_tomcat}
        echo "tomcat stop ok."
}
status(){  
        numproc=`ps -ef | grep catalina | grep -v "grep catalina" | wc -l`  
        if [ $numproc -gt 0 ]; then  
            echo "Tomcat is running..."  
        else  
            echo "Tomcat is stopped..."  
        fi  
}
# See how we were called
                                                   
case "$1" in
  start)
        start
        ;;
  stop)
        stop
        ;;
  status)
        status
        ;;
  restart)
        stop
        sleep 10
        start
        ;;
  *)
        echo "Usage: $0 {start|stop|restart}"
esac
exit 0
```
1.给脚本权限：chmod 755 tomcat
2.添加到服务：chkconfig --add tomcat
3.开机启动项：chkconfig --level 345 tomcat on
4.现在可以通过 systemctl start  tomcat  命令启动 Tomcat 了

## 