---
title: Nginx网站服务
tags:
  - nginx
  - linux
categories: 运维
date: 2020-08-11 15:20:01
---
## Nginx简介
Nginx是一个高性能的 HTTP 和 反向代理 服务器,也是一个 IMAP/POP3/SMTP 服务器
Nginx是由伊戈尔·赛索耶夫为俄罗斯访问量第二的Rambler·ru站点（俄文：Рамблер）开发的,第一个公开版本0.1.0发布于2004年10月4日其将源代码以类BSD许可证的形式发布,因它的稳定性、丰富的功能集、示例配置文件和低系统资源的消耗而闻名,2011年6月1日,nginx1.0.4发布
Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器,在BSD-like 协议下发行。其特点是占有内存少,并发能力强,事实上nginx的并发能力确实在同类型的网页服务器中表现较好。Nginx能够支持高达5万个并发连接的响应。

## Nginx的优点
```bash
低资源：占用内存小,可以实现高并发访问、处理响应快
高并发：可抗高并发,Nginx支持的并发连接上限取决于你的内存
作用：可以实现http服务器、虚拟主机、反向代理、负载均衡
配置简单：Nginx配置简单
安全: 可以不暴露真实服务器IP地址
```

## Nginx负载均衡和lvs对比
lvs的优势
```bash
1、抗负载能力强,因为lvs工作方式的逻辑是非常之简单,而且工作在网络4层仅做请求分发之用,没有流量,所以在有效上基本不需要太过考虑,在我手里的lvs,仅仅出过一次问题：在并发最高的一小段时间内均衡器出现丢包现象,据分析为网络问题,即网卡或linux2.4内核的承载能力已到上线,内存和cpu方面基本无消耗。
2、配置性低,这通常是一大劣势,但同时也是一大优势,因为没有太多可配置的选项,所以除了增减服务器,并不需要经常在触碰它,大大减少了人为出错的几率。
3、工作稳定,因为其本身抗负载能力很强,所以稳定性高也是顺理成章,另外各种lvs都有完整的双机热备方案,所以一点不用担心均衡器本身会出现什么问题,节点出现故障的话,lvs会自动判别,所以系统整体是非常稳定的
4、无流量,上面已经有所提及了,lvs仅仅分发请求,而流量并不从它本身出去,所以可以利用它这点来做一些线路分流之用。没有流量同时也是保住了均衡器的IO性能不会受到大流量的影响
5、基本上能支持所有应用,因为lvs工作在4层,所以它可以对几乎所有应用做负载均衡,包括http、数据库、聊天室等等。
另：lvs也不是完全能判别节点故障的,譬如在wlc分配方式下,集群里有一个节点没有配置VIP,会使整个集群不能使用,这时使用wrr分配方式则会丢掉一台机,目前这个问题还在进一步测试中,所以,用lvs也得多多当心为妙
```
nginx对比lvs的优势
```bash
1、nginx工作在网络的7层,所以它可以针对http应用 本身来做分流策略,比如针对域名、目录结构等,相比之下lvs并不具备这样的功能,所以nginx单凭这点可利用的场合就远多于lvs了。但nginx有用的这些功能使其可调整度要高于lvs,所以经常要去触碰触碰,由lvs的第2条优点看,触碰多了。人为出问题的记录也就会大。
2、nginx对网络的依赖较小,理论上只要ping得通,网页访问正常,nginx就能连得通,nginx同时还能区分内外网,如果是同时拥有内外网的 节点,就相当于单机拥有了备份线路；lvs就比较依赖于网络环境,目前来看服务器在同一网段内并且lvs使用direct方式分流,效果较能得到保证。另 外注意,lvs需要向托管商至少申请多一个ip来做Visual IP,貌似是不能用本身的IP来做VIP的。要做好LVS管理员,确实得跟进学习很多有关网络通信方面的知识,就不再是一个HTTP那么简单了。
3、nginx安装和配置比较简单,测试起来也很方便,因为它基本能把错误用日志打印出来。lvs的安装和配置、测试就要花比较长的时间了,因为同上所述,lvs对网络依赖比较大,很多时候不能配置成功都是因为网络问题而不是配置问题,出了问题要解决也相应的会麻烦得多。
4、nginx也同样能承受很高负载且稳定,但负载度和稳定度差lvs还有几个等级：nginx处理所有流量所以受限于机器IO和配置；本身的bug也还是难以避免的；nginx没有现成的双机热备方案,所以跑在单机上还是风险较大,单机上的事情全都很难说。
5、nginx可以检测到服务器内部的故障,比如根据服务器处理网页返回的状态码、超时等等,并且会把返回错误的请求重新提交到另一个节点。目前lvs中 ldirectd也能支持针对服务器内部的情况来监控,但lvs的原理使其不能重发请求。重发请求这点,譬如用户正在上传一个文件,而处理该上传的节点刚 好在上传过程中出现故障,nginx会把上传切到另一台服务器重新处理,而lvs就直接断掉了,如果是上传一个很大的文件或者很重要的文件的话,用户可能 会因此而恼火。
6、nginx对请求的异步处理可以帮助节点服务器减轻负载,假如使用apache直接对外服务,那么出现很多的窄带链接时apache服务器将会占用大 量内存而不能释放,使用多一个nginx做apache代理的话,这些窄带链接会被nginx挡住,apache上就不会堆积过多的请求,这样就减少了相 当多的内存占用。这点使用squid也有相同的作用,即使squid本身配置为不缓存,对apache还是有很大帮助的。lvs没有这些功能,也就无法能 比较。
7、nginx能支持http和email（email的功能估计比较少人用）,lvs所支持的应用在这点上会比nginx更多。
```
## 特点对比

