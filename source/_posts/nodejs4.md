---
title: nodejs之基础函数
tags:
  - nodejs
  - liunx
categories: 开发
date: 2020-05-30 09:44:42
---
## 搜索模块

     npm search express

在JavaScript中，一个函数可以作为另一个函数的参数。我们可以先定义一个函数，然后传递，也可以在传递参数的地方直接定义函数。

Node.js中函数的使用与Javascript类似，举例来说，你可以这样做： 

    function say(word) {
      console.log(word);
    }

    function execute(someFunction, value) {
      someFunction(value);
    }

    execute(say, "Hello");

执行结果

    Administrator@WF-20180726HPZY MINGW64 /e/coding/nodejs
    $ node test.js
    word

 以上代码中，我们把 say 函数作为execute函数的第一个变量进行了传递。这里传递的不是 say 的返回值，而是 say 本身！
<br/>这样一来， say 就变成了execute 中的本地变量 someFunction ，execute可以通过调用 someFunction() （带括号的形式）来使用 say 函数。<br/>
当然，因为 say 有一个变量， execute 在调用 someFunction 时可以传递这样一个变量。 

## 匿名函数
<br/>我们可以把一个函数作为变量传递，但是我们不一样要绕这个"先定义，在传递"的圈子，我们可以直接在另一个函数的括号中定义和传递这个参数：<br/>

    function execute(someFunction,value){
        someFunction(value);
    }
    execute(function(word){console.log(word)},"hello")

运行结果：

    $ node test.js
    hello

 我们在 execute 接受第一个参数的地方直接定义了我们准备传递给 execute 的函数。用这种方式，我们甚至不用给这个函数起名字，这也是为什么它被叫做匿名函数 。 

 ## 导出函数

    function say(word){
          console.log("word");
      }

      exports.say = say;

接收函数

    var test = require("./test.js")

    test.say();

执行结果

    $ node a.js
    word