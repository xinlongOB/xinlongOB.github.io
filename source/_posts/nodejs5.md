---
title: nodejs之事件监听
tags:
  - nodejs
  - linux
categories: 开发
date: 2020-05-30 14:14:59
---
## 事件概述
大多数 Node.js 核心 API 构建于惯用的异步事件驱动架构，其中某些类型的对象（又称触发器，Emitter）会触发命名事件来调用函数（又称监听器，Listener）。比如：fs.readStream 打开文件时会发出一个事件。可以通过 require("events"); 获得 event 模块。通常，事件名采用“小驼峰式”（即第一个单词全小写，后面的单词首字母大写，其它字母小写）命名方式。

所有能触发事件的对象都是EventEmitter类的实例，这些对象有一个eventEmitter.on()函数，用于将一个或多个函数绑定到命名事件上。当EventEmitter对象触发一个事件时，所有绑定在该事件上的函数都会被同步地调用

EventEmitter类获取

    // 引入 events模块
    var events = require("events")
    // 创建 eventEmitter对象
    var eventEmitter = new events.EventEmitter();

## 添加监听器

emitter.on(eventName,listener)
<br/>使用emitter.on(eventName,listener)方法为指定事件注册一个监听器，添加listener函数到名为eventname的事件的监听器数组的末尾，不会检查listener是否已被添加。多次调用并传入相同的eventname 与 listener会导致listener会被添加多次<br/>

  参数说明：
    
    eventName：事件名称，string类型。
    listener：回调函数

例子：

    // 引入events模块
    var events = require("events");
    // 创建emitter 对象
    var emitter = new events.EventEmitter();
    // 为 connection 事件注册一个监听器
    emitter.on("connection",function(){
        console.log("已连接");
    });

    // 一秒后调用监视器
    setTimeout(function(){
        emitter.emit("connection");
    },1000)

运行结果：

    $ node listener.js
    已连接    // 一秒后打印已连接

默认情况下，事件监听器会按照添加的顺序依次调用。emitter.prependListener() 方法可用于将事件监听器添加到监听器数组的开头。比如：

    // 引入events模块
    var events = require("events");
    // 创建emitter 对象
    var emitter = new events.EventEmitter();
    // 为 connection 事件注册一个监听器
    emitter.on("connection",function(){
        console.log("我是a");
    });

    emitter.prependListener("connection",function(){
        console.log("我是b");
    });


    // 一秒后调用监视器
    setTimeout(function(){
        emitter.emit("connection");
    },1000)

运行结果：

    $ node listener.js
    我是b
    我是a

注：emitter.addListener(eventName, listener) 是 emitter.on(eventName, listener) 的别名。

## 调用监听器
<br/>使用emitter.emit(eventName[, ...args])按照监听器注册的顺序，同步地调用每个注册到名为eventName的事件监听器，并传入提供的参数。如果事件有注册监听返回True，否则返回false<br/>

  参数说明：
    
    eventName：事件名称
    args：传递的参数，多个，类型为任意

例如：

    // 引入event模块
    var events = require("events");
    // 创建emitter 对象
    var emitter = new events.EventEmitter();
    // 定义一个回调函数
    var callback1 = function(arg1,arg2){
        console.log("print",arg1,arg2);
    };

    var callback2 = function(arg3,arg4){
        console.log("echo ",arg3,arg4);
    };

    // 为 connection 事件注册监听器
    emitter.on("connection",callback1)
    emitter.on("connection",callback2)

    // 调用监听器
    emitter.emit("connection","愿你","安好")

运行结果：

    $ node emit.js
    print 愿你 安好
    echo 愿你 安好

## 只执行一次的监听器
<br/>当时用eventEmitter.on(eventName,listener)注册监听器时，监听器会在每次触发命名事件时被调用。比如：<br/>

    // 引入 events模块
    var  events = require("events");
    // 创建emitter对象
    var emitter = new events.EventEmitter();
    // 为 connectio 事件注册一个监听器
    var n = 0;
    emitter.on("connection",function(){
        ++n;
        console.log("调用第" + n + "次");
    })

    // 调用监听器
    emitter.emit("connection")
    emitter.emit("connection")
    emitter.emit("connection")
    emitter.emit("connection")

运行结果：

    $ node while.js
    调用第1次
    调用第2次
    调用第3次
    调用第4次

使用 eventEmitter.once(eventName, listener) 可以注册最多可调用一次的监听器。当事件被触发时，监听器会被注销，然后再调用。比如：

    // 引入 events模块
    var  events = require("events");
    // 创建emitter对象
    var emitter = new events.EventEmitter();
    // 为 connectio 事件注册一个监听器
    var n = 0;
    emitter.once("connection",function(){    // 把emitter.on  换为 emitter.once
        ++n;
        console.log("调用第" + n + "次");
    })

    // 调用监听器
    emitter.emit("connection")
    emitter.emit("connection")
    emitter.emit("connection")
    emitter.emit("connection")

运行结果：

    $ node while.js
    调用第1次

默认情况下，事件监听器会按照添加的顺序依次调用。emitter.prependOnceListener() 方法可用于将事件监听器添加到监听器数组的开头。用法与我们前面所学的 emitter.prependListener() 方法一致，区别在于这个方法注册的监听器最多只能调用一次

## 移除监听器
<br/>使用emitter.removeListener(eventName.listener)移除监听器<br/>
参数说明：

    eventName：事件名称
    listener：监听器也就是回调函数名称

例如：

    // 引入 events 模块
    var events = require("events");
    // 创建 emitter 对象
    var emitter = new events.EventEmitter();
    // 定义一个回调函数
    var callback = function () {
      console.log("syl");
    };
    // 为 connection 事件注册一个监听器
    emitter.on("connection", callback);
    // 为 connection 事件移除监听器
    emitter.removeListener("connection", callback);
    // 调用监听器
    emitter.emit("connection");