LVS特点：
```bash
1.抗负载能力强,使用IP负载均衡技术,只做分发,所以LVS本身并没有多少流量产生；
2.稳定性、可靠性好,自身有完美的热备方案；（如：LVS+Keepalived）
3.应用范围比较广,可以对所有应用做负载均衡；
4.不支持正则处理,不能做动静分离。

常用四种算法：

1.rr：轮询,轮流分配到后端服务器；
2.wrr：权重轮询,根据后端服务器负载情况来分配；
3.lc：最小连接,分配已建立连接最少的服务器上；
4.wlc：权重最小连接,根据后端服务器处理能力来分配。
可以采用ipvsadm –p（persistence）来保持session,默认是300/s 
```
Nginx特点：
```bash
1.工作在7层,可以对做正则规则处理；（如：针对域名、目录进行分流）
2.配置简单,能ping通就能进行负载功能,可以通过端口检测后端服务器状态,不支持url检测；
3.抗高并发,采用epoll网络模型处理客户请求；
4.只支持HTTP和EMail,应用范围比较少；
5.nginx主要是HTTP和反向代理服务器,低系统资源消耗。

常用四种算法：

1.RR：（默认）轮询,轮流分配到后端服务器；
2.weight：根据后端服务器性能分配；
3.ip_hash：每个请求按访问ip的hash结果进行分配,并发小时合适,解决session问题；
4.fair：（扩展策略）,默认不被编译nginx内核,根据后端服务器响应时间判断负载情况,选择最轻的进行处理。 
```
为什么用Nginx而不用LVS?
```bash
1、高并发连接： 官方测试能够支撑5万并发连接,在实际生产环境中跑到 2~3 万并发连接数。
2、内存消耗少： 在 3 万并发连接下,开启的 10 个 Nginx 进程才消耗 150M 内存（ 15M*10=150M ）。
3、配置文件非常简单： 风格跟程序一样通俗易懂。
4、成本低廉： Nginx 为开源软件,可以免费使用。而购买 F5 BIG-IP、NetScaler 等硬件负载均衡交换机则需要十多万至几十万人民币。
  使用 Nginx 做七层负载均衡的理由?
5、支持 Rewrite 重写规则： 能够根据域名、 URL 的不同,将 HTTP 请求分到不同的后端服务器群组。
6、内置的健康检查功能： 如果 Nginx Proxy 后端的某台 Web 服务器宕机了,不会影响前端访问。
7、节省带宽： 支持 GZIP 压缩,可以添加浏览器本地缓存的 Header 头。
```
## Nginx安装部署
nginx官网下载地址：http://nginx.org/en/download.html
```bash
# 安装依赖工具包
yum install  gcc*   c++*    pcre  pcre-devel  openssl  openssl-devel  -y
# 下载源码包
wget  http://nginx.org/download/nginx-1.18.0.tar.gz
# 解压编译安装
tar xf  nginx-1.18.0.tar.gz  -C /usr/src/
cd  /usr/src/nginx-1.18.0/
./configure    --prefix=/usr/local/nginx
make   && make  install  
/usr/local/nginx/sbin
# 启动nginx
./nginx 
```

