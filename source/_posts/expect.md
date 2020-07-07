---
title: linux expect介绍和用法
tags:
  - expect
  - linux
date: 2020-07-06 16:17:34
---
## 简介
expect是一个自动化交互套件,主要用于执行命令和程序时,系统以交互形式要求输入指定字符串,实现交互通信
## 流程
expect自动交互流程
```bash
spawn启动指定进程-->expect获取指定关键字-->send向程序发送指定字符-->执行完成退出
```
## 用法
执行脚本之前需要安装expect这个软件
```bash
sudo yum install  expect   -y
```
expect常用选项
```bash
spawn             交互程序开始后面跟命令或者指定程序
expect            获取匹配信息匹配成功则执行expect后面的程序动作
send              用于发送指定的字符串信息
exp_continue      在expect中多次匹配就要用到
send_user         用来打印输出  相当于shell中的echo
exit              退出expect脚本
eof               expect执行结束 退出
set               定义变量
puts              输出变量
set  timeout      设置超时时间
```
## 示例
```bash
#!/bin/bash

passwd='test220'   # 定义密码

/usr/bin/expect <<-EOF   # 定义结束符

set time 30   # 定义超时时间
spawn ssh sgsm@192.168.1.220 df -Th   # 执行的命令-- 连接到192.168.1.220  指定df -Th
expect {
"*yes/no" { send "yes\r"; exp_continue  }   # 输入yes 然后回车 --\r是回车换行符 \n只是回车
"*password:" { send "$passwd\r"  }    # 输入密码 然后回车

}
expect eof   # 结束
EOF  # 结束
```
复制这条段,上一段主要用于备注(格式有误)
```bash
#!/bin/bash
passwd='test220'

/usr/bin/expect <<-EOF

set time 30
spawn ssh sgsm@192.168.1.220 df -Th
expect {
"*yes/no" { send "yes\r"; exp_continue  }
"*password:" { send "$passwd\r"  }
}
expect eof
EOF
```
## 非交互式建立免密登录
```bash
#!/bin/bash
user=`cat hosts | cut -d " " -f 2`
ip=`cat hosts | cut -d " " -f 1`
passwd=`cat hosts | cut -d " " -f 3`
    
    expect <<EOF
      set timeout 10
      spawn ssh-copy-id $user@$ip
      expect {
        "yes/no" { send "yes\n";exp_continue }
        "password" { send "$passwd\n" }
      }
     expect "password" { send "$passwd\n" }
EOF
```
## 创建ssh key，免密登录所有主机
创建主机文件
```bash
[root@localhost script]# cat host 
192.168.1.10 root 123456
192.168.1.20 root 123456
192.168.1.30 root 123456
```
创建脚本
```bash
#!/bin/bash

# 判断id_rsa密钥文件是否存在
if [ ! -f ~/.ssh/id_rsa ];then
 ssh-keygen -t rsa -P "" -f ~/.ssh/id_rsa
else
 echo "id_rsa has created ..."
fi
#分发到各个节点,这里分发到host文件中的主机中.
while read line
  do
user=`echo $line  | cut -d " " -f 2`
ip=`echo $line| cut -d " " -f 1`
passwd=`echo $line | cut -d " " -f 3`
    
    expect <<EOF
      set timeout 10
      spawn ssh-copy-id $user@$ip
      expect {
        "yes/no" { send "yes\n";exp_continue }
        "password" { send "$passwd\n" }
      }
     expect "password" { send "$passwd\n" }
EOF
done < hosts
```
## 如果密码一样 可以使用for循环更简单点
```bash
#!/bin/bash
USER=root
PASSWD=1
IP=`cat hosts|awk '{print $1}' |tr -s "\n" " "`
for i in $IP
do
    expect << EOF
    spawn ssh-copy-id $USER@$i
    expect {
    "*yes/no"  { send "yes\r";exp_continue }
    "password" { send "$PASSWD\r" }
}
    expect "password" { send "$passwd\n" }
EOF
done
```


```bash
#!/bin/bash
user=sgsm
while read line
  do
    passwd="test122"
    expect <<EOF
      set timeout 10
      spawn ssh-copy-id $user@192.168.1.122
      expect {
        "*yes/no" { send "yes\r"; exp_continue   }
        "*password" { send "$passwd\r" }
      }
     expect "password" { send "$passwd\r" }
EOF
done < hosts
```