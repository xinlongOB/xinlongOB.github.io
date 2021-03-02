---
title: vsftpd：500 OOPS: vsftpd: refusing to run with writable root inside chroot ()错误的解决方法
tags:
  - vsftpd
  - linux
categories: 运维
date: 2021-03-02 18:20:49
---
## 报错
当我们限定了用户不能跳出其主目录之后，使用该用户登录FTP时往往会遇到这个错误：
```bash
500 OOPS: vsftpd: refusing to run with writable root inside chroot ()
```


## 解决办法
从2.3.5之后，vsftpd增强了安全检查，如果用户被限定在了其主目录下，则该用户的主目录不能再具有写权限了！如果检查发现还有写权限，就会报该错误。

 要修复这个错误，可以用命令chmod a-w /home/user去除用户主目录的写权限，注意把目录替换成你自己的。或者你可以在vsftpd的配置文件中增加下列两项中的一项：
```bash
allow_writeable_chroot=YES 
```