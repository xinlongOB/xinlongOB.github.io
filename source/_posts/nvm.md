---
title: CentOS通过nvm安装管理node版本
tags:
  - nvm
  - linux
categories: 运维
date: 2021-03-10 16:07:58
---
## 安装nvm
```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash

 或者

 wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
```
立即生效
```bash
source ~/.bashrc 
```
## 命令相关
```bash
# 查看所有可安装的node版本号
nvm ls-remote
#  安装13.0.0版本的node
nvm install 13.0.0
# 安装15系列中最高版本的node  比如15系列有15.0.0,　　15.0.1,　　15.1.1,　　15.1.2,　　15.2.1,　　15.3.0,那么最后安装的就是15.3.0
nvm install 15
# 安装14.2系列中最高版本的node
nvm install 14.2
# 查看已安装的所有node版本以及默认的版本
nvm list
# 使用13.0.0版本的node
nvm use 13.0.0
# 使用14.2系列的最高版本node
nvm use 14.2
# 卸载13.0.0版本的node
nvm uninstall 13.0.0 
```

## 安装过程中的报错
```bash
[sgsm@iZ2ze9k2rrtcy962g9wiyrZ server]$ nvm install  v14.15.4
v14.15.4 is already installed.
Error: ENOENT: no such file or directory, uv_cwd
    at process.wrappedCwd (internal/bootstrap/switches/does_own_process_state.js:129:28)
    at process.cwd (/home/sgsm/.nvm/versions/node/v14.15.4/lib/node_modules/npm/node_modules/graceful-fs/polyfills.js:10:19)
    at Conf.loadPrefix (/home/sgsm/.nvm/versions/node/v14.15.4/lib/node_modules/npm/lib/config/load-prefix.js:46:24)
    at load_ (/home/sgsm/.nvm/versions/node/v14.15.4/lib/node_modules/npm/lib/config/core.js:109:8)
    at Conf.<anonymous> (/home/sgsm/.nvm/versions/node/v14.15.4/lib/node_modules/npm/lib/config/core.js:96:5)
    at Conf.emit (events.js:315:20)
    at ConfigChain._resolve (/home/sgsm/.nvm/versions/node/v14.15.4/lib/node_modules/npm/node_modules/config-chain/index.js:281:34)
    at ConfigChain.add (/home/sgsm/.nvm/versions/node/v14.15.4/lib/node_modules/npm/node_modules/config-chain/index.js:259:10)
    at Conf.add (/home/sgsm/.nvm/versions/node/v14.15.4/lib/node_modules/npm/lib/config/core.js:338:27)
    at Conf.<anonymous> (/home/sgsm/.nvm/versions/node/v14.15.4/lib/node_modules/npm/lib/config/core.js:314:25)
internal/bootstrap/switches/does_own_process_state.js:129
    cachedCwd = rawMethods.cwd();
                           ^

Error: ENOENT: no such file or directory, uv_cwd
    at process.wrappedCwd (internal/bootstrap/switches/does_own_process_state.js:129:28)
    at process.cwd (/home/sgsm/.nvm/versions/node/v14.15.4/lib/node_modules/npm/node_modules/graceful-fs/polyfills.js:10:19)
    at process.errorHandler (/home/sgsm/.nvm/versions/node/v14.15.4/lib/node_modules/npm/lib/utils/error-handler.js:183:30)
    at process.emit (events.js:315:20)
    at process._fatalException (internal/process/execution.js:163:25) {
  errno: -2,
  code: 'ENOENT',
  syscall: 'uv_cwd'
}
nvm is not compatible with the npm config "prefix" option: currently set to ""
Run `npm config delete prefix` or `nvm use --delete-prefix v14.15.4` to unset it.
Creating default alias: default -> v14.15.4
```
安装v14.15.4版本的时候 报错 nvm与npm的配置文件不兼容
执行一下命令
```bash
npm config delete prefix
npm config set prefix $NVM_DIR/versions/node/v14.15.4
```
之后在安装就可以了   注意：不能再node进程的目录中执行上面命令


## 升级pm2
```bash
npm  install pm2@4.5.5  -g
```
安装之后关闭窗口 新建窗口执行 pm2 list 会提示
```bash
[sgsm@iZ2zebqwvcwhckgto4m1afZ ~]$ pm2 list

>>>> In-memory PM2 is out-of-date, do:
>>>> $ pm2 update
In memory PM2 version: 2.8.0
Local PM2 version: 4.5.5
```
这个时候执行下 pm2  update     在执行pm2 list 就正常了 


## 多个版本设置默认node版本
```bash
[sgsm@iZ2ze9k2rrtcy962g9wiyrZ ~]$ nvm  ls
         v4.4.4
->       system
default -> v14.15.4 (-> N/A)
node -> stable (-> v4.4.4) (default)
stable -> 4.4 (-> v4.4.4) (default)
iojs -> N/A (default)
lts/* -> lts/fermium (-> N/A)
lts/argon -> v4.9.1 (-> N/A)
lts/boron -> v6.17.1 (-> N/A)
lts/carbon -> v8.17.0 (-> N/A)
lts/dubnium -> v10.24.1 (-> N/A)
lts/erbium -> v12.22.1 (-> N/A)
lts/fermium -> v14.17.1 (-> N/A)
[sgsm@iZ2ze9k2rrtcy962g9wiyrZ ~]$ 
[sgsm@iZ2ze9k2rrtcy962g9wiyrZ ~]$ nvm  alias default  v4.4.4
default -> v4.4.4

[sgsm@iZ2ze9k2rrtcy962g9wiyrZ ~]$ nvm  ls
->       v4.4.4
       v14.15.4
         system
default -> v4.4.4
node -> stable (-> v14.15.4) (default)
stable -> 14.15 (-> v14.15.4) (default)
iojs -> N/A (default)
lts/* -> lts/fermium (-> N/A)
lts/argon -> v4.9.1 (-> N/A)
lts/boron -> v6.17.1 (-> N/A)
lts/carbon -> v8.17.0 (-> N/A)
lts/dubnium -> v10.24.1 (-> N/A)
lts/erbium -> v12.22.1 (-> N/A)
lts/fermium -> v14.17.1 (-> N/A)
```