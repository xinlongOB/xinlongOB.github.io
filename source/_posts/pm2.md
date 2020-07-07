---
title: pm2安装报错
tags:
  - pm2
  - linux
categories:
  - 运维
  - 开发
date: 2020-05-20 15:19:18
---
## 使用npm安装pm2报错

    sudo  npm  -g  install   pm2  

报错日志

    gyp WARN EACCES user "root" does not have permission to access the dev dir "/root/.node-gyp/4.9.1"
    gyp WARN EACCES attempting to reinstall using temporary dev dir "/usr/lib/node_modules/pm2/node_modules/@pm2/agent/node_modules/utf-8-validate/.node-gyp"
    make: Entering directory `/usr/lib/node_modules/pm2/node_modules/@pm2/agent/node_modules/utf-8-validate/build'
      CC(target) Release/obj.target/validation/src/validation.o
    make: cc: Command not found
    make: *** [Release/obj.target/validation/src/validation.o] Error 127
    make: Leaving directory `/usr/lib/node_modules/pm2/node_modules/@pm2/agent/node_modules/utf-8-validate/build'
    gyp ERR! build error 
    gyp ERR! stack Error: `make` failed with exit code: 2
    gyp ERR! stack     at ChildProcess.onExit (/usr/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:276:23)
    gyp ERR! stack     at emitTwo (events.js:87:13)
    gyp ERR! stack     at ChildProcess.emit (events.js:172:7)
    gyp ERR! stack     at Process.ChildProcess._handle.onexit (internal/child_process.js:211:12)
    gyp ERR! System Linux 3.10.0-957.el7.x86_64
    gyp ERR! command "/usr/bin/node" "/usr/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
    gyp ERR! cwd /usr/lib/node_modules/pm2/node_modules/@pm2/agent/node_modules/utf-8-validate
    gyp ERR! node -v v4.9.1
    gyp ERR! node-gyp -v v3.4.0
    gyp ERR! not ok 

这个意思是因为node版本太低很多依赖库无法使用

更换镜像重试下

    sudo  npm install -g pm2 --registry=https://registry.npm.taobao.org  

使用cnpm安装试下

    sudo  npm install -g cnpm --registry=https://registry.npm.taobao.org  
    sudo  cnpm install -g  pm2


还是报错，只有升级node的版本了

## node有一个模块叫 n ，是专门用来管理node.js的版本的    
<br/>第一步：首先安装n模块:<br/>

    sudo  npm install -g n

第二步：升级node.js到最新稳定版

    n stable

第二步：n后面也可以跟随版本号比如

    n v0.10.26
    n 0.10.26



安装管理命令

    sudo npm install -g n  

    [sgsm@f069vn-thamdinh yum.repos.d]$ sudo n stable

      installing : node-v12.16.3
          mkdir : /usr/local/n/versions/node/12.16.3
          fetch : https://nodejs.org/dist/v12.16.3/node-v12.16.3-linux-x64.tar.xz
      installed : v12.16.3 to /usr/local/bin/node
          active : v4.9.1 at /bin/node

