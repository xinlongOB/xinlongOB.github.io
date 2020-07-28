---
title: nodejs编译安装
tags:
  - nodejs
  - linux
categories: 运维
date: 2020-07-28 17:21:09
---
```bash
sudo  wget https://nodejs.org/dist/v4.4.4/node-v4.4.4-linux-x64.tar.xz

sudo tar xf  node-v* -C /usr/local    

# 配置环境变量
sudo vim /etc/profile
    export NODE_HOME=/usr/local/node-v4.4.4-linux-x64
		export PATH=$NODE_HOME/bin:$PATH
		export NODE_PATH=$NODE_HOME/lib/node_modules:$PATH

# 立即生效
source  /etc/profile 

# 查看版本
node -v
```