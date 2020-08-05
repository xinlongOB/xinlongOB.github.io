---
title: CentOS7下安装Slowhttptest DDoS检测工具
tags:
  - slowhttptest
  - linux
categories: 运维
date: 2020-08-05 10:07:07
---
## Slowhttptest简介
Slowhttptest是依赖HTTP协议的慢速攻击DoS攻击工具，设计的基本原理是服务器在请求完全接收后才会进行处理，如果客户端的发送速度缓慢或者发送不完整，服务端为其保留连接资源池占用，大量此类请求并发将导致DoS。

## 攻击方式
slowloris：完整的http请求是以\r\n\r\n结尾，攻击时仅发送\r\n，少发送一个\r\n，服务器认为请求还未发完，就会一直等待直至超时。等待过程中占用连接数达到服务器连接数上限，服务器便无法处理其他请求。

slow http post：原理和slowloris有点类似，这次是通过声明一个较大的content-length后，body缓慢发送，导致服务器一直等待

slow read attack：向服务器发送一个正常合法的read请求，请求一个很大的文件，但认为的把TCP滑动窗口设置得很小，服务器就会以滑动窗口的大小切割文件，然后发送。文件长期滞留在内存中，消耗资源。这里有两点要注意：
    1.tcp窗口设置要比服务器的socket缓存小，这样发送才慢。
    2.请求的文件要比服务器的socket缓存大，使得服务器无法一下子将文件放到缓存，然后去处理其他事情，而是必须不停的将文件切割成窗口大小，再放入缓存。同时攻击端一直说自己收不到。

## 安装Slowhttptest
安装Slowhttptest需要依赖以下组件，可按照以下步骤来进行(如果组件已经安装，可以跳过)：
```bash
# 安装libssl-dev   C++编译器
 yum install openssl openssl-devel   gcc-c++   -y
```
安装m4、autoconf、perl和automake
```bash
# 方法一：通过yum直接安装
yum install m4
yum install autoconf
yum install perl
yum install automake 
# 注意：需要按照顺序进行安装，因为automake依赖于m4，autoconf和perl，autoconf依赖于m4
```
本例中automake是通过下载包进行安装，原因为通过yum中的版本1.13较低，而安装slowhttptest需要automake-1.16版本,所以需要使用编译安装
```bash
# 方法二：通过直接下载包文件进行安装（m4、autoconf和automake是GNU中的开源软件，本例中automake安装在/usr/local/下）
# 安装目录
cd /usr/local/
# 安装m4
wget http://ftp.gnu.org/gnu/m4/m4-1.4.18.tar.gz
tar -zxvf m4-1.4.18.tar.gz
cd m4-1.4.18/
./configure   && make   && make install
cd ..
# 安装autoconf
wget http://ftp.gnu.org/gnu/autoconf/autoconf-2.69.tar.gz
tar -zxvf autoconf-2.69.tar.gz
cd  autoconf-2.69/
./configure   && make   && make install
cd ..
# 安装automake
wget http://ftp.gnu.org/gnu/automake/automake-1.16.1.tar.gz
tar -xvf automake-1.16.1.tar.gz
cd automake-1.16.1/
./configure --prefix=/usr/   &&  make  && make install
```

安装slowhttptest
```bash
# 代码托管在https://github.com/shekyan/slowhttptest   需要使用git下载
yum install  git    -y
git  clone   https://github.com/shekyan/slowhttptest
cd slowhttptest/
./configure  &&  make  && make install
# 安装后可以使用 slowhttptest  -h  查看帮助文档
slowhttptest  -h  
```
## 运行Slowhttptest
参数：
```bash
 -g      在测试完成后，以时间戳为名生成一个CVS和HTML文件的统计数据
 -H      SlowLoris模式
 -B      Slow POST模式
 -R      Range Header模式
 -X      Slow Read模式
 -c      number of connections 测试时建立的连接数
 -d      HTTP proxy host:port  为所有连接指定代理
 -e      HTTP proxy host:port  为探测连接指定代理
 -i      seconds 在slowrois和Slow POST模式中，指定发送数据间的间隔。
 -l      seconds 测试维持时间
 -n      seconds 在Slow Read模式下，指定每次操作的时间间隔。
 -o      file name 使用-g参数时，可以使用此参数指定输出文件名
 -p      seconds 指定等待时间来确认DoS攻击已经成功
 -r      connections per second 每秒连接个数
 -s      bytes 声明Content-Length header的值
 -t      HTTP verb 在请求时使用什么操作，默认GET
 -u      URL  指定目标url
 -v      level 日志等级（详细度）
 -w      bytes slow read模式中指定tcp窗口范围下限
 -x      bytes 在slowloris and Slow POST tests模式中，指定发送的最大数据长度
 -y      bytes slow read模式中指定tcp窗口范围上限
 -z      bytes 在每次的read()中，从buffer中读取数据量
```
实例：
```bash
使用以下攻击命令，slowhttptest -c 5000 -u [hostname/ip]

    -c 表示发起5000个连接，由于是慢速DDOS且是基于http协议的，这里发起的连接请求是确确实实会与服务器进行三次握手并维持与服务器的连接的
    -u 注意这里的 hostname 或者 ip 都需要在前面加上协议 http://
```
```bash
slowloris模式：
slowhttptest -c 1000 -H -g -o my_header_stats -i 10 -r 200 -t GET -u https://xxxxxx.xxxxx.xx -x 24 -p 3

slow post模式：
slowhttptest -c 3000 -B -g -o my_body_stats -i 110 -r 200 -s 8192 -t FAKEVERB -u http://xxx.xxx.xxx -x 10 -p 3

slow read模式：
slowhttptest -c 8000 -X -r 200 -w 512 -y 1024 -n 5 -z 32 -k 3 -u https://xxx.xxx.xxx -p 3
```


## 报错解决
直接下载包文件进行安装时遇到的一些问题和解决方法：

问题1：执行./configure时提示“没有权限”。

解决方法：可以用以下命令解决。
```bash
	chmod -R 777 ./
```
　　
问题2：安装automake-1.16在make失败，提示–no-discard-stderr。

解决方法：修改automake-1.16文件夹下Makefile源代码第3694行，在其后增加–no-discard-stderr：
```bash
#3694行  automake-$(APIVERSION) --no-discard-stderr
```

问题3：安装automake-1.16后，后续对slowhttptest执行make操作时仍然提示missing automake-1.16 –foreign
解决方法：建议安装automake-1.16.1