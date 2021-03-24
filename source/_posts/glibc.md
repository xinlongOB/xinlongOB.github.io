---
title: centos下安装node版本报错升级lib库
tags:
  - node
  - linux
categories: 运维
date: 2021-03-22 10:07:29
---
## GLIBCXX报错 
```bash
node: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.14' not found (required by node)
node: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.18' not found (required by node)
node: /usr/lib64/libstdc++.so.6: version `CXXABI_1.3.5' not found (required by node)
node: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.15' not found (required by node)
```
首先检查动态库
```bash
# strings /usr/lib64/libstdc++.so.6 | grep GLIBC
GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBC_2.2.5
GLIBC_2.3
GLIBC_2.4
GLIBC_2.3.2
GLIBCXX_FORCE_NEW
GLIBCXX_DEBUG_MESSAGE_LENGTH
```
发现最高只有 GLIBCXX_3.4.13，所以这里需要下载最新gcc库：
```bash
cd /opt
wget http://ftp.tsukuba.wide.ad.jp/software/gcc/releases/gcc-8.3.0/gcc-8.3.0.tar.gz
```
解压安装
```bash
tar -zxvf gcc-8.3.0.tar.gz
cd gcc-8.3.0/ 

./contrib/download_prerequisites

mkdir build

cd build   

../configure --enable-checking=release --enable-languages=c,c++ --disable-multilib

make && make install  # 时间非常久

cp /usr/local/lib64/libstdc++.so.6.0.25 /usr/lib64

cd /usr/lib64

rm -rf libstdc++.so.6

ln -s libstdc++.so.6.0.25 libstdc++.so.6
```
然后再次执行以下命令来查看是否包括 GLIBCXX_3.4.14
```bash
strings /usr/lib64/libstdc++.so.6 | grep GLIBC
GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBCXX_3.4.14
GLIBCXX_3.4.15
GLIBCXX_3.4.16
GLIBCXX_3.4.17
GLIBCXX_3.4.18
GLIBCXX_3.4.19
GLIBCXX_3.4.20
GLIBCXX_3.4.21
GLIBCXX_3.4.22
GLIBCXX_3.4.23
GLIBCXX_3.4.24
GLIBCXX_3.4.25
GLIBC_2.2.5
GLIBC_2.3
GLIBC_2.14
GLIBC_2.18
GLIBC_2.16
GLIBC_2.17
GLIBC_2.3.2
GLIBCXX_FORCE_NEW
GLIBCXX_DEBUG_MESSAGE_LENGTH
```

## GLIBC报错
```bash
node: /lib64/libc.so.6: version `GLIBC_2.17' not found (required by ./node)
```
查看系统中可使用的glibc版
```bash
//使用strings命令查看
strings /lib64/libc.so.6 |grep GLIBC_
//查看结果如下：
GLIBC_2.2.5
GLIBC_2.2.6
GLIBC_2.3
GLIBC_2.3.2
GLIBC_2.3.3
GLIBC_2.3.4
GLIBC_2.4
GLIBC_2.5
GLIBC_2.6
GLIBC_2.7
GLIBC_2.8
GLIBC_2.9
GLIBC_2.10
GLIBC_2.11
GLIBC_2.12
GLIBC_2.13
GLIBC_2.14
GLIBC_2.15
GLIBC_2.16
GLIBC_PRIVATE
```
下载高版本的glibc库
```bash
cd /opt
wget https://ftp.gnu.org/gnu/glibc/glibc-2.17.tar.gz
tar -xvf glibc-2.17.tar.gz
```
编译安装
```bash
#进入glibc-2.17目录中
cd glibc-2.17
#创建build目录
mkdir build
#进入build目录中
cd build
#执行./configure
../configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin
#安装
make && make install
```
查看共享库
```bash
ls -l /lib64/libc.so.6
=====================
//可以看到已经建立了软链接
lrwxrwxrwx. 1 root root 12 Jan 13 01:49 /lib64/libc.so.6 -> libc-2.17.so
```
再次查看系统中可使用的glibc版本
```bash
[root@localhost ~]# strings /lib64/libc.so.6 |grep GLIBC_
GLIBC_2.2.5
GLIBC_2.2.6
GLIBC_2.3
GLIBC_2.3.2
GLIBC_2.3.3
GLIBC_2.3.4
GLIBC_2.4
GLIBC_2.5
GLIBC_2.6
GLIBC_2.7
GLIBC_2.8
GLIBC_2.9
GLIBC_2.10
GLIBC_2.11
GLIBC_2.12
GLIBC_2.13
GLIBC_2.14
GLIBC_2.15
GLIBC_2.16
GLIBC_2.17
GLIBC_PRIVATE
```
