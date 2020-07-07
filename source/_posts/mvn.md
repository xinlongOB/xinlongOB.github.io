---
title: mvn环境配置
tags:
  - mvn
  - linux
categories: 运维
date: 2020-05-18 17:08:00
---
## 配置jdk环境变量
创建目录

    mkdir /application/ 

解压jdk包到创建的目录中

    tar xf jdk-8u60-linux-x64.tar.gz   -C /application/

做软连接

    ln -s  /application/jdk1.8.0_60/ /application/jdk

设置环境变量

    sed -i.ori '$a export  JAVA_HOME=/application/jdk\nexport PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH\nexport  CLASSPATH=.$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$JAVA_HOME/lib/tools.jar'  /etc/profile

source一下生效环境变量

## 配置mvn环境变量

    cd /application/ 

下载mvn包

    wget http://mirrors.hust.edu.cn/apache/maven/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.tar.gz

解压

    tar -zxvf apache-maven-3.5.4-bin.tar.gz

vim /etc/profile

    export MAVEN_HOME=/application/apache-maven-3.0.5
    export PATH=$PATH:$MAVEN_HOME/bin

source一下生效环境变量

最后可以使用mvn -v 查看

    [sgsm@localhost weblog]$ mvn  -v
    Apache Maven 3.5.4 (1edded0938998edf8bf061f1ceb3cfdeccf443fe; 2018-06-18T02:33:14+08:00)
    Maven home: /home/sgsm/test3/apache-maven-3.5.4
    Java version: 1.8.0_60, vendor: Oracle Corporation, runtime: /application/jdk1.8.0_60/jre
    Default locale: zh_CN, platform encoding: UTF-8
    OS name: "linux", version: "3.10.0-514.21.2.el7.x86_64", arch: "amd64", family: "unix"
    

接下来就可以使用mvn打jar包了

    mvn clean package 