### 配置文件
```bash
user nginx;
worker_processes auto; （默认为自动,可以自己设置,一般不大于cpu核数）
error_log /var/log/nginx/error.log; （错误日志路径）
pid /run/nginx.pid; （pid文件路径）


# Load dynamic modules. See /usr/share/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;


events {
    accept_mutex on; （设置网路连接序列化,防止惊群现象发生,默认为on）
    multi_accept on; （设置一个进程是否同时接受多个网络连接,默认为off）
    worker_connections 1024; （一个进程的最大连接数）
}


http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
      # 设置日志格式
    
    access_log  /var/log/nginx/access.log  main;


    sendfile            on;
    # tcp_nopush          on; （这里注释掉）
    tcp_nodelay        on;
    keepalive_timeout  65; （连接超时时间）
    types_hash_max_size 2048;
    gzip on; （开启压缩）
    include            /etc/nginx/mime.types;
    default_type        application/octet-stream;


    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;


# 这里设置负载均衡,负载均衡有多中策略,nginx自带的有轮询,权重,ip-hash,响应时间等粗略。
# 默认为平分http负载,为轮询的方式。
# 权重则是按照权重来分发请求,权重高的负载大
# ip-hash,根据ip来分配,保持同一个ip分在同一台服务器上。
# 响应时间,根据服务器都nginx 的响应时间,优先分发给响应速度快的服务器。
# 集中策略可以适当组合
    upstream tomcat { （tomcat为自定义的负载均衡规则名）
        ip_hash; （ip_hash则为ip-hash方法）

　　　　　　server 192.168.2.78:80 weight=3 fail_timeout=20s;
　　　　　　server 192.168.2.82:80 weight=4 fail_timeout=20s;

 

## 可以定义多组规则
}

 


    server {
        listen      80 default_server; （默认监听80端口）
        listen      localhost; （监听的服务器）
        server_name  _;
        root        /usr/share/nginx/html;


        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;


    location / { （ /  表示所有请求,可以自定义来针对不同的域名设定不同负载规则 和服务）
          proxy_pass    http://tomcat; （反向代理,填上你自己的负载均衡规则名）
          proxy_redirect off; （下面一些设置可以直接复制过去,不要的话,有可能导致一些 没法认证等的问题）
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_connect_timeout 90; （下面这几个都只是一些超时设置,可不要）
          proxy_send_timeout 90;
          proxy_read_timeout 90;
        }
  # location ~\.(gif|jpg|png)$ { （比如,以正则表达式写） 
  #  root /home/root/images;
  #  }


        error_page 404 /404.html;
            location = /40x.html {
        }


        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }


# Settings for a TLS enabled server.
#
#    server {
#        listen      443 ssl http2 default_server;
#        listen      [::]:443 ssl http2 default_server;
#        server_name  _;
#        root        /usr/share/nginx/html;
#
#        ssl_certificate "/etc/pki/nginx/server.crt";
#        ssl_certificate_key "/etc/pki/nginx/private/server.key";
#        ssl_session_cache shared:SSL:1m;
#        ssl_session_timeout  10m;
#        ssl_ciphers HIGH:!aNULL:!MD5;
#        ssl_prefer_server_ciphers on;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        location / {
#        }
#
#        error_page 404 /404.html;
#            location = /40x.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;
#            location = /50x.html {
#        }
#    }
}
```
```bash
#运行用户
user www-data;    
#启动进程,通常设置成和cpu的数量相等
worker_processes  1;
 #全局错误日志及PID文件
error_log  /var/log/nginx/error.log;
pid        /var/run/nginx.pid;
 #工作模式及连接数上限
events {
    use   epoll;             #epoll是多路复用IO(I/O Multiplexing)中的一种方式,但是仅用于linux2.6以上内核,可以大大提高nginx的性能
    worker_connections  1024;#单个后台worker process进程的最大并发链接数
    # multi_accept on; 
}
 #设定http服务器,利用它的反向代理功能提供负载均衡支持
http {
     #设定mime类型,类型由mime.type文件定义
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    #设定日志格式
    access_log    /var/log/nginx/access.log;
   #sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件,对于普通应用,
    #必须设为 on,如果用来进行下载等应用磁盘IO重负载应用,可设置为 off,以平衡磁盘与网络I/O处理速度,降低系统的uptime.
    sendfile        on;
    #tcp_nopush     on;
    #连接超时时间
    #keepalive_timeout  0;
    keepalive_timeout  65;
    tcp_nodelay        on;
     #开启gzip压缩
    gzip  on;
    gzip_disable "MSIE [1-6]\.(?!.*SV1)";
    #设定请求缓冲
    client_header_buffer_size    1k;
    large_client_header_buffers  4 4k;
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
    #设定负载均衡的服务器列表
     upstream mysvr {
    #weigth参数表示权值,权值越高被分配到的几率越大
    #本机上的Squid开启3128端口
    server 192.168.8.1:3128 weight=5;
    server 192.168.8.2:80  weight=1;
    server 192.168.8.3:80  weight=6;
    }
   server {
    #侦听80端口
        listen       80;
        #定义使用www.xx.com访问
        server_name  www.xx.com;
        #设定本虚拟主机的访问日志
        access_log  logs/www.xx.com.access.log  main;
    #默认请求
    location / {
          root   /root;      #定义服务器的默认网站根目录位置
          index index.php index.html index.htm;   #定义首页索引文件的名称
          fastcgi_pass  www.xx.com;
          fastcgi_param  SCRIPT_FILENAME  $document_root/$fastcgi_script_name; 脚本文件请求的路径
          include /etc/nginx/fastcgi_params;
        }
    # 定义错误提示页面
    error_page   500 502 503 504 /50x.html;  
        location = /50x.html {
        root   /root;
    }
    #静态文件,nginx自己处理
    location ~ ^/(images|javascript|js|css|flash|media|static)/ {
        root /var/www/virtual/htdocs;
        #过期30天,静态文件不怎么更新,过期可以设大一点,如果频繁更新,则可以设置得小一点。
        expires 30d;
    }
    #PHP 脚本请求全部转发到 FastCGI处理. 使用FastCGI默认配置.
    location ~ \.php$ {
        root /root;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME /home/www/www$fastcgi_script_name;
        include fastcgi_params;
    }
    #设定查看Nginx状态的地址
    location /NginxStatus {
        stub_status            on;
        access_log              on;
        auth_basic              "NginxStatus";
        auth_basic_user_file  conf/htpasswd;
    }
    #禁止访问 .htxxx 文件
    location ~ /\.ht {
        deny all;
    }
     }
}
```

