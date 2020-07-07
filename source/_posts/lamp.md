---
title: lamp
tags:
  - linux
categories: 运维
date: 2020-05-13 23:00:44
---
<!-- 文章头部设置 -->

>&  表示任务在后台执行，如要在后台运行redis-server,则有  redis-server &
>&& 表示前一条命令执行成功时，才执行后一条命令 ，如 echo '1' && echo '2'
>| 表示管道，上一条命令的输出，作为下一条命令参数，如 echo 'yes' | wc -l
>|| 表示上一条命令执行失败后，才执行下一条命令，如 cat nofile || echo "fail"

# 安装apache

## 下载和安装依赖
```bash
yum -y install autoconf libtool gcc expat expat-devel make zlib-devel gcc-c++ openssl-devel pcre-devel openssl

# 进入 https://mirror.bit.edu.cn/apache//apr/ 找到最新的 apr 和 apr-util 包即可
wget https://mirror.bit.edu.cn/apache//apr/apr-1.7.0.tar.gz
wget https://mirror.bit.edu.cn/apache/httpd/httpd-2.4.43.tar.gz
wget https://mirror.bit.edu.cn/apache//apr/apr-util-1.6.1.tar.gz
```

## 编译安装apr

```bash
# 编辑 configure文件，查找 $RM "$cfgfile" 这个地方，用#注释掉
31880行 #    $RM "$cfgfile"

# 在configure里面 RM='$RM  -f' 这里的$RM后面一定有一个空格。 如果后面没有空格，直接连接减号，就依然会报错。把 RM='$RM' 改为 RM='$RM -f'
31279行     RM='$RM -f'
# 更改上面两行，否则./configure会报错：rm: cannot remove `libtoolT': No such file or directory
./configure --prefix=/home/lamp/apr
make && make install
```

## 编译安装apr-util

```bash
# 指明apr的安装位置--with-apr=/home/lamp/apr
./configure --prefix=/home/lamp/apr-util --with-apr=/home/lamp/apr
make && make install
```

## 编译安装apache
```bash
./configure --prefix=/home/lamp/apache2 --with-apr=/home/lamp/apr --with-apr-util=/home/lamp/apr-util
make && make install
```

# 启动apache

```bash
cd /home/lamp/apache2
# 修改端口为800
./bin/httpd -k start
curl localhost:800
# 显示<html><body><h1>It works!</h1></body></html>
```

## 配置基于域名访问不同资源目录

```bash
# 编辑httpd.conf，在文件最后加入以下几行：

</IfModule>
<VirtualHost *:800>
    DocumentRoot "/home/lamp/apache2/htdocs/"
    ServerName www.example.com
    # 访问www.example.com会访问/home/lamp/apache2/htdocs/目录下的资源

</VirtualHost>

<VirtualHost *:800>
    DocumentRoot "/home/lamp/apache2/htdocs/org/"
    ServerName www.example.org
    # 访问www.example.org会访问/home/lamp/apache2/htdocs/org/目录下的资源

</VirtualHost>

```

### 验证

```bash
# 配置hosts文件！！！

cd /home/lamp/apache2/
mkdir htdocs/org/

# 编辑htdocs/index.html填入www.example.com
cat > htdocs/index.html << EOF
www.example.com
EOF

# 编辑htdocs/org/index.html填入www.example.org
cat > htdocs/org/index.html << EOF
www.example.org
EOF

# 重启 访问
./bin/httpd -k restart

[root@www apache2]# curl www.example.org:800
www.example.org
[root@www apache2]# curl www.example.com:800
www.example.com

```

## 配置基于端口访问不同资源目录

```bash
# 在上文的基础上增加www.example.com:8000端口，直接在配置文件最下面添加以下内容
<VirtualHost *:8000>
    DocumentRoot "/home/lamp/apache2/htdocs/8000/"
    ServerName www.example.com
    # 访问www.example.com:8000会访问/htdocs/8000/目录下的资源

</VirtualHost>
# 在监听端口下面增加新的监听端口
Listen 800
Listen 8000
```

### 验证

```bash
mkdir htdocs/8000

cat > htdocs/8000/index.html << EOF
www.example.com:8000
EOF

# 检查
./bin/httpd -t   # 显示Syntax OK即可

./bin/httpd -k restart

