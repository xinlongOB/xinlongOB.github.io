---
title: nodejs之fs模块
tags:
  - nodejs
  - Linux
categories: 开发
date: 2020-06-03 22:40:41
---
## 概述
<br/>nodejs中的fs模块提供了一个API，用于以接近标准POSTX函数的方式与文件系统进行交互<br/>
导入文件系统模块的语法如下：

    var fs = require("fs");

所有文件系统操作都具有同步和异步的形式，异步方法中回调函数的第一个参数总是留给异常参数(execption)，如果方法成功完成，那么这个参数为null 或者 undefined

 在nodejs中绝大部分需要在服务器运行期反复执行业务逻辑的代码，必须使用异步代码。否则，同步代码在执行时期，服务器将停止响应，因为nodejs是单线程

 服务器启动时如果需要读取配置文件，或者结束时需要写入到状态文件时，可以使用同步代码。因为这些代码只在启动和结束时执行一次，不影响服务器正常运行时的异步执行

 异步打开文件的语法格式为：

    fs.open(path,flags[,mode],callback);

  参数说明：

      path：文件的路径
      flags：文件打开的行为
      mode：设置文件模式(权限)，文件创建默认权限为0666(可读写)，
            mode设置文件模式(权限和粘滞位)，但仅限于创建文件的情况，在windows上只能操作写权限
      callback：回调函数，带有两个参数如：callback(err.fd)

  flags参数可以是以下值：

      a' - 打开文件用于追加。如果文件不存在，则创建该文件。
      'ax' - 与 'a' 相似，但如果路径存在则失败。
      'a+' - 打开文件用于读取和追加。如果文件不存在，则创建该文件。
      'ax+' - 与 'a+' 相似，但如果路径存在则失败。
      'as' - 以同步模式打开文件用于追加。如果文件不存在，则创建该文件。
      'as+' - 以同步模式打开文件用于读取和追加。如果文件不存在，则创建该文件。
      'r' - 打开文件用于读取。如果文件不存在，则会发生异常。
      'r+' - 打开文件用于读取和写入。如果文件不存在，则会发生异常。
      'rs+' - 以同步模式打开文件用于读取和写入。指示操作系统绕开本地文件系统缓存。这对于在 NFS 挂载上打开文件非常有用，因为它允许跳过可能过时的本地缓存。它对 I/O 性能有非常实际的影响，因此除非需要，否则不建议使用此标志。这不会将 fs.open() 或 fsPromises.open() 转换为同步的阻塞调用。如果需要同步操作，则应使用 fs.openSync() 之类的操作。
      'w' - 打开文件用于写入。创建文件（如果它不存在）或截断文件（如果存在）。
      'wx' - 与 'w' 相似，但如果路径存在则失败。
      'w+' - 打开文件用于读取和写入。创建文件（如果它不存在）或截断文件（如果存在）。
      'wx+' - 与 'w+' 相似，但如果路径存在则失败。

例如：
<br/>新建一个input.txt文件，不写任何内容，然后创建file.js文件打开input.txt文件进行读写，代码如下<br/>

    // 引入 fs 模块
    var fs = require("fs")
    // 异步打开文件
    fs.open("input.txt","r+",function(err,fd){
        if (err){
            return console.error(err);
        }
        console.log("文件打开成功");
    })

运行结果：

    silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs
    $ node  fs.js
    文件打开成功

同步打开文件的语法格式为：

    fs.openSync(path, flags[, mode])


异步关闭文件的语法格式为：

    fs.close(fd,callback);

参数说明：

  fd：通过fs.open()方法返回的文件描述符
  callback：回调函数，除了可能的异常，完成回调没有其他参数

例如：

    // 引入 fs 模块
    var fs = require("fs")
    // 异步打开文件
    fs.open("input.txt","r+",function(err,fd){
        if (err){
            return console.error(err);
        }
        console.log("文件打开成功");


        fs.close(fd,function (err){
            if (err){
                console.log(err);
            }
            console.log("文件关闭成功");
        });
    });

运行结果：

    silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs
    $ node  fs.js
    文件打开成功
    文件关闭成功

使用fs.read()和fs.write()读写文件需要使用fs.open()打开文件和fs.close()关闭文件

使用fs.read读取文件

异步读取文件的语法格式为：

    fs.read(fd, buffer, offset, length, position, callback);

参数说明：

    fd：通过 fs.open() 方法返回的文件描述符。
    buffer：是数据写入的缓冲区。
    offset：是缓冲区中开始写入的偏移量。一般它的值我们写为 0。
    length：是一个整数，指定要读取的字节数。
    position：指定从文件中开始读取的位置。如果 position 为 null，则从当前文件位置读取数据，并更新文件位置。
    callback：回调函数，有三个参数 (err, bytesRead, buffer)。err 为错误信息，bytesRead 表示读取的字节数，buffer 为缓冲区对象。

例如：
<br/>新建一个test.txt文件写入：hello world。在新建一个read.js文件，写上如下代码：<br/>

    // 引入 fs 模块
    var fs = require("fs");
    // 打开文件
    fs.open("test.txt","r+",function (err,fd){
        if (err){
            return console.error(err);
        }
        console.log("文件打开成功");


    // 读取文件内容
        var buf = Buffer.alloc(1024);
    fs.read(fd,buf,0,buf.length,0, function(err,bytesRead,buffer){
        if (err){
            return console.error(err);
        }
        console.log(bytesRead + " 字节被读取");
        if (bytesRead > 0){
            console.log(buffer.slice(0,bytesRead).toString());
        }


    // 关闭文件
    fs.close(fd,function(err,fd){
        if (err){
            return console.error(err);
        }
        console.log("文件关闭成功");
        });
      });
    });

    