升级node之后就可以安装pm2了

    [sgsm@f069vn-thamdinh yum.repos.d]$ pm2 list             

                            -------------

    __/\\\\\\\\\\\\\____/\\\\____________/\\\\____/\\\\\\\\\_____
    _\/\\\/////////\\\_\/\\\\\\________/\\\\\\__/\\\///////\\\___
      _\/\\\_______\/\\\_\/\\\//\\\____/\\\//\\\_\///______\//\\\__
      _\/\\\\\\\\\\\\\/__\/\\\\///\\\/\\\/_\/\\\___________/\\\/___
        _\/\\\/////////____\/\\\__\///\\\/___\/\\\________/\\\//_____
        _\/\\\_____________\/\\\____\///_____\/\\\_____/\\\//________
          _\/\\\_____________\/\\\_____________\/\\\___/\\\/___________
          _\/\\\_____________\/\\\_____________\/\\\__/\\\\\\\\\\\\\\\_
            _\///______________\///______________\///__\///////////////__


                              Runtime Edition

            PM2 is a Production Process Manager for Node.js applications
                        with a built-in Load Balancer.

                    Start and Daemonize any application:
                    $ pm2 start app.js

                    Load Balance 4 instances of api.js:
                    $ pm2 start api.js -i 4

                    Monitor in production:
                    $ pm2 monitor

                    Make pm2 auto-boot at server restart:
                    $ pm2 startup

                    To go further checkout:
                    http://pm2.io/


                            -------------

    [PM2] Spawning PM2 daemon with pm2_home=/home/sgsm/.pm2
    [PM2] PM2 Successfully daemonized
    ┌─────┬───────────┬─────────────┬─────────┬─────────┬──────────┬────────┬──────┬───────────┬──────────┬──────────┬──────────┬──────────┐
    │ id  │ name      │ namespace   │ version │ mode    │ pid      │ uptime │ ↺    │ status    │ cpu      │ mem      │ user     │ watching │
    └─────┴───────────┴─────────────┴─────────┴─────────┴──────────┴────────┴──────┴───────────┴──────────┴──────────┴──────────┴──────────┘

其实到这里就出现了很大的问题了，首先应该需要确定下代码是否支持node的高版本，所以更改node版本后其他进程就会有问题
<br/>(还好是测试服，如果正式服我估计现在已经没办法写这个文档了)<br/>

最后只能还原node版本

    sudo n   v4.4.4

## 然后把进程启动，关于安装pm2只能使用下面的办法了
<br/>找一个同版本node并且已经安装pm2的机器<br/>

    [sgsm@f069vn-thamdinh ~]$ rpm -ql  nodejs  |head  -30
    /usr/bin/node
    /usr/bin/npm
    /usr/lib/node_modules
    /usr/lib/node_modules/npm
    /usr/lib/node_modules/npm/.mailmap
    /usr/lib/node_modules/npm/.npmignore
    /usr/lib/node_modules/npm/.travis.yml
    /usr/lib/node_modules/npm/AUTHORS
    /usr/lib/node_modules/npm/CHANGELOG.md
    /usr/lib/node_modules/npm/CONTRIBUTING.md
    /usr/lib/node_modules/npm/LICENSE
    /usr/lib/node_modules/npm/Makefile

查看到nodejs模板文件的安装位置然后进入目录

    cd /usr/lib/node_modules/

ls会查看到有一个pm2的目录

    [sgsm@localhost node_modules]$ ll
    总用量 4
    drwxr-xr-x 9 root   root  4096 12月 12 2018 npm
    drwxr-xr-x 5 nobody users  320 5月   6 2019 pm2
    drwxr-xr-x 8 nobody root   267 12月 12 2018 pomelo

直接使用tar打包

    tar czf  pm2.tar.gz  pm2/

最后传到需要安装pm2的机器

然后去相同的目录解压

    sudo  tar  xf  pm2.tar.gz 

设置环境变量

      sudo vim /etc/profile
          export PM2_HOME=/usr/lib/node_modules/pm2
          export PATH=$PM2_HOME/bin:$PATH

生效环境变量

    source  /etc/profile

由于pm2这个命令在pm2/bin/下面 所以设置个软连接 方便使用

     sudo ln -s /usr/lib/node_modules/pm2/bin/pm2 /usr/bin/pm2

执行试下是否报错 

    pm2 list 

我这里是遇到了权限的问题给下用户权限就可以了

    sudo chown  sgsm.sgsm   pm2 -R

至此pm2 成功安装


最后最后最后最后最后最后  经过神秘大佬的指点

    sudo  npm -g install  pm2@版本号

可以安装指定的低版本pm2

写完这篇文档就GG

等下 还有几个npm的常用命令分享

    npm -v #显示版本，检查npm 是否正确安装。

    npm install express #安装express模块

    npm install -g express #全局安装express模块

    npm list #列出已安装模块

    npm show express #显示模块详情

    npm update #升级当前目录下的项目的所有模块

    npm update express #升级当前目录下的项目的指定模块

    npm update -g express #升级全局安装的express模块

    npm uninstall express #删除指定的模块
