---
title: Node.js 创建第一个应用 
tags:
  - nodejs
  - liunx
categories: 开发
date: 2020-05-28 17:14:27
---

  1、引入 required 模块：使用 required 指令来载入 Node.js 模块。
  2、创建服务器：服务器可以监听客户端的请求，类似于 Apache、Nginx 等 HTTP 服务器。
  3、接受请求与响应请求。

新建一个名为 server.js 的文件

    var http = require("http"); // 加载 http 模块，并将实例化的 HTTP 赋值给变量 http

    http
      .createServer(function (request, response) {
        // 发送 HTTP 头部
        // HTTP 状态值: 200 : OK
        // 内容类型: text/plain
        response.writeHead(200, { "Content-Type": "text/plain" });

        response.end("Hello World\n"); // 发送响应数据 "Hello World"
      })
      .listen(8080);

    // 终端打印如下信息
    console.log("Server running at http://127.0.0.1:8080/");

以上代码我们完成了一个可以工作的 HTTP 服务器。本地计算机使用 node server.js 命令后，直接在浏览器中访问 http://127.0.0.1:8888/，你会看到一个写着 "Hello World"的网页

由于我的环境部署在centos系统上，可以直接使用elinks访问，访问结果

    [sgsm@iZ2ze53g8gh7cdxahhcv95Z ~]$ elinks  http://127.0.0.1:8080
                                                                                                                      http://127.0.0.1:8080/ 
    Hello World     