运行结果

    Administrator@WF-20180726HPZY MINGW64 /e/coding/nodejs
    $ node emit.js

    Administrator@WF-20180726HPZY MINGW64 /e/coding/nodejs

注：removeListener() 最多只会从监听器数组中移除一个监听器。我们可以多次调用 removeListener() 的方式来一个个的移除我们需要移除掉的监听器。

一旦事件被触发，所有绑定到该事件的监听器都会按顺序依次调用。也就是说在事件触发之后、且最后一个监听器执行完成之前，removeListener() 或 removeAllListeners() 不会从 emit() 中移除它们。

例如：

    // 引入 events 模块
    var events = require("events");
    // 创建 emitter 对象
    var emitter = new events.EventEmitter();
    // 定义回调函数
    var callback1 = function () {
      console.log("我是1");
      emitter.removeListener("connection", callback2);
    };
    var callback2 = function () {
      console.log("我是2");
    };
    // 为 connection 事件注册监听器
    emitter.on("connection", callback1);
    emitter.on("connection", callback2);
    // 第一次调用监听器，callback1 移除了监听器 callback2，但它依然会被调用。触发时内部的监听器数组为 [callback1, callback2]
    emitter.emit("connection");
    // 第二次调用监听器，此时 callback2 已经被移除了。内部的监听器数组为 [callback1]
    emitter.emit("connection");

运行结果：

    $ node emit.js
    我是1
    我是2
    我是1

## emitter.off(eventName, listener)

emitter.off(eventName, listener) 是 emitter.removeListener() 的别名

## emitter.removeAllListeners([eventName])

使用 emitter.removeAllListeners([eventName]) 移除全部监听器或指定的 eventName 事件的监听器
<br/>例如：<br/>

        // 引入events模块
        var events = require("events")
        // 创建emitter对象
        var emitter = new  events.EventEmitter();
        // 定义回调函数
        var callback1 = function(){
            console.log("我是1");
        };

        var callback2 = function(){
            console.log("我是2");
        };

        // 为connection 事件注册监听器
        emitter.on("connection",callback1);
        emitter.on("connection",callback2);

        // 移除 connection事件的所有监听器
        emitter.removeAllListeners("connection");
        // 调用监听器
        emitter.emit("connection");

运行结果：

        silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs
        $ node removealllisteners.js 

        silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs

    说明所有监听器已被删除

## 设置监听器最大绑定数
<br/>emitter.setMaxListeners(n)<br/>

使用 emitter.setMaxListeners(n) 设置同一事件的监听器最大绑定数。默认情况下，如果为特定事件添加了超过 10 个监听器，则 EventEmitter 会打印一个警告，这有助于我们发现内存泄露。显然实际编码中并不是所有的事件都要限制 10 个监听器。emitter.setMaxListeners() 方法可以为指定的 EventEmitter 实例修改限制。当值设为 Infinity（或 0）表示不限制监听器的数量

使用 emitter.listenerCount(eventName) 查看事件绑定的监听器个数

例：

        // 引入 events 模块
        var events = require("events");
        // 创建emitter 对象
        var emitter = new  events.EventEmitter();
        // 定义回调函数
        var callback1 = function(){
            console.log("test1");
        }

        var callback2 = function(){
            console.log("test2");
        }

        // 为connection 事件注册监听器
        emitter.on("connection",callback1);
        emitter.on("connection",callback2);

        // 查看connection 事件绑定的监听个数 赋值给num
        var num = emitter.listenerCount("connection");
            console.log(num);

运行结果：

        silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs
        $ node maxlisteners.js
        2

        silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs
    
## ERROR 事件
<br/>当EventEmitter 实例出错时，应该触发'error'事件<br/>

如果没有为'error'事件注册监听器，则当'error'事件触发时，会抛出错误、打印堆栈跟踪、并退出nodejs进程
<br/>例如：<br/>

        var events = require("events");
        var emitter = new events.EventEmitter();
        emitter.emit("error");

运行结果：

        silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs
        $ node error.js 
        events.js:201
            throw err; // Unhandled 'error' event
            ^

        Error [ERR_UNHANDLED_ERROR]: Unhandled error. (undefined)
            at EventEmitter.emit (events.js:199:17)
            at Object.<anonymous> (E:\程序代码\nodejs\error.js:3:9)
            at Module._compile (internal/modules/cjs/loader.js:959:30)
            at Object.Module._extensions..js (internal/modules/cjs/loader.js:995:10)
            at Module.load (internal/modules/cjs/loader.js:815:32)
            at Function.Module._load (internal/modules/cjs/loader.js:727:14)
            at Function.Module.runMain (internal/modules/cjs/loader.js:1047:10)
            at internal/main/run_main_module.js:17:11 {
        code: 'ERR_UNHANDLED_ERROR',
        context: undefined
        }

        silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs

因为没有定义函数，会报错 undefined

通常我们要为会触发 error 事件的对象设置监听器，避免遇到错误后整个程序崩溃。比如：

        var events = require("events");
        var emitter = new events.EventEmitter();
        // 设置监听器
        var error = function(){
            console.error("错误信息");
        }
        emitter.on("connection",error)
        emitter.emit("connection");

或者写为箭头函数：

        // 引入 events 模块
        var events = require("events");
        // 创建 emitter 对象
        var emitter = new events.EventEmitter();
        // 设置监听器
        emitter.on("error", (err) => {
        console.error("错误信息");
        });
        emitter.emit("error");

运行结果：

        silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs
        $ node error.js
        错误信息

        silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs

