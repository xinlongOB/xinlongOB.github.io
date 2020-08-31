---
title: 阿里云服务器如何设置IPV6通过appstore的审核
tags:
  - ipv6
  - linux
categories: 运维
date: 2020-08-20 15:02:10
---
苹果上架要求：要求支持IPV6only(因为阿里云主机没有IPV6only)
确认IPV6是否开启：

方式1：使用ifconfig查看自己的IP地址是否含有IPV6地址
![](../1.png)
![](../2.png)
方式2：查看服务器监听的IP中是否有IPV6格式的地址(netstat -tuln)
![](../3.png)

## 开启IPV6
```bash
vim /etc/sysctl.conf
```
![](../4.png)
```bash
vim /etc/modprobe.d/disable_ipv6.conf
```
![](../5.png)
```bash
vim /etc/sysconfig/network
```
![](../6.png)
至此ipv6的服务器端支持已经完成,重启服务器测试是否支持ipv6,重启后, ifconfig查看ipv6的信息,有看到有关IPV6的输出就可以
![](../7.png)

## 添加ipv6隧道
注册Tunnel broker
https://www.tunnelbroker.net/
注册很容易,就不讲了,注册需要邮箱验证,gmail、163能收得到认证邮件,qq还是一样收不到

创建通道“Create Regular Tunnel”
填写云服务器ip以及选择默认的隧道节点,点击Create Tunnel创建。填写ip都,如果出现“IP is a potential tunnel endpoint.”则证明可以添加ipv6隧道,一般隧道节点系统已经默认分配,可以手动选择,大家可以在自己的云服务器上分别ping一下这些ip,选时延低的
![](../8.png)
创建ipv6隧道及路由
到下一页面切换到Example configurations选项卡,如果你的VPS是centOS/Debian这些常见Linux的话,下拉菜单选择Linux-route2,出现了设置的命令,复制到自己的云服务器上运行
>注：一定要一条一条的复制

![](../9.png)
测试ipv6
![](../10.png)
添加ipv6的dns服务器,在最后添加 nameserver 2001:4860:4860::8888,nameserver 2001:4860:4860::8844 谷歌的ipv6 dns服务器

```bash
 vim /etc/resolv.conf
    options timeout:1 attempts:1 rotate
    nameserver x.x.x.x
    nameserver x.x.x.x
    nameserver 2001:4860:4860::8888
    nameserver 2001:4860:4860::8844 
```

```bash
 ping6 -c 5 ipv6.google.com

    PING ipv6.google.com(tsa03s01-in-x0e.1e100.net) 56 data bytes
    64 bytes from tsa03s01-in-x0e.1e100.net: icmp_seq=1 ttl=55 time=25.5 ms
    64 bytes from tsa03s01-in-x0e.1e100.net: icmp_seq=2 ttl=55 time=25.5 ms
    64 bytes from tsa03s01-in-x0e.1e100.net: icmp_seq=3 ttl=55 time=33.1 ms
    64 bytes from tsa03s01-in-x0e.1e100.net: icmp_seq=4 ttl=55 time=25.5 ms
    64 bytes from tsa03s01-in-x0e.1e100.net: icmp_seq=5 ttl=55 time=25.4 ms

    --- ipv6.google.com ping statistics ---
    5 packets transmitted, 5 received, 0% packet loss, time 4031ms
    rtt min/avg/max/mdev = 25.473/27.040/33.180/3.073 ms
```
阿里云服务配置

 代理配置好之后服务器中执行ifconfig命令,找到he-ipv6虚拟网卡,找到scope为Global 的ipv6地址,在阿里云后台配置AAAA记录为上面提到的ipv6地址
![](../11.png)