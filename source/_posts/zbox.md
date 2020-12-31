---
title: centos下禅道部署
tags:
  - zbox
  - linux
categories: 运维
date: 2020-12-31 16:42:30
---
## 部署禅道
```bash
64位下载：wget http://dl.cnezsoft.com/zentao/9.0.1/ZenTaoPMS.9.0.1.zbox_64.tar.gz
32位下载：wget http://dl.cnezsoft.com/zentao/9.0.1/ZenTaoPMS.9.0.1.zbox_32.tar.gz


tar -zxvf ZenTaoPMS.9.0.1.zbox_64.tar.gz -C /opt/   # 必须解压到这个目录

cd /opt/zbox/ 

./zbox start   # 启动Apache和MySQL

./zbox  -ap 8080 start  # 指定httpd端口号启动
```

## 修改禅道端口号

一、修改Apache端口


        首先，如果我们的服务器的80端口没有开放的话，那么我们就是只能修改Apache应用服务的端口了，其实非常简单，安装完成禅道后，在任意目录下输入命令：

        /opt/zbox/zbox  -h   //查看zbox的帮助命令

       /opt/zbox/zbox  -ap  8080  //修改Apache服务器的端口号为8080

       /opt/zbox/zbox  restart    //重启Apache服务器

        做完以上操作之后，禅道的端口号就被修改为8080了

二、修改mysql端口

       修改mysql端口，其实非常简单，做的操作和上面的类似：       


       /opt/zbox/zbox  -mp  8090  //修改mysql服务器的端口号为8090

       /opt/zbox/zbox  restart    //重启Apache服务器

       至此，禅道中带的mysql数据库端口就修改完成了

       但是，事情没有那么简单就可以了，我们修改了数据库的端口，但是禅道发布在Apache上的服务却不会认这个新发布的端口，这个时候，我们打开前端服务的地址，点击禅道的服务进入之后，整个页面就是一片空白。

        这个时候，我们还需要设置一下访问的数据库端口：

        在服务器上，我们先定位到以下位置：

                   /opt/zbox/app/zentao/config

        然后再里面找到my.php，用vi命令去操作：
![](../1.png)


第二种方式

进入到禅道安装目录 一般为 /opt/zbox

依次执行下面命令

./zbox stop                     #关闭禅道

./zbox -ap 8082             #更改禅道内置 apache 端口

./zbox -mp 3307            #更改禅道内置 mysql 端口

./zbox start                    #启动禅道