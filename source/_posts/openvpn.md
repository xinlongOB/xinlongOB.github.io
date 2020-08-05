---
title: CentOS7 搭建OpenVPN服务器
tags:
  - openvpn
  - linux
categories: 运维
date: 2020-07-31 16:34:39
---
## 简介和工作原理
VPN直译就是虚拟专用通道,是提供给企业之间或者个人与公司之间安全数据传输的隧道,OpenVPN无疑是Linux下开源VPN的先锋,提供了良好的性能和友好的用户GUI
它大量使用了OpenSSL加密库中的SSLv3/TLSv1协议函数库
目前OpenVPN能在Solaris、Linux、OpenBSD、FreeBSD、NetBSD、Mac OS X与Microsoft Windows以及Android和iOS上运行,并包含了许多安全性的功能。它并不是一个基于Web的VPN软件,也不与IPsec及其他VPN软件包兼容

OpenVPN 是一个基于 OpenSSL 库的应用层 VPN 实现。和传统 VPN 相比,它的优点是简单易用

OpenVPN服务器一般需要配置一个虚拟IP地址池和一个自用的静态虚拟IP地址（静态地址和地址池必须在同一个子网中）,然后为每一个成功建立SSL连接的客户端动态分配一个虚拟IP地址池中未分配的地址。这样,物理网络中的客户端和OpenVPN服务器就连接成一个虚拟网络上的星型结构局域网,OpenVPN服务器成为每个客户端在虚拟网络上的网关。OpenVPN服务器同时提供对客户端虚拟网卡的路由管理。当客户端对OpenVPN服务器后端的应用服务器的任何访问时,数据包都会经过路由流经虚拟网卡,OpenVPN程序在虚拟网卡上截获数据IP报文,然后使用SSL协议将这些IP报文封装起来,再经过物理网卡发送出去。OpenVPN的服务器和客户端在虚拟网卡之上建立起一个虚拟的局域网络,这个虚拟的局域网对系统的用户来说是透明的

OpenVPN的服务器和客户端支持tcp和udp两种连接方式,只需在服务端和客户端预先定义好使用的连接方式（tcp或udp）和端口号,客户端和服务端在这个连接的基础上进行SSL握手。连接过程包括SSL的握手以及虚拟网络上的管理信息,OpenVPN将虚拟网上的网段、地址、路由发送给客户端。连接成功后,客户端和服务端建立起SSL安全连接,客户端和服务端的数据都流入虚拟网卡做SSL的处理,再在tcp或udp的连接上从物理网卡发送出去

## 环境部署
```bash
192.168.1.89 外网     39.19.18.30 内网

系统环境
[root@localhost ~]# cat /etc/centos-release
CentOS Linux release 7.5.1804 (Core) 
[root@localhost ~]# 

[root@localhost ~]# uname -r
3.10.0-862.el7.x86_64
[root@localhost ~]# 
```
### 制作ca证书
首先我们先使用easy-rsa制作openVPN证书
下载并解压easy-rsa软件包
```bash
yum install  lrzsz  unzip   wget   -y
mkdir /data/tools -p 
wget -P /data/tools http://down.i4t.com/easy-rsa.zip
unzip -d /usr/local /data/tools/easy-rsa.zip
```
在开始制作CA证书之前,我们还需要编辑vars文件,修改如下相关选项
```bash
vi  /usr/local/easy-rsa-old-master/easy-rsa/2.0/vars
    export KEY_COUNTRY="cn"
    export KEY_PROVINCE="BJ"
    export KEY_CITY="BJ"
    export KEY_ORG="abcdocker"
    export KEY_EMAIL="cyh@i4t.com"
    export KEY_CN=abc
    export KEY_NAME=abc
    export KEY_OU=abc

    #行数大约67行开始,主要是修改默认的注册信息,比如注册公司、公司名称、部门、国家城市等
```
>注意：以上内容,我们也可以使用系统默认的,也就是说不进行修改也是可以使用的
然后使用使环境变量生效
```bash
#初始化环境边看
source vars
./clean-all

#注意：执行clean-all命令会在当前目录下创建一个名词为keys的目录
```
接下来开始正式制作CA证书,命令如下
```bash
./build-ca

# 生成根证书ca.crt和根密钥ca.key
#因为在vars中填写了证书的基本信息,所以这里一路回车即可
```
可以查看keys到目录,已经生成ca.crt和ca.key两个文件,其中ca.crt证书文件
```bash
[root@localhost 2.0]# ls keys/
ca.crt  ca.key  index.txt  serial
```
### 制作Server端证书
为服务端生成证书和密钥
```bash
#一直回车,2个Y

[root@localhost 2.0]# ./build-key-server server
....
An optional company name []:
Using configuration from /usr/local/easy-rsa-old-master/easy-rsa/2.0/openssl-1.0.0.cnf
Check that the request matches the signature
Signature ok
The Subject's Distinguished Name is as follows
countryName           :PRINTABLE:'cn'
stateOrProvinceName   :PRINTABLE:'BJ'
localityName          :PRINTABLE:'BJ'
organizationName      :PRINTABLE:'abcdocker'
organizationalUnitName:PRINTABLE:'abc'
commonName            :PRINTABLE:'abc'
name                  :PRINTABLE:'abc'
emailAddress          :IA5STRING:'cyh@i4t.com'
Certificate is to be certified until Jan 31 14:01:35 2030 GMT (3650 days)
Sign the certificate? [y/n]:y


1 out of 1 certificate requests certified, commit? [y/n]y
Write out database with 1 new entries
Data Base Updated

# 这里的server就是我们server端的证书 
```
查看新生成的证书
```bash
[root@localhost 2.0]# ls keys
01.pem   abc.key  index.txt       serial
server.crt  ca.crt   index.txt.attr  serial.old
server.csr  ca.key   index.txt.old
```
这里我们已经生成了server.crt、server.key、server.csr三个文件,其中server.crt和server.key两个文件是我们需要使用的

