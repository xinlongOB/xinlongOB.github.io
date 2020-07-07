---
title: 初学nodejs
tags:
  - nodejs
  - linux
categories: 开发
date: 2020-05-28 15:11:48
---
## Node.js 概述

Node.js 是一个能够在服务器端运行 JavaScript 的开放源代码、跨平台 JavaScript 运行环境。Node.js 由 Node.js 基金会持有和维护，并与 Linux 基金会有合作关系。Node.js 采用 Google 开发的 V8 运行代码，使用事件驱动、非阻塞和异步输入输出模型等技术来提高性能，可优化应用程序的传输量和规模。这些技术通常用于数据密集的即时应用程序。

Node.js 大部分基本模块都用 JavaScript 语言编写。在 Node.js 出现之前，JavaScript 通常作为客户端程序设计语言使用，以 JavaScript 写出的程序常在用户的浏览器上运行。Node.js 的出现使 JavaScript 也能用于服务端编程。Node.js 含有一系列内置模块，使得程序可以脱离 Apache HTTP Server 或 IIS，作为独立服务器运行。

注：定义来自维基百科。

## Node.js 特点

  1、它是一个 JavaScript 运行环境。
  <br/>2、依赖于 Chrome V8 引擎进行代码解释。<br/>
  3、事件驱动：在 Node.js 中，客户端请求建立连接，提交数据等行为，会触发相应的事件。Node.js 在一个时刻，只能执行一个事件回调函数，但是在执行一个事件回调函数的中途，可以转而处理其他事件，然后返回继续执行原事件的回调函数。
  <br/>4、非阻塞 I/O：Node.js 中采用了非阻塞型 I/O 机制，在执行了访问数据库的代码之后，将立即转而执行其后面的代码，把数据库返回结果的处理代码放在回调函数中，从而提高了程序的执行效率。<br/>
  5、轻量可伸缩，适用于实时数据交互应用。
  <br/>6、单线程：好处是减少内存开销，不用像多线程编程那样处处在意状态同步的问题。缺点是错误会引起整个应用的退出。<br/>

## Node.js 适用场景

我们从 Node.js 的特点中可以知道 Node.js 擅长处理 I/O，不善于计算（单线程的缺点），因此 Node.js 适用于：当应用程序需要处理大量并发的 I/O，而在向客户端发出响应之前，应用程序内部并不需要进行非常复杂的处理的时候，Node.js 也非常适合与 Web socket 配合，开发长连接的实时交互应用程序。比如：聊天室，博客系统，考试系统等。


## NPM介绍
npm是随同nodejs一起安装的包管理工具

在中断中查看系统nodejs的版本：

    node -v

查看系统中npm版本

    npm  -v

## 启动node终端
<br/>类似于python的终端，启动node终端直接输入node

    node

基础的计算

    > 1 + 1
    2
    > 2 * 2
    4
    > 6 / 2
    3
    > 2+(8*3)-10
    16
    > 

多行表达式

    > for(var i=0; i < 8 ; i++){
    ... console.log(i);
    ... }
    0
    1
    2
    3
    4
    5
    6
    7
    undefined
    > 

三个点的符号是系统自动生成的，回车换行后即可。Node.js 会自动检测是否为连续的表达式。

下划线变量 -- 可以使用下划线（_）获取上一个表达式的运算结果：

    > var a = 10 ; var b = 20 ; a + b;
    30
    > var num = _;
    undefined
    > console.log(num)
    30
    undefined
    > 

REPL 常用命令

    Ctrl + C - 退出当前终端。
    Ctrl + C - 连续按两次退出 Node REPL。
    Ctrl + D - 退出 Node REPL。
    向上/向下键 - 查看输入的历史命令

运行 JavaScript 文件

新建两个 JavaScript 文件名为为 test.j写下如下代码：

test.js 中的代码：

    console.log("hello world");

运行结果

    [sgsm@localhost nodejs]$ node  test.js 
    hello world


## 全局变量

按照 ECMAScript 的定义，满足以下条件的变量是全局变量：

    在最外层定义的变量。
    全局对象的属性。
    隐式定义的变量（未定义直接赋值的变量）。

注：当你定义一个全局变量的时候，这个变量同时也会成为全局对象的属性，反之亦然。在 Node.js 中你不可能在最外层定义变量，因为所有用户代码都是属于当前模块的，而模块本身不是最外层上下文。定义变量一定要使用 var 关键字，因为全局变量会污染命名空间。

下面介绍一些常用的全局变量和全局函数：

  __filename 表示当前正在执行的脚本的文件名。它将输出文件所在位置的绝对路径，且和命令行参数所指定的文件名不一定相同。如果在模块中，返回的值是模块文件的路径。比如创建一个叫 fnTest.js 的文件，输入以下代码：

    console.log(__filename);

  运行结果

    [sgsm@bogon nodejs]$ node fnTest.js
    /home/sgsm/nodejs/fnTest.js


  __dirname 表示当前执行脚本所在的目录。比如创建一个 dnTest.js 的文件，输入以下代码：

    console.log(__dirname);

  运行结果

    [sgsm@bogon nodejs]$ node dnTest.js 
    /home/sgsm/nodejs

  setTimeout(cb, ms) 全局函数在指定的毫秒（ms）数后执行指定函数（cb），只执行一次函数。比如创建一个 st.js 的文件，输入以下代码：

      function foo() {
        console.log("Hello, syl!");
      }
      // 三秒后执行以上函数
      setTimeout(foo, 3000);

  运行结果

    [sgsm@bogon nodejs]$ node  st.js 
    Hello, syl!
    [sgsm@bogon nodejs]$

  clearTimeout(t) 用于停止一个之前通过 setTimeout() 创建的定时器。参数 t 是通过 setTimeout() 函数创建的定时器。比如清除上面案例的定时器：

      function foo() {
        console.log("Hello, syl!");
      }
      // 三秒后执行以上函数
      var t = setTimeout(foo, 3000);
      // 清除定时器
      clearTimeout(t);

  运行结果

      [sgsm@bogon nodejs]$ node  st.js 
      [sgsm@bogon nodejs]$ 

  setInterval(cb, ms) 与 setTimeout(cb, ms) 类似，不同的是这个方法会不停的执行函数。直到 clearInterval() 被调用或窗口被关闭，也可以按 Ctrl + C 停止。比如创建一个 sI.js 的文件，输入以下代码：

      function foo() {
        console.log("Hello, syl!");
      }
      // 三秒后执行以上函数
      var t = setInterval(foo, 3000);
      // 清除定时器
      clearInterval(t);

  运行结果

      [sgsm@bogon nodejs]$ node  st.js 
      [sgsm@bogon nodejs]$ 

  如果不加clearInterval的运行结果

      [sgsm@iZ2ze53g8gh7cdxahhcv95Z nodejs]$ node st.js 
      Hello, syl!
      Hello, syl!
      Hello, syl!

  console.log() 是个全局函数用于进行标准输出流的输出，即在控制台中显示一行字符串，和 JavaScript 中的使用一样