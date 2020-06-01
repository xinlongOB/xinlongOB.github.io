---
title: nodejs模块
tags:
  - nodejs
  - liunx
categories: 开发
date: 2020-05-29 22:13:16
---
## 概述
<br/>包用于管理多个模块及依赖关系，可以对多个模块进行封装，包的根目录必须包含package.jsonwenjian，package.json文件是commonjs规范用于描述包的文件，符合commonjs规范的package.json文件一般包含以下字段：<br/>
  1、name：包名。包名是唯一的，只能包含小写字母、数字和下划线
  2、version：包版本号
  3、description：包说明
  4、keywords：关键字数组，用于搜索
  5、homepage：项目主页
  6、bugs：提交bug的地址
  7、license：许可证
  8、maintainers：维护者数组
  9、contribution：贡献者数组
  10、repositories：项目仓库托管地址数组
  11、dependencies：包依赖

下面是一个package.json示例：

    {
      "name": "shiyanlou",
      "description": "Shiyanlou test package.",
      "version": "0.1.0",
      "keywords": ["shiyanlou", "nodejs"],
      "maintainers": [
        {
          "name": "test",
          "email": "test@shiyanlou.com"
        }
      ],
      "contributors": [
        {
          "name": "test",
          "web": "http://www.shiyanlou.com/"
        }
      ],
      "bugs": {
        "mail": "test@shiyanlou.com",
        "web": "http://www.shiyanlou.com/"
      },
      "licenses": [
        {
          "type": "Apache License v2",
          "url": "http://www.apache.org/licenses/apache2.html"
        }
      ],
      "repositories": [
        {
          "type": "git",
          "url": "http://github.com/test/test.git"
        }
      ],
      "dependencies": {
        "webkit": "1.2",
        "ssl": {
          "gnutls": ["1.0", "2.0"],
          "openssl": "0.9.8"
        }
      }
    }

注：package.json 文件可以自己手动编辑，还可以通过 npm init 命令进行生成。你可以自己尝试在终端中输入 npm init 命令来生成一个包含 package.json 文件的包。直接输入 npm init --yes 跳过回答问题步骤，直接生成默认值的 package.json 文件。此外，我们在 github 上传自己项目的时候，通常是不会把 node_modules 这个文件夹传上去的（太大了），只需要有 package.json 就能通过 npm install 命令安装所有依赖

    npm init --yes

执行结果


    Wrote to E:\程序代码\nodejs\package.json:

    {
      "name": "nodejs",
      "version": "1.0.0",
      "description": "",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [],
      "author": "",
      "license": "ISC"
    }

注：执行init的文件夹名称必须要符合要求不可以包含中文以及特殊字符

## 包操作

通过命令npm  install  xxx来安装包。比如：
<br/>安装包<br/>

    npm install  express

更新包

    npm update  express

删除包

    npm  uninstall express

搜索包

     npm search express
     
注：安装包的时候指定版本使用@  例如： npm install pm2@2.8.0


在JavaScripts中，我们通常把 JavaScript 代码分为几个 js 文件，然后在浏览器中将这些 js 文件合并运行，但是在 Node.js 中，是通过以模块为单位来划分所有功能的。每一个模块为一个 js 文件，每一个模块中定义的全局变量和函数的作用范围也被限定在这个模块之内，只有使用 exports 对象才能传递到外部使用。Node.js 官方提供了很多模块，这些模块分别实现了一种功能，如操作文件及文件系统的模块 fs，构建 http 服务的模块 http，处理文件路径的模块 path 等。当然我们也可以自己编写模块。

模块的使用
<br/>在nodejs创建模块很简单，比如我们创建一个myMoudule.js的文件<br/>

    function foo(){
      console.log("hello world")
    }

这样就创建好了一个模块，但是别的模块如何来访问它呢？我们使用 module.exports 来导出它。也就是说把 myModule.js 的代码改写成下面这样：

    function foo(){
      console.log("hello world")
    }
    module.exports.foo = foo;

