---
title: nodejs之http模块
tags:
  - nodejs
  - linux
categories: 开发
date: 2020-06-02 20:38:58
---
## 概述
<br/>nodejs提供了http模块，http模块主要用于搭建HTTP服务端和客户端，要使用HTTP服务器和客户端功能必须调用http模块<br/>
代码如下：

    // 引入http 模块
    var http = require("http");

创建http server
<br/>例如：<br/>

    // 加载 http 模块
    var http = require("http");

    // 创建服务器
    http
      .createServer(function (request, response) {
        // 发送 HTTP 头部
        // HTTP 状态值: 200 : OK
        // 内容类型: text/plain
        response.writeHead(200, { "Content-Type": "text/plain" });

        // 发送响应数据 "Hello World"
        response.end("Hello World\n");
      })
      .listen(8080);

    // 终端打印如下信息
    console.log("Server running at http://127.0.0.1:8080/");

下面介绍下每个步骤：
<br/>创建服务器<br/>
创建服务器使用如下代码：

    http.createServer([requestListener]);

该方法属于http模块，所以我们要先引入http模块，requestListener是一个请求函数，也就是我们上面所写的：

    function (request,response){
      // 函数内容
    }

requestListener 请求函数是一个自动添加到 request 事件的函数（request 事件每次有请求时都会触发，初期学习我们清楚有这个东西就行，不过多的去追究）。函数传递有两个参数：request 请求对象 和 response 响应对象。我们调用 request 请求对象的属性和方法就可以拿到所有 HTTP 请求的信息，我们操作 response 响应对象的方法，就可以把 HTTP 响应返回给浏览器。

response 对象常用的方法有：

  1、response.writeHead(statusCode[;statusMessage][,headers])。表示向请求发送响应头。

      参数说明：
        statusCode：状态码，是一个 3 位 HTTP 状态码，如 404 表示网页未找到，200 表示正常
        statusMessage：可选的，可以将用户可读的 statusMessage 作为第二个参数
        headers：响应头。也就是设置 Content-Type 的值，用于定义网络文件的类型和网页的编码，决定浏览器将以什么形式、什么编码读取这个文件。常用值有：（1）text/html：HTML 格式（2）text/plain：纯文本格式（3）application/x-www-form-urlencoded：数据被编码为名称/值对，这是标准的编码格式。其余的可以自行百度了解，比如 Content-Type 对照表。

  比如：
      
      response.writeHead(200, { "Content-Type": "text/plain;charset=UTF-8" });

  注：

      注：此方法只能在消息上调用一次，并且必须在调用 `response.end()` 之前调用它。

  2、response.write() 发送一块响应主体，也就是说用来给客户端发送响应数据。可以直接写文本信息，也可以写我们的 html 代码，注意要设置 Content-Type 的值。write 可以使用多次，但是最后一定要使用 end 来结束响应，否则客户端会一直等待

  3、response.end() 此方法向服务器发出信号，表示已发送所有响应头和主体，该服务器应该视为此消息完成。必须在每个响应上调用方法 response.end()。

例如：

    // 加载 http 模块
    var http = require("http")

    // 创建服务器

    http
        .createServer(function (request,response){
            // 发送 HTTP 头部
            // HTTP 状态值：200：OK
            // 内容类型：test/html
            response.writeHead(200,{"Content-Type":"test/html;charset=UTF-8"})

            // 发送响应数据 'hello world'
            response.write("ni hao");
            // 发送数据 hello world 并且字体为 h1 格式
            response.write("<h1>hello  world</h1>");
            // 结束
            response.end();
            // 上面的三行代码也可以直接写成 response.end('hello syl <h1>hello world</h1>');
        })
        .listen(8080);
        
        // 终端打印如下信息
        console.log("Server running ai http://127.0.0.1:8080/")

运行结果：

    [sgsm@iZ2ze53g8gh7cdxahhcv95Z ~]$ elinks  http://127.0.0.1:8080
                                                                                                                      http://127.0.0.1:8080/ 
    Hello World     

request对象：

  1、request.url 获取请求路径，获取到的是端口号之后的那一部分路径，也就是说url都是以/开头的，判断路径处理响应
 <br/> 2、request.socket.localAddress 获取 ip 地址<br/>
  3、quest.socket.remotePort 获取源端口

例如：

    // 1. 创建 Server
    var server = http.createServer();

    // 2. 监听 request 请求事件，设置请求处理函数
    server.on("request", function (req, res) {
      console.log("收到请求了，请求路径是：" + req.url);
      console.log(
        "请求我的客户端的地址是：",
        req.socket.remoteAddress,
        req.socket.remotePort
      );
      var url = req.url;
      res.writeHead(200, { "Content-Type": "text/html;charset=UTF-8" });
      if (url === "/") {
        res.end("<h1>Index page</h1>");
      } else if (url === "/login") {
        res.end("<h1>Login page</h1>");
      } else {
        res.end("404 Not Found.");
      }
    });

    // 3. 绑定端口号，启动服务
    server.listen(8080, function () {
      console.log("服务器启动成功，可以访问了。。。");
    });

运行结果：

    