[root@www apache2]# curl www.example.com:8000
www.example.com:8000
[root@www apache2]# curl www.example.com:800
www.example.com
[root@www apache2]# curl www.example.org:800
www.example.org
```

## 配置基于虚拟主机访问不同资源目录

&emsp;&emsp;注释掉上面的3个配置
```bash
# 增加新的配置
# 注意！IP地址是主机自带的IP地址，并非虚拟不存在的。改完配置要修改hosts解析！！！
<VirtualHost 192.168.1.100>
    DocumentRoot "/home/lamp/apache2/htdocs/100/"
    ServerName www.example.com
    # 访问www.example.com:800会访问htdocs/100/目录下的资源

</VirtualHost>

<VirtualHost 192.168.1.200>
    DocumentRoot "/home/lamp/apache2/htdocs/200/"
    ServerName www.example.org
    # 访问www.example.org:800会访问htdocs/200/目录下的资源

</VirtualHost>

```
### 验证

```bash
./bin/httpd -k restart

mkdir htdocs/{1,2}00
cat > htdocs/100/index.html << EOF
www.example.com  100
EOF

cat > htdocs/200/index.html << EOF
www.example.org  200
EOF
./bin/httpd -k restart

[root@www apache2]# curl www.example.com:800
www.example.com  100
[root@www apache2]# curl www.example.org:800
www.example.org  200
```
## 配置基于简单的用户密码验证访问

&emsp;&emsp;注释掉上面的配置
```bash
<Directory /usr/local/apache2/htdocs/wang>
        AuthName "wang Auth"
        AuthType basic
        AuthUserFile /usr/local/apache2/.htpasswd
        Require user wang
</Directory>
# AuthName "wang Auth"   该字符串显示在网页访问时输入用户密码的对话框之上，实际测试并未显示
# AuthType basic         定义验证模块类型
# AuthUserFile /file     密码文件的存放地址
# Require user wang      设置哪些用户生效

# 下面这些注释也能实现用户密码访问，建议留存以便解决一些未知的bug，如果你是yum安装的httpd，你可以直接修改conf.d/userdir.conf文件，直接在最下面增加上述配置即可。
<IfModule mod_userdir.c>
    UserDir public_html
</IfModule>