### 制作Client端证书
这里我们创建2个用户,分别为client1和client2
```bash
#每一个登陆的VPN客户端需要有一个证书,每个证书在同一时刻只能供一个客户端连接,下面建立2份
#为客户端生成证书和密钥（一路按回车,直到提示需要输入y/n时,输入y再按回车,一共两次）
./build-key client1
./build-key client2
```
每一个登陆的VPN客户端需要有一个证书,每个证书在同一时刻只可以一个客户端连接(可以修改配置文件)

现在为服务器生成加密交换时的Diffie-Hellman文件
```bash
./build-dh
# 创建迪菲·赫尔曼密钥,会生成dh2048.pem文件（生成过程比较慢,在此期间不要去中断它）
```
证书生成完毕
```bash
[root@localhost 2.0]# ll keys
总用量 116
-rw-r--r-- 1 root root 8224 7月  31 14:36 01.pem
-rw-r--r-- 1 root root 8107 7月  31 14:37 02.pem
-rw-r--r-- 1 root root 8107 7月  31 14:37 03.pem
-rw-r--r-- 1 root root 2431 7月  31 14:36 ca.crt
-rw------- 1 root root 3272 7月  31 14:36 ca.key
-rw-r--r-- 1 root root 8107 7月  31 14:37 client1.crt
-rw-r--r-- 1 root root 1777 7月  31 14:37 client1.csr
-rw------- 1 root root 3272 7月  31 14:37 client1.key
-rw-r--r-- 1 root root 8107 7月  31 14:37 client2.crt
-rw-r--r-- 1 root root 1777 7月  31 14:37 client2.csr
-rw------- 1 root root 3272 7月  31 14:37 client2.key
-rw-r--r-- 1 root root  424 7月  31 14:38 dh2048.pem
-rw-r--r-- 1 root root  410 7月  31 14:37 index.txt
-rw-r--r-- 1 root root   21 7月  31 14:37 index.txt.attr
-rw-r--r-- 1 root root   21 7月  31 14:37 index.txt.attr.old
-rw-r--r-- 1 root root  273 7月  31 14:37 index.txt.old
-rw-r--r-- 1 root root    3 7月  31 14:37 serial
-rw-r--r-- 1 root root    3 7月  31 14:37 serial.old
-rw-r--r-- 1 root root 8224 7月  31 14:36 server.crt
-rw-r--r-- 1 root root 1777 7月  31 14:36 server.csr
-rw------- 1 root root 3272 7月  31 14:36 server.key
[root@localhost 2.0]# 
```
其中包含了一个client1用户和client2用户的证书
>其中只有.crt和.key文件是我们需要使用的