### 虚拟主机
虚拟主机是使用特殊的软硬件技术,把一台真实的物理服务器主机分割成多个逻辑存储单元。每个逻辑单元都没有物理实体,但是每一个逻辑单元都能像真实的物理主机一样在网络上工作,具有单独的IP地址（或共享的IP地址）、独立的域名以及完整的Internet服务器（支持WWW、FTP、E-mail等）功能。
虚拟主机的关键技术在于,即使在同一台硬件、同一个操作系统上,运行着为多个用户打开的不同的服务器程式,也互不干扰。而各个用户拥有自己的一部分系统资源（IP地址、文档存储空间、内存、CPU等）。各个虚拟主机之间完全独立,在外界看来,每一台虚拟主机和一台单独的主机的表现完全相同。所以这种被虚拟化的逻辑主机被形象地称为"虚拟主机"

### 基于端口的虚拟主机
修改nginx.conf (server 配置段需要添加在http配置段中)
```bash
    server {
        listen       80;
        server_name  localhost;

        location / {
            root   /webdate/test;
            index  index.html index.htm;
        }
    }
    server {
        listen       8090;
        server_name  localhost;
        location / {
            root   /webdata/test1;
            index  index.html index.htm;
        }
    }
```
检测配置文件是否正确
```bash
[root@localhost sbin]# ./nginx  -t            
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
```
重启nginx并且测试
```bash
[root@localhost sbin]# echo  "port is 80" > /webdata/test/index.html 
[root@localhost sbin]# echo  "port is 8090" > /webdata/test1/index.html 
[root@localhost sbin]# lsof -i:80
COMMAND   PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
nginx   46506   root    6u  IPv4 695380      0t0  TCP *:http (LISTEN)
nginx   46507 nobody    6u  IPv4 695380      0t0  TCP *:http (LISTEN)
[root@localhost sbin]# kill -9  46506 46507
[root@localhost sbin]# ./nginx 
[root@localhost sbin]# elinks  http://localhost:80   -dump  
   port is 80
[root@localhost sbin]# elinks  http://localhost:8090   -dump  
   port is 8090
[root@localhost sbin]# 
```

### 基于IP的虚拟主机
首先保证服务器有两个ip地址,实验环境是虚拟机直接添加个网络适配器配置下ip就可以了
```bash
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.89  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::e9b6:bbdd:9d72:9e0a  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:cb:96:c9  txqueuelen 1000  (Ethernet)
        RX packets 1111  bytes 138751 (135.4 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 247  bytes 41089 (40.1 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens37: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.110  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::326:4059:a69b:80c  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:cb:96:d3  txqueuelen 1000  (Ethernet)
        RX packets 872  bytes 106472 (103.9 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 12  bytes 1176 (1.1 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 13  bytes 965 (965.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 13  bytes 965 (965.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
配置nginx.conf
```bash
vim ../conf/nginx.conf

    server {
        listen       192.168.1.89:80;
        server_name  localhost;

        location / {
            root   /webdata/test;
            index  index.html index.htm;
        }
    }
    server {
        listen       192.168.1.110:80;
        server_name  localhost;
        location / {
            root   /webdata/test1;
            index  index.html index.htm;
        }
    }