<Directory /home/*/public_html>
    AllowOverride FileInfo AuthConfig Limit
    Options MultiViews Indexes SymLinksIfOwnerMatch IncludesNoExec
    <Limit GET POST OPTIONS>
        Order allow,deny
        Allow from all
    </Limit>
    <LimitExcept GET POST OPTIONS>
        Order deny,allow
        Deny from all
    </LimitExcept>
</Directory>

```

### 验证

```bash
./bin/httpd -k restart
# 创建用户：
useradd wang
cd /usr/local/apache2
mkdir htdocs/wang
cat > htdocs/wang/index.html << EOF
wang auth
EOF

# 生成密码文件，修改密码再次执行此命令即可
./bin/htpasswd -c -m /home/lamp/apache2/.htpasswd wang
# 输入密码a123456

cat .htpasswd
# 显示 wang:$apr1$eL9wB7zB$F6bE1abbu1vGDVrW4Ji9V1
```
|选项 |注释|
|:---|:-|
|-c  |自动创建文件，仅应该在文件不存在时使用(初建时使用-c,再次创建不取消该选项则会覆盖之前内容)|
|-m  |md5格式加密|
|-s  |sha格式加密|
|-D  |删除指定用户|

```bash
# 访问
curl www.example.com:800/wang
# 报错401
# 下载elinks
wget http://rpmfind.net/linux/centos/8.1.1911/PowerTools/x86_64/os/Packages/elinks-0.12-0.58.pre6.el8.x86_64.rpm
rpm -ivh elinks-0.12-0.58.pre6.el8.x86_64.rpm
# elinks访问
elinks http://www.example.com:800/wang
# 输入用户名密码 >> 点击OK >> 点击here （①可以鼠标操作，②可以通过方向键移动光标，enter确认）
```
![](/medias/lamp/0.jpg)
![](/medias/lamp/1.jpg)
![](/medias/lamp/2.jpg)

### 扩展基于组用户密码访问

&emsp;&emsp;上面的**配置不变**，增加两行
```bash
<Directory /usr/local/apache2/htdocs/wang>
        AuthName "wang Auth"
        AuthType basic
        AuthUserFile /usr/local/apache2/.htpasswd
        Require user wang
        AuthGroupFile /usr/local/apache2/groupfile   # 组文件
        Require group wang   # 允许的组
</Directory>
# 通过上面的配置文件可知，允许wang组里面的用户访问，允许用户wang访问
```

#### 验证

```bash
./bin/httpd -k restart
cat > groupfile << EOF
wang:test
test0:test0
EOF
# :前面是组名，后面是用户名；组名等于http.conf中的Require group wang规定的组名
# 增加用户test，test0
./bin/htpasswd -m /home/lamp/apache2/.htpasswd test
./bin/htpasswd -m /home/lamp/apache2/.htpasswd test0
# 由配置文件可知，允许test，和wang访问，不允许test0访问

# 分别输入wang，test，test0用户密码验证即可
elinks http://www.example.com:800/wang
```

# 一般遇到的问题

1. httpd.conf配置文件中，填写的路径不对
2. 多使用./bin/httpd -t检查，可以避免很多的粗心错误
3. 修改完配置文件一定记得重启，./bin/httpd -k restart
4. 端口，资源目录，目录权限等，一定要再三验证
5. 你遇到的其它问题欢迎留言~

# 编译MySQL

&emsp;&emsp;安装依赖
```bash
# MySQL源码地址：https://cdn.mysql.com//Downloads/MySQL-8.0/mysql-8.0.20.tar.gz
yum install -y ncurses-devel libtirpc-devel cmake

# 缺少：libtirpc-devel
# 报错：Could not find rpc/rpc.h in /usr/include or /usr/include/tirpc

# 缺少：ncurses-devel
# 报错：Curses library not found. Please install appropriate package

# 缺少：rpcsvc
# 报错：Could not find rpcgen
# 编译安装
wget https://github.com/thkukuk/rpcsvc-proto/releases/download/v1.4.1/rpcsvc-proto-1.4.1.tar.xz
tar xf rpcsvc-proto-1.4.1.tar.xz
cd rpcsvc-proto-1.4.1/
./configure && make && make install
```

```bash
mkdir /home/lamp/mysql/data -p

cmake . \
-DCMAKE_INSTALL_PREFIX=/home/lamp/mysql \
-DMYSQL_DATADIR=/home/lamp/mysql/data \
-DMYSQL_UNIX_ADDR=/home/lamp/mysql/mysql.sock \
-DWITH_INNODBBASE_STORAGE_ENGINE=1 \
-DENABLE_LOCAL_INFILE=1 \
-DEXTRA_CHARSETS=all \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DMYSQL_USER=mysql \
-DWITH_DEBUG=0 \
-DFORCE_INSOURCE_BUILD=1 \
-DDOWNLOAD_BOOST=1 -DWITH_BOOST=/home/lamp/boost \
-DWITH_EMBEDED_SERVER=0
# boost下载超时的话，记录下载地址：https://dl.bintray.com/boostorg/release/1.70.0/source/boost_1_70_0.tar.gz
# 使用迅雷下载，大约不到1min就下载好了
# 移动到/home/lamp/boost目录下面
```
编译时间较长长长长长长长长长长长长长长长长😡

## 一般遇到的问题

1. 依赖问题
2. 网速太慢
3. 编译的时候内存不足
4. 目录权限

不等待直接编译PHP
# 编译PHP

## 安装依赖
```bash
yum install libxml2-devel bzip2-devel net-snmp-devel curl-devel libpng-devel freetype-devel libjpeg-devel -y

wget http://ftp.gnu.org/gnu/libiconv/libiconv-1.16.tar.gz
# wget http://ftp.gnu.org/gnu/libiconv/libiconv-1.14.tar.gz
./configure --prefix=/usr/local --with-apr=/home/lamp/apr
make && make install

wget https://sourceforge.net/projects/mcrypt/files/Libmcrypt/2.5.8/libmcrypt-2.5.8.tar.gz
./configure && make && make install && /sbin/ldconfig
cd libltdl/
./configure --enable-ltdl-install && make && make install

wget https://jaist.dl.sourceforge.net/project/mhash/mhash/0.9.9.9/mhash-0.9.9.9.tar.gz
./configure && make && make install
ln -s /usr/local/lib/* /usr/lib/
ln -s /usr/local/bin/libmcrypt-config /usr/bin/

wget https://jaist.dl.sourceforge.net/project/mcrypt/MCrypt/2.6.8/mcrypt-2.6.8.tar.gz
# 解决报错：configure: error: *** libmcrypt was not found
ln -s /usr/local/bin/libmcrypt_config /usr/bin/libmcrypt_config
export LD_LIBRARY_PATH=/usr/local/lib: LD_LIBRARY_PATH
./configure && make && make install
```
## 安装PHP

```bash
wget https://www.php.net/distributions/php-7.4.5.tar.gz
```

```bash

```

```bash

```

```bash

```

