---
title: centos下清除记录的svn用户名和密码
tags:
  - subversion
  linux
categories: 运维
date: 2020-05-26 17:05:01
---
## centos下清除记录的svn用户名和密码
<br/>由于公司人员的变动，离职人员的svn账号也会被删除，使用之前账号检出的代码执行svn update的时候会显示报错<br/>

    svn: E210005: Unable to connect to a repository at URL 'svn://xxx'
    svn: E210005: No repository found in 'svn://xxx'

是因为检出代码的是提示是否保存明文密码然后用户选择的yes


<br/>解决办法<br/>
linux下删除~/.subversion/auth即可清除之前的用户名和密码：

    rm -rf ~/.subversion/auth

以后再操作svn会提示你输入root密码以及svn用户名和密码，这样就可以输入正常的账号密码了