### 安装OpenVPN
安装vpn的方法有2种,一种是使用yum安装,另外一种是编译安装。这两个我们选择一个就可以
编译安装
```bash
#安装依赖包
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
yum makecache
yum install -y lzo lzo-devel openssl openssl-devel pam pam-devel net-tools git lz4-devel


#下载openVPN软件包
wget -P /data/tools http://down.i4t.com/openvpn-2.4.7.tar.gz
cd /data/tools

#安装openVPN
tar zxf openvpn-2.4.7.tar.gz
cd openvpn-2.4.7
./configure --prefix=/usr/local/openvpn-2.4.7
make
make install

# 创建软连接
ln -s /usr/local/openvpn-2.4.7 /usr/local/openvpn
```
yum安装
```bash
curl -o /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
yum clean all && yum makecache
yum install -y openvpn
```
### 配置OpenVPN服务端
我们需要创建openVPN文件目录和证书目录
```bash
# openVPN配置文件目录,yum安装默认存在
mkdir /etc/openvpn

#openvpn证书目录
mkdir /etc/openvpn/keys
```
生成tls-auth key并将其拷贝到证书目录中（防DDos攻击、UDP淹没等恶意攻击）
```bash
# 编译安装执行此句
/usr/local/openvpn/sbin/openvpn --genkey --secret ta.key

# yum安装执行此句
openvpn --genkey --secret ta.key

#将本地的ta.key移动到openVPN证书目录
mv ./ta.key /etc/openvpn/keys/
```
将我们上面生成的CA证书和服务端证书拷贝到证书目录中
```bash
cp /usr/local/easy-rsa-old-master/easy-rsa/2.0/keys/{server.crt,server.key,ca.crt,dh2048.pem} /etc/openvpn/keys/

[root@localhost 2.0]# ll /etc/openvpn/keys/
总用量 28
-rw-r--r-- 1 root root 2431 7月  31 15:21 ca.crt
-rw-r--r-- 1 root root  424 7月  31 15:21 dh2048.pem
-rw-r--r-- 1 root root 8224 7月  31 15:21 server.crt
-rw------- 1 root root 3272 7月  31 15:21 server.key
-rw------- 1 root root  636 7月  31 15:21 ta.key
[root@localhost 2.0]#
```
拷贝OpenVPN配置文件
```bash
# 编译安装
cp /data/tools/openvpn-2.4.7/sample/sample-config-files/server.conf /etc/openvpn/

# yum安装
cp /usr/share/doc/openvpn-2.4.9/sample/sample-config-files/server.conf /etc/openvpn/
```
接下来我们来配置服务端的配置文件
```bash
[root@localhost 2.0]# cat /etc/openvpn/server.conf  |grep -v ^#  |grep -v ^\; |grep  -v  ^$
port 1194      #openVPN端口
proto udp      # udp连接  也可以改成tcp  client改成对应的连接方式即可
dev tun        # 生成tun0虚拟网卡
ca /etc/openvpn/keys/ca.crt       #相关证书配置路径
cert /etc/openvpn/keys/server.crt
key /etc/openvpn/keys/server.key  # This file should be kept secret
dh /etc/openvpn/keys/dh2048.pem
server 10.8.0.0 255.255.255.0     #默认虚拟局域网网段,不要和实际的局域网冲突就可以
ifconfig-pool-persist ipp.txt
keepalive 10 120
tls-auth /etc/openvpn/keys/ta.key 0 # This file is secret
cipher AES-256-CBC
persist-key
persist-tun
status openvpn-status.log
verb 3
explicit-exit-notify 1
```
### 开启内核路由转发功能
```bash
echo "net.ipv4.ip_forward = 1" >>/etc/sysctl.conf
sysctl -p
```
### 启动openvpn服务
```bash
nohup  /usr/sbin/openvpn   --daemon  --config /etc/openvpn/server.conf   &  

[root@localhost 2.0]# netstat  -atplnu  |grep  1194
udp        0      0 0.0.0.0:1194            0.0.0.0:*                           4105/openvpn        
[root@localhost 2.0]# 
```
设置开机启动
```bash
echo " /usr/sbin/openvpn   --daemon  --config /etc/openvpn/server.conf > /dev/null 2>&1 &" >>  /etc/rc.local
```
### 添加用户
以后我们如果想添加用户只需要到cd /usr/local/easy-rsa-old-master/easy-rsa/2.0目录下执行./build-key 用户名,在将keys目录下生成的用户名.crt和key导出,修改一下client.ovpn的用户key名称即可

## 客户端连接测试
```bash
# yum安装
cp /usr/share/doc/openvpn-2.4.9/sample/sample-config-files/client.conf    /root/

#修改如下,并将client.conf修改为client.ovpn
[root@localhost 2.0]# cat /root/client.conf
client
dev tun
proto udp       # 可server端对应
remote 192.168.0.10 1194    #openvpn服务器的外网IP和端口(可以写多个做到高可用)
resolv-retry infinite
nobind
persist-key
persist-tun
ca ca.crt
cert client1.crt         #用户的证书
key client1.key

tls-auth ta.key 1
cipher AES-256-CBC
comp-lzo
verb 3


#比较重点的就是修改remote 地址,这里的地址为server
cert key,我们这里使用用户的证书,所以证书也应当修改为client1.crt和client1.key
tls-auth 因为使用加密协议,所以ta.key也需要下载下来
```
修改后缀并导出
```bash
[root@localhost ~]# mv client.conf client.ovpn
[root@localhost ~]# sz client.ovpn 
#同时还需要导出几个证书
[root@localhost ~]#  sz   /etc/openvpn/keys/ca.crt 
[root@localhost ~]#  sz   /etc/openvpn/keys/ta.key 
[root@localhost ~]#  sz   /usr/local/easy-rsa-old-master/easy-rsa/2.0/keys/client1.crt 
[root@localhost ~]#  sz   /usr/local/easy-rsa-old-master/easy-rsa/2.0/keys/client1.key 
```


### Windows
Windows客户端下载
http://down.i4t.com/openvpn-install-2.4.7-I606-Win10.exe

http://down.i4t.com/openvpn-install-2.4.7-I606-Win7.exe

安装完成后点击桌面的logo,右击属性 
![](../2.png)
点击打开文件位置 
![](../3.png)
点击上方OpenVPN进入上级目录
![](../4.png)
选择config目录 
![](../5.png)
将config目录下文件全部删除,然后将我们导出的5个证书复制过去 
![](../6.png)
>其中client1.*为client1用户的证书
现在我们进行启动openvpn客户端,进行连接 
![](../7.png)
![](../8.png)
![](../9.png)