```
重启nginx并且测试
```bash
[root@localhost sbin]# echo   "192.168.1.89" > /webdata/test/index.html 
[root@localhost sbin]# echo "192.168.1.110" > /webdata/test1/index.html 
[root@localhost sbin]# lsof -i:80
COMMAND   PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
nginx   46506   root    6u  IPv4 695380      0t0  TCP *:http (LISTEN)
nginx   46507 nobody    6u  IPv4 695380      0t0  TCP *:http (LISTEN)
[root@localhost sbin]# kill -9  46506 46507
[root@localhost sbin]# ./nginx 
[root@localhost sbin]# elinks  http://192.168.1.89:80   -dump            
   192.168.1.89
[root@localhost sbin]# elinks  http://192.168.1.110:80   -dump   
   192.168.1.110
[root@localhost sbin]# 
```
### 基于域名的虚拟主机
修改/etc/hosts文件
```bash
vi /etc/hosts
    192.168.1.89  www.xin.com
    192.168.1.89  www.long.com
```
修改配置文件
```bash
    server {
        listen       80;
        server_name  www.xin.com;

        location / {
            root   /webdata/test;
            index  index.html index.htm;
        }
    }
    server {
        listen       80;
        server_name  www.long.com;
        location / {
            root   /webdata/test1;
            index  index.html index.htm;
        }
    }

```
重启nginx并且测试
```bash
[root@localhost sbin]# echo   "www.xin.com" > /webdata/test/index.html 
[root@localhost sbin]# echo "www.long.com" > /webdata/test1/index.html 
[root@localhost sbin]# lsof -i:80
COMMAND   PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
nginx   46506   root    6u  IPv4 695380      0t0  TCP *:http (LISTEN)
nginx   46507 nobody    6u  IPv4 695380      0t0  TCP *:http (LISTEN)
[root@localhost sbin]# kill -9  46506 46507
[root@localhost sbin]# ./nginx 
[root@localhost sbin]# elinks  http://www.xin.com:80   -dump                 
   www.xin.com
[root@localhost sbin]# elinks  http://www.long.com:80   -dump   
   www.long.com
[root@localhost sbin]# 
```
## nginx认证配置
nginx basic auth指令
	语法:     auth_basic string | off;
	默认值:     auth_basic off;
	配置段:     http, server, location, limit_except
	默认表示不开启认证,后面如果跟上字符,这些字符会在弹窗中显示。
	语法:     auth_basic_user_file file;
	默认值:     —
	配置段:     http, server, location, limit_except
	用户密码文件,文件内容类似如下：
	ttlsauser1:password1
	ttlsauser2:password2:comment
生成密码文件
```bash
[root@bogon sbin]# htpasswd  -cm   /usr/local/nginx/.htpasswd   test
New password:        # 输入密码
Re-type new password:     # 确认密码
Adding password for user test
```
nginx认证配置实例
```bash
    server {
        listen       80;
        server_name  www.xin.com;

        location / {
            root   /webdata/test;    # 加密的目录
            index  index.html index.htm;
            auth_basic  "nginx basic http test for ttlsa.com";    # 描述文件
            auth_basic_user_file  /usr/local/nginx/.htpasswd;     # 密码文件
            autoindex  on;    # 是否开启
        }
    }
