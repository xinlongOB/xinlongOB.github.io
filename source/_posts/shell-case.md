---
title: Shell脚本case判断
tags:
  - shell
  - linux
categories: 运维
date: 2020-07-24 17:05:56
---
## if =~ 判断是否包含
```bash
#!/bin/bash
a=`echo "hhomepageaha batchecommerce" | grep -Eo "batch|dictionary|ecommerce|gateway|homepage|module|products" | xargs`
echo $a
if [[ "$a" =~ "batch" ]];then
        echo "batch"
elif [[  "$a" =~ "dictionary" ]];then
        echo "dictionary"
else
        echo "nono"
fi
```

## case判断
格式
```bash
case $变量名 in
    "值1")
        程序1
        ;;
    "值2")
        程序2
        ;;
    ...
    *)
        程序n
        ;;
esac
```
实例
```bash
#!/bin/bash
read -p "Please choose yes/no: " -t 30 cho    # read 输入 赋值cho
echo $cho                                     # 打印cho的值
case $cho in                                  # 判断
    "yes")                                    # 如果为yes
        echo "you input is yes"               # 输出
        ;;
    "no")                                     # 如果为no
        echo "you input is no"                # 输出
        ;;
    "*")                                      # 如果为其他
        echo "input error"                    # 输出错误
        ;;
esac                                          # 结尾
```
```bash
#!/bin/bash
read -p "Please choose yes/no: " -t 30 cho
echo $cho
case $cho in
    "yes")
        echo "you input is yes"
        ;;
    "no")
        echo "you input is no"
        ;;
    "*")
        echo "input error"
        ;;
esac
```
tomcat重启脚本
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