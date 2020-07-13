---
title: hexo的ayer主题之添加在线聊天功能daovoice
tags:
  - hexo
  - linux
date: 2020-07-11 15:16:13
---
## daovoice官网配置
1、进入daovoice官网注册
2、注册成功后进入控制台页面，点击【应用设置】-【安装到网站】，也可以在进入控制台页面后直接点击红色框内的【点击接入】，会出现如下界面
![](../1.png)

## hexo博客的文件配置
在ayer主题下的文件找到head.ejs文件,路径在themes/hexo-themes-ayer/layout/_partial/下并配置此文件
![](../2.png)
```java
  <script>(function(i,s,o,g,r,a,m){i["DaoVoiceObject"]=r;i[r]=i[r]||function(){(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;a.charset="utf-8";m.parentNode.insertBefore(a,m)})(window,document,"script",('https:' == document.location.protocol ? 'https:' : 'http:') + "//widget.daovoice.io/widget/xxxxxx.js","daovoice")
  daovoice('init', {
  app_id: "xxxxxx",
  });
  daovoice('update');
  </script>

\\ 把xxxxxx 替换为 你注册daovoice的app_id 
```
在主题的配置文件_config.yml 中添加
```yaml
# Online contact
daovoice: true  
daovoice_app_id: "xxxxxx"
```
填完后保存,刷新daovoice页面会显示
![](../3.png)
博客的右下角会显示一个会话图标
![](../44.png)
点击就可以进行会话
![](../5.png)