最后我们在创建一个index.js的文件，使用require()函数来访问上面的模块。输入一下代码：

    var hello = require("./myModule.js");
    hello.foo();


运行结果：

    $ node myModule.js 


    $ node index.js
    hello world

注：require() 加载模块，以 '/' 为前缀的模块是文件的绝对路径。'./' 为前缀的模块是相对于调用 require() 的文件的，上面的例子中 index.js 和 myModule.js 是在同一个目录下（project 目录）。当没有以 '/'、'./' 或 '../' 开头来表示文件时，这个模块必须是一个核心模块或加载自 node_modules 目录。如果给定的路径不存在，则 require() 会抛出一个 code 属性为 'MODULE_NOT_FOUND' 的 Error。

## 核心模块
<br/>核心模块定义在nodejs源代码的lib/ 目录下。require()总是会优先加载核心模块。例如：require('http')始终返回内置的HTTP模块，即使有同名文件<br/>

## 循环
<br/>当循环调用require()时，一个模块可能在未完成执行时被返还。比如：<br/>

a.js 的代码为：

    console.log("a 开始");
    exports.done = false;
    var b = require("./b.js");
    console.log("在 a 中，b.done = %j", b.done);
    exports.done = true;
    console.log("a 结束");

b.js 的代码为：

    console.log("b 开始");
    exports.done = false;
    var a = require("./a.js");
    console.log("在 b 中，a.done = %j", a.done);
    exports.done = true;
    console.log("b 结束");

main.js 的代码为：

    console.log("main 开始");
    var a = require("./a.js");
    var b = require("./b.js");
    console.log("在 main 中，a.done=%j，b.done=%j", a.done, b.done);

运行效果为：

    silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs
    $ node main.js
    main 开始
    a 开始
    b 开始
    在 b 中，a.done = false
    b 结束
    在a 中,b.done = true
    a 结束
    在 main 中，a.done=true，b.done=true

    silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs

也就是说当 main.js 加载 a.js 时，a.js 又加载 b.js。此时，b.js 会尝试去加载 a.js。为了防止无限的循环，会返回一个 a.js 的 exports 对象的未完成的副本给 b.js 模块。然后 b.js 完成加载，并将 exports 对象提供给 a.js 模块。

module.exports 和 exports 的区别
<br/>我们发现每次导出接口成员的时候都通过module.exports.xxx = xxx 的方式很麻烦，点儿的太多了。所以，nodejs为了简化你的操作，专门提供了一个变量：exports 等于 module.exports。也就是说在模块中还有这么一句代码<br/>

    var exports = module.exports;

我们前面案例中的代码也就可以简写了：

    module.exports.foo = foo;
    exports.foo = foo; // 这两行代码效果是一样的

但是需要注意的是：就像任何变量，如果一个新的值被赋值给 exports，它就不再绑定到 module.exports。我们具体来看个例子：

a.js 的代码:

    console.log(module.exports === exports);

运行结果

    silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs
    $ node 1.js
    true

    silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs

两者一致，说明我们可以用任意一个来导出内部成员

b.js 的代码：

    exports = {
      a: 3,
    };
    console.log(exports);
    console.log(module.exports);
    console.log(exports === module.exports);

运行结果

    silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs
    $ node 1.js
    { a: 3 }
    {}
    false

    silly dog@LAPTOP-OEVDT7RG MINGW64 /e/程序代码/nodejs

也就是说给 exports 赋值会断开和 module.exports 之间的引用，同样的给 module.exports 重新赋值也会断开它们之间的引用。但是最终导出的是 module.exports，在上面的例子中我们另外一个文件来用 require() 加载 b.js 只会得到 {} 而不是 {a:3}。

总结：require() 得到的是 module.exports 导出的值，导出多个成员可以用 module.exports 和 exports，导出单个成员只能用 module.exports。如果你实在不好区分，那就全部都使用 module.exports 也是没问题的。