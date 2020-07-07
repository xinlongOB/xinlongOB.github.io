---
title: centos7设置python3环境
tags:
  - python
  linux
categories:
  - 运维
  - 开发
date: 2020-05-26 17:38:10
---
## 配置yum源-使用阿里云的镜像

    sudo  wget http://mirrors.aliyun.com/repo/Centos-7.repo
    sudo  wget http://mirrors.aliyun.com/repo/epel-7.repo
    yum clean  all

安装依赖工具包

    sudo  yum -y install zlib-devel bzip2-devel openssl-devel openssl-static ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel libffi-devel lzma gcc

## 下载python3.7安装包

    cd /opt/
    sudo   wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tar.xz

解压-->配置-->编译-->安装

    sudo tar xf  Python-3.7.0.tar.xz    -C /usr/local/
    cd  /usr/local/Python-3.7.0/
    sudo  ./configure --prefix=/usr/local/sbin/python-3.7
    sudo   make && sudo  make install

  安装成功会打印

      Collecting setuptools
      Collecting pip
      Installing collected packages: setuptools, pip
      Successfully installed pip-10.0.1 setuptools-39.0.1

## 测试

    [sgsm@bogon Python-3.7.0]$ /usr/local/sbin/python-3.7/bin/python3
    Python 3.7.0 (default, Jun  3 2020, 23:49:44) 
    [GCC 4.8.5 20150623 (Red Hat 4.8.5-39)] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> exit()

## 创建软连接
<br/>查看目前的链接文件<br/>

    [sgsm@bogon Python-3.7.0]$ ll /usr/bin/ |grep python
    lrwxrwxrwx.   1 root root           7 4月  13 2017 python -> python2
    lrwxrwxrwx.   1 root root           9 4月  13 2017 python2 -> python2.7
    -rwxr-xr-x.   1 root root        7136 11月  6 2016 python2.7
    -rwxr-xr-x.   1 root root        1835 11月  6 2016 python2.7-config
    lrwxrwxrwx.   1 root root          16 4月  13 2017 python2-config -> python2.7-config
    lrwxrwxrwx.   1 root root          14 4月  13 2017 python-config -> python2-config

删除原来的连接文件

    sudo rm -rf /usr/bin/python

创建新的连接文件

    sudo  ln -s /usr/local/sbin/python-3.7/bin/python3   /usr/bin/python

查看现在的版本

    [sgsm@bogon Python-3.7.0]$ python -V
    Python 3.7.0

## 报错
<br/>修改完python默认版本之后，会存不能执行yum命令，需要做一些修改，如下<br/>

     将/usr/bin/yum的顶部的：

    !/usr/bin/python  改成  !/usr/bin/python2.7 

    将/usr/libexec/urlgrabber-ext-down的顶部的：

    /usr/bin/python  改为   /usr/bin/python2.7

    将/usr/bin/yum-config-manager的顶部的

    #!/usr/bin/python 改为 #!/usr/bin/python2.7 


最后将pip指向到python3.7

    [sgsm@bogon Python-3.7.0]$ sudo ln -s /usr/local/sbin/python-3.7/bin/pip3 /usr/bin/pip 
    [sgsm@bogon Python-3.7.0]$ 
    [sgsm@bogon Python-3.7.0]$ pip -v
    pip 10.0.1 from /usr/local/sbin/python-3.7/lib/python3.7/site-packages/pip (python 3.7)