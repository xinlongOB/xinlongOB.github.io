---
title:  Nginx中http模块和stream模块
tags:
  - nginx
  - linux
categories: 运维
date: 2021-04-12 19:36:35
---
nginx配置文件中的模块共分为两大部分：

    第一：http/https–存在于http{ } 模块中
    第二：tcp / udp --存在于stream{ } 模块中


什么是空闲连接超时

    在超时时间内一直没有访问请求，负载均衡会暂时中断当前连接，直到下一次请求来临时重新建立新的连接。

Nginx七层配置（http模块）

    "proxy_read_timeout"：从代理服务器读取响应的超时时间（默认60s），这个可以解决因为代理服务器响应过慢而导致的504Time-out
    "proxy_send_timeout"：向代理服务器发送请求的超时时间（默认60s）
    "keepalive_timeout"：设置一个保持活动的客户端连接在服务器端保持打开状态的超时时间（默认75s）
    "keepalive_requests"：这个参数默认是100，意思就是，一个连接，在他保持连接的状态内（我们设置的是300s），最多能发送1000个请求，如果超过1000请求，那么nginx会把这个连接断掉

keepalive_timeout 既可以配置到http{ }作为全局配置， 也可以配置到server{ }里作为每个监听器的配置
```bash
http {
  proxy_read_timeout 300s;
  proxy_send_timeout 300s;
  keepalive_requests 1000;
  #keepalive_timeout 300s;
 
  server {
    listen 88;
    server_name localhost;
    keepalive_timeout 100s;
    location / {
      proxy_pass http://bb;
    }
  }
   upstream bb {
     server 192.168.159.159:8080;
   }
  }
}
```
Nginx四层配置（stream模块）

"proxy_timeout" 这个参数可以写在stream{ }下，所有server都生效，也可以单独写在一个server的节点下。这个参数不配置的话，默认连接超时是10min
```bash
stream {
  server_traffic_status_zone;
  #proxy_timeout 200s;
  server {
    listen 192.168.159.159:880;
    proxy_timeout 300s;
    proxy_pass aaa;
  }
  upstream aaa {
    server 192.168.159.159:8080;
  }
}
```