---
title: Git无法添加主题文件夹
tags:
  - git
  - linux
categories: 运维
date: 2020-05-17 17:07:14
---
由于主题都是在git上下载的所以默认会有一个.git的文件，这样导致提交的时候无法提交主题文件
<br/>解决办法<br/>

删除主题文件夹下.git

    git rm --cached themes/hexo-theme-ayer
    git add .
    git commit -m "xxx"
    git push origin master