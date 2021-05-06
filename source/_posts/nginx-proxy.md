---
title: CentOS下nginx配置 stream 转发
tags:
  - nginx
  - linux
categories: 运维
date: 2021-04-12 17:07:40
---
nginx tcp 代理
```bash
nginx-1.17.9使用增加了stream 模块用于一般的TCP 代理和负载均衡，ngx_stream_core_module 这个模块在1.90版本后将被启用。但是并不会默认安装，
    需要在编译时通过指定 --with-stream 参数来激活这个模块
```
```bash
wget   http://nginx.org/download/nginx-1.16.1.tar.gz

yum install -y freetype-devel bzip2 bzip2-devel ncurses-devel openssl-devel libxml2 libxml2-devel libjpeg-dev gd-devel gd lrzsz libxml2-devel gcc gcc-c++ re2c libxml2 libxml2-devel libcurl-devel libjpeg-turbo libjpeg-turbo-devel libpng libpng-devel  libtool-ltdl-devel openssl openssl-devel freetype-devel libmemcached cyrus-sasl cyrus-sasl-lib cyrus-sasl-devel libmemcached-devel pcre-devel openssl* zlib zlib-devel tcl gdbm-devel

tar xf  nginx-1.16.1.tar.gz    -C  /usr/src/

# 注意：如果使用 nginx 的 stream 功能，在编译时一定要加上 "--with-stream"
./configure --prefix=/usr/local/nginx-1.16.1 --error-log-path=/var/logs/nginx/error.log --user=www --group=www --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_ssl_module --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector --param=ssp-buffer-size=4 -m64 -mtune=generic'  --with-http_stub_status_module

make  &&  make install
```
配置文件
```bash
[root@iZt4ng8emsvi9tuuj4ha6vZ conf]# cat nginx.conf
user  www www;
worker_processes  auto;

worker_rlimit_nofile 102400;
events {
    use epoll;  # 使用 epoll 的 I/O 模型
    worker_connections 102400;  # 连接数，在大量请求的时候需要调大此参数
}


http {
    include       mime.types;
    default_type  application/octet-stream;
    log_format access '$time_local |$request |$remote_addr |$http_host |'
                    '$status |$request_time |$upstream_response_time |$body_bytes_sent |'
                    '"$http_referer" |$request_method |"$uri" |$http_host$uri |'
                    '$upstream_addr |$upstream_status |'
                    '"$http_x_forwarded_for" |"$http_user_agent"';


    access_log  /var/log/nginx/http-access.log access;
    sendfile        on;
    keepalive_timeout  65;
    server {
        listen       80;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    include vhosts/*.conf;
}

# steam 流转发配置
stream {
    log_format proxy '$remote_addr [$time_local] '
                 '$protocol $status $bytes_sent $bytes_received '
                 '$session_time "$upstream_addr" '
                 '"$upstream_bytes_sent" "$upstream_bytes_received" "$upstream_connect_time"';
    access_log /var/log/nginx/tcp-access.log proxy ;
    open_log_file_cache off;
    include tcpproxy/*.conf;
}
```
```bash
[root@iZt4ng8emsvi9tuuj4ha6vZ conf]# cat vhosts/rft-log.conf  # 代理

upstream rft-log.com{
        server 192.168.4.201:80 weight=4 max_fails=1 fail_timeout=10s;
} 

server {
    listen 80;
    server_name   rft-log.com;

    location / {
        proxy_pass  http://rft-log.com;
    }
}
[root@iZt4ng8emsvi9tuuj4ha6vZ conf]# cat tcpproxy/10000.conf   # 转发  IP后必须加端口
    upstream backend10000 {
       # hash $remote_addr consistent;
        server 192.168.4.201:10000;
    }

    server {
        listen 10000 so_keepalive=on; # 支持长链接
       # proxy_connect_timeout 1s;
       # proxy_timeout 3s;
        proxy_pass 192.168.4.201:10000;
        # proxy_pass  backend10000;
    }
```
```bash
# 集群模板
upstream back{
    server 10.10.62.210:3306 up;
    server 10.10.51.213:3306 up;
}
server {
    listen 3301;
    proxy_connect_timeout 5s;
    proxy_timeout 300s;
    proxy_pass back;
}
```

转