```
测试
```bash
[root@bogon sbin]# ./nginx  -t
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
```
访问网站
![](../1.png)
提示需要输入密码
![](../2.png)
备注：一定要注意auth_basic_user_file路径,否则会不厌其烦的出现403

## nginx 网站限速
配置项：limit_rate_after
   当一个客户端连接后,将以最快的速度下载多大文件,然后在以限制速度下载文件
   该指令是下载字节量的大小值,而不是时间值
   当一个客户端连接后,将以最快的速度下载3M,然后再以大约10k的速度下载

实例
```bash
server {
                listen 80;
                server_name www.domain.com;
                location / {
                        root /tmp/186;
                        index index.html;
                        limit_rate 10k;  # 下载速度
                        limit_rate_after 3m;
      }

```
![](../11.png)
![](../12.png)
![](../13.png)
![](../14.png)
![](../15.png)
![](../16.png)
![](../17.png)

配置项：limit_rate
    是指定向客户端传输数据的速度,单位是每秒传输的字节数
    该限制只针对一个连接的设定,如果同时两个连接数,那么速度是设置值的两倍
limit_rate_after
   当一个客户端连接后,将以最快的速度下载多大文件,然后在以限制速度下载文件
   该指令是下载字节量的大小值,而不是时间值
   当一个客户端连接后,将以最快的速度下载3M,然后再以大约10k的速度下载

## nginx 区域配置规则
### Location规则
```bash
语法规则： location [=|~|~*|^~] /url/ {… }
匹配顺序： ＝／ ／  ^~   ~*
符号含义
=
= 开头表示精确匹配
^~
^~开头表示uri以某个常规字符串开头,理解为匹配 url路径即可。nginx不对url做编码,因此请求为/static/20%/aa,可以被规则^~ /static/ /aa匹配到（注意是空格）
~
~ 开头表示区分大小写的正则匹配
~*
~* 开头表示不区分大小写的正则匹配
!~和!~*
!~和!~*分别为区分大小写不匹配及不区分大小写不匹配的正则
/
用户所使用的代理（一般为浏览器）
$http_x_forwarded_for
可以记录客户端IP,通过代理服务器来记录客户端的ip地址
$http_referer
可以记录用户是从哪个链接访问过来的
```
### 匹配规则示例：
```bash
location = / {  
    #规则A  
}  
location = /login {  
    #规则B  
}  
location ^~ /static/ {  
    #规则C  
}  
location ~ \.(gif|jpg|png|js|css)$ {  
    #规则D  
}  
location ~* \.png$ {  
    #规则E  
}  
location !~ \.xhtml$ {  
    #规则F  
}  
location !~* \.xhtml$ {  
    #规则G  
}  


那么产生的效果如下：
1. 访问根目录/,比如http://localhost/将匹配规则A
2. 访问 http://localhost/login 将匹配规则B,
3. 访问 http://localhost/static/a.html 将匹配规则C
4. 访问 http://localhost/a.gif,http://localhost/b.jpg 将匹配规则D和规则E,但是规则D顺序优先,规则E不起作用,而http://localhost/static/c.png则优先匹配到规则C
5. 访问 http://localhost/a.PNG 则匹配规则E,而不会匹配规则D,因为规则E不区分大小写。
6. 访问 http://localhost/a.xhtml 不会匹配规则F和规则G,http://localhost/a.XHTML不会匹配规则G,因为不区分大小写。规则F,规则G属于排除法,符合匹配规则但是不会匹配到,所以想想看实际应用中哪里会用到。
7. 访问 http://localhost/category/id/1111 则最终匹配到规则H,因为以上规则都不匹配,这个时候应该是nginx转发请求给后端应用服务器,比如FastCGI（php）,tomcat（jsp）,nginx作为方向代理服务器存在。
```
### 实际常用规则
```bash
#直接匹配网站根,通过域名访问网站首页比较频繁,使用这个会加速处理。
#这里是直接转发给后端应用服务器了,也可以是一个静态首页
# 第一个必选规则
[plain] view plain copy
location = / {  
   proxy_pass  http://tomcat:8080/index  
}  
```
```bash
# 第二个必选规则是处理静态文件请求,这是nginx作为http服务器的强项
# 有两种配置模式,目录匹配或后缀匹配,任选其一或搭配使用
[plain] view plain copy
    location ^~ /static/ {  
       # 请求/static/a.txt 将被映射到实际目录文件:/webroot/res/static/a.txt  
       root /webroot/res/;  
    }  
  
    location ~* \.(gif|jpg|jpeg|png|css|js|ico)${  
       root /webroot/res/;  
    } 
```
```bash
#第三个规则就是通用规则,用来转发动态请求到后端应用服务器
#非静态文件请求就默认是动态请求,自己根据实际把握
#毕竟目前的一些框架的流行,带.php,.jsp后缀的情况很少了

[plain] view plain copy
location / {  
   proxy_pass http://tomcat:8080/  
}  
```
### Location解析过程
```bash
先判断精准命中,如果命中,立即返回结果并结束解析过程。
判断普通命中,如果有多个命中,“记录”下来“最长”的命中结果（记录但不结束,最长的为准）。
继续判断正则表达式的解析结果,按配置里的正则表达式顺序为准,由上至下开始匹配,一旦匹配成功1个,立即返回结果,并结束解析过程。
普通命中顺序无所谓,是因为按命中的长短来确定。正则命中,顺序有所谓,因为是从前入往后命中的。
```

### rewrite语法
```bash
Nginx提供的全局变量或自己设置的变量,结合正则表达式和标志位实现url重写以及重定向。
rewrite只能放在server{},location{},if{}中,并且只能对域名后边的除去传递的参数外的字符串起作用。
Rewrite主要的功能就是实现URL的重写,Nginx的Rewrite规则采用Pcre,perl兼容正则表达式的语法规则匹配,如果需要Nginx的Rewrite功能,在编译Nginx之前,需要编译安装PCRE库。
通过Rewrite规则,可以实现规范的URL、根据变量来做URL转向及选择配置
```
### rewrite相关指令

|指令|默认值|适用范围|作用|
|:-:|:-:|:-:|:-:|
|break|none|if,server,location|完成当前规则集,不再处理rewrite指令,需要和last加以区分|
|if ( condition ) <br>{ ... } |none|server,location|用于检测一个条件是否符合,符合则执行大括号内的语句。不支持嵌套,不支持多个条件&&或||处理|
|return|none|server,if,location|用于结束规则的执行和返回状态码给客户端。状态码的值可以是：204,400,402\~406,408,410,411,413,416以及500\~504,另外非标准状态码444,表示以不发送任何的Header头来结束连接。|
|rewrite regex replacement flag||server,location,if|该指令根据表达式来重定向URI,或者修改字符串。指令根据配置文件中的顺序来执行。注意重写表达式只对相对路径有效。|
|uninitialized_variable_warn on\|off|on|http,server,location,if|该指令用于开启和关闭未初始化变量的警告信息,默认值为开启。|
|set  variable  value|none||该指令用于定义一个变量,并且给变量进行赋值。变量的值可以是文本、一个变量或者变量和文本的联合,文本需要用引号引起来。|


### rewrite全局变量

|变量|含义|
|:-:|:-:|
|\$args|这个变量等于请求行中的参数,同$query_string|
|\$content length|请求头中的Content-length字段|
|\$content_type|请求头中的Content-Type字段|
|\$document_root|当前请求在root指令中指定的值|
|\$host|请求主机头字段,否则为服务器名称|
|\$http_user_agent|客户端agent信息|
|\$http_cookie|客户端cookie信息|
|\$limit_rate|这个变量可以限制连接速率|
|\$request_method|客户端请求的动作,通常为GET或POST|
|\$remote_addr|客户端的IP地址|
|\$remote_port|客户端的端口|
|\$remote_user|已经经过Auth Basic Module验证的用户名|
|\$request_filename|当前请求的文件路径,由root或alias指令与URI请求生成|
|\$scheme|HTTP方法（如http,https）|
|\$server_protocol|请求使用的协议,通常是HTTP/1.0或HTTP/1.1|
|\$server_addr|服务器地址,在完成一次系统调用后可以确定这个值|
|\$server_name|服务器名称|
|\$server_port|请求到达服务器的端口号|
|\$request_uri|包含请求参数的原始URI,不包含主机名,如”/foo/bar.php?arg=baz”|
|\$uri|不带请求参数的当前URI,$uri不包含主机名,如”/foo/bar.html”|
|\$document_uri|与$uri相同|

### rewrite语法规则

|操作符|含义|
|:-:|:-:|
|= ,!=|比较的一个变量和字符串|
|~, ~*|与正则表达式匹配的变量,如果这个正则表达式中包含},;则整个表达式需要用"或'包围|
|-f,!-f|检查一个文件是否存在|
|-d, !-d|检查一个目录是否存在|
|-e,!-e|检查一个文件、目录、符号链接是否存在|
|-x, !-x|检查一个文件是否可执行|

### if指令
```bash
if  语法格式
     if 空格 (条件) {
        重写模式
     }

        # 限制浏览器访问
    if ($http_user_agent ~ Firefox) { 
      rewrite ^(.*)$ /firefox/$1 break; 
    }      

    if ($http_user_agent ~ MSIE) { 
        rewrite ^(.*)$ /msie/$1 break; 
    }      

    if ($http_user_agent ~ Chrome) { 
        rewrite ^(.*)$ /chrome/$1 break; 
    }
```
案例：
![](../31.png)
![](../32.png)
![](../33.png)
![](../34.png)
### return指令
```bash
    # 限制IP访问
    if  ($remote_addr = 192.168.197.142) {
       return 403;
    }
```
![](../21.png)

### rewrite指令
判断目录是否存在
服务器内部的rewrite和302跳转不一样.跳转的话URL都变了,变成重新http请求index.html,而内部rewrite,上下文没变
```bash
    if (!-e $document_root$fastcgi_script_name) {
        rewrite ^.*$ /index.html break;
    }
```

### set指令
set指令是设置变量用的,可以用来达到多条件判断时作标志用
判断IE并重写,且不用break;我们用set变量来达到目的
```bash
if ($http_user_agent ~* msie) {
        set $isie 1;
    }

    if ($fastcgi_script_name = ie.html) {
        set $isie 0;
    }

    if ($isie 1) {
        rewrite ^.*$ ie.html;
    }
```

### 常用例子
表示访问路径有a,b,c,d都跳转到//127.0.0.1:8080$Request_uri
```bash
         location ~^/(a|b|c|d){
            proxy_pass http://127.0.0.1:8080$Request_uri;
            client_max_body_size   10240k;
            client_body_buffer_size   128k;
        }
```


### 实现防盗链
实例：nginx服务端配置防盗链如下
```bash
location ~* \.(gif|jpg|png|jpeg)$ {
        valid_referers none blocked 192.168.0.23 192.168.0.123;
        if ($invalid_referer) {
        #rewrite ^/ http://ww4.sinaimg.cn/bmiddle/051bbed1gw1egjc4xl7srj20cm08aaa6.jpg;  
        return 403;

```
>注解：valid_referers可以理解为白名单。后面可以使用*通配符来配置白名单,例如：*.baidu.com都可以通过盗链访问服务器
>none 允许客户机直接访问服务器,blocked允许通过防火墙

客户端通过盗链访问服务端配置：编一个html文件,复制以下内容。并启动httpd服务
href配置成防盗链服务器的IP地址：http://192.168.0.23/index.png
```html

  <html> 
		<head> 
		<title>fang dao lian ce shi</title> 
		</head> 
		<body> 
		<font size="5"> 
		<a href="http://192.168.0.23/index.png">dao lian</a> 
		<br></br>
		</font> 
		</body> 
	</html>
```
![](../51.png)
![](../52.png)
![](../53.png)
![](../54.png)
![](../55.png)
![](../56.png)
![](../57.png)
![](../58.png)
![](../59.png)
![](../60.png)
![](../61.png)

## Nginx负载就均衡算法
1、轮询（默认）
每个请求按时间顺序逐一分配到不同的后端服务,如果后端某台服务器死机,自动剔除故障系统,使用户访问不影响
2、weight（轮询权值    权重）
Weight的值越大,分配到的访问概率越高,主要用于后端每台服务器性能不均衡的情况下,或者仅仅为在主从的情况下设置不同的权值,达到合理有效的利用主机资源
3、ip_hash
每个请求按访问的IP的哈希结果分配,使来自同一个IP的访客固定访问一台后端服务器,并且可以有效解决动态网页存在的session共享问题

### 轮询(默认)
每个请求按时间顺序逐一分配到不同的后端服务器,如果后端服务器down掉,能自动删除
![](../71.png)
![](../72.png)
![](../73.png)
![](../74.png)
![](../75.png)
![](../76.png)

### 权重(weight)
指定轮询几率,weight和访问比率成正比,用于后端服务器性能不均的情况
复制代码代码如下
```bash
upstream bakend {  
server 192.168.0.14 weight=3;  
server 192.168.0.15 weight=1;  
}
```
![](../81.png)
![](../82.png)
![](../83.png)
### ip_hash
每个请求按访问ip的hash结果分配,这样每个访客固定访问一个后端服务器,可以解决session的问题
复制代码代码如下
```bash
upstream bakend {  
ip_hash;  
server 192.168.0.14:88;  
server 192.168.0.15:80;  
} 
```
![](../91.png)
![](../92.png)
![](../93.png)
![](../94.png)

## nginx负载均衡调度状态
在nginx upstream模块中,可以设定每台后端服务器在负载均衡调度中的状态,常用状态有
down: 表示当前的server暂时不参与负载均衡
![](../95.png)
![](../96.png)
![](../97.png)


Backup: 预留的备份及其,当其他所有的非backup及其出现故障或者忙的时候,才会请求backup机器,因此这台及其的访问压力最低
![](../100.png)
![](../101.png)
![](../102.png)

Max_fails: 允许请求失败的次数,默认为1,当超过最大次数后,返回proxy_next_upstream模块定义的错误

Fail_timeout: 请求失败超时时间,在经历了max_fails次失败后暂停服务时间,max_fails和fail_timeout可以一起使用

## 获取数据源地址
具体配置过程如下
```bash
upstream linux { 
      server 10.0.6.108:7080; 
      server 10.0.0.85:8980; 
}
```
将server节点下的location节点中的proxy_pass配置为：
```bash
location / { 
            root  html; 
            index  index.html index.htm; 
            proxy_pass http://linuxidc;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header REMOTE-HOST $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
      }

注解：
            ＃代理到upstream定义的服务地址池
            proxy_pass http://linuxidc;
            ＃获取数据源地址
            proxy_set_header X-Real-IP $remote_addr;
            ＃获取到远程主机地址
            proxy_set_header REMOTE-HOST $remote_addr;
            ＃让服务端获取真实客户端地址
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```
![](../111.png)
![](../112.png)
![](../113.png)
![](../114.png)
![](../115.png)
![](../116.png)
![](../117.png)
![](../118.png)
![](../119.png)
![](../120.png)
![](../121.png)
![](../122.png)
![](../123.png)
