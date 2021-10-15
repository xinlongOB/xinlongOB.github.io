---
title: jenkins报错解决
tags:
  - jenkins
  - linux
categories: 运维
date: 2020-10-22 15:17:19
---
## 首次访问jenkins显示离线
修改配置文件
```bash
/var/lib/jenkins/hudson.model.UpdateCenter.xml 

<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>https://updates.jenkins.io/update-center.json</url>
  </site>
</sites>
```bash
修改为
```bash
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>http://mirror.esuni.jp/jenkins/updates/update-center.json</url>
  </site>
</sites>
```

##  No such plugin: cloudbees-folder
jenkins 安装出现一个错误： No such plugin: cloudbees-folder

上面的错误显示是，安装插件cloudbees-folder失败，是因bai为下载的Jenkins.war里没有cloudbees-folder插件

需要在网上下载：https://updates.jenkins-ci.org/download/plugins/cloudbees-folder/


下载cloudbees-folder.hpi放在/var/cache/jenkins/war/WEB-INF/detached-plugins即可
重启jenkins，浏览器访问Jenkins服务器，安装插件并设置用户名、密码等，然后进入Jenkins首页 





## jenkins汉化
jenkins转换显示语言为中文简体
主界面-->系统管理-->插件管理-->可选插件
安装插件locale plugin
系统管理-->系统设置-->Locale
填入：zh_CN
应用保存

如果以上方法生效了，恭喜你，不用向下看了。不过有些版本不生效，那我们继续：

安装汉化包 
Localization: Chinese (Simplified)
Localization Support


## 无法安装插件
更换jdk版本
```bash
 yum install   java  java-devel   -y
```
或者更改源
```bash
http://updates.jenkins.io/update-center.json
```