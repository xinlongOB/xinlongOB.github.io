---
title: 大于2T的硬盘需要parted磁盘分区
tags:
  - parted
  - Linux
categories: 运维
date: 2020-05-16 16:23:32
---
## 首先安装parted

    yum install parted   -y

查看硬盘情况使用fdisk -l 查看分区情况，对于大于2TB的硬盘用parted分区
<br/>格式化 /dev/sdb<br/>
  
    parted /dev/sdb

使用print打印分区信息

    (parted) print

将分区设置成gpt格式

    mklabel gpt    

将所有空间创建一个分区

    mkpart primary 0 100%

退出

    quit

## 将硬盘分为两个主分区

    [root@localhost ~]# parted /dev/sdb   
    GNU Parted 1.8.1 Using /dev/sdb Welcome to GNU Parted! Type ‘help’ to view a list of commands.
    (parted) mklabel gpt           # 将MBR磁盘格式化为GPT
    (parted) print                       #打印当前分区
    (parted) mkpart primary 0 4.5TB                # 分一个4.5T的主分区
    (parted) mkpart primary 4.5TB 12TB      # 分一个7.5T的主分区
    (parted) print                         #打印当前分区
    (parted) quit 退出
