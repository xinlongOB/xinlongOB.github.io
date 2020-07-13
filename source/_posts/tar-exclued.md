---
title: tar打包时排除或忽略某个子目录或文件
tags:
  - tar
  - linux
date: 2020-07-13 15:21:09
---
比如你想打包/home这个目录，但是/home/test/目录和/home/www/index.php文件你都不想打包，方法是：
```bash
tar -zcvf home.tar.gz   /home --exclude=/home/test   --exclude=/home/www/index.php
```
语法：
```bash
tar -zcvf xxx.tar.gz   要打包的目录  --exclude=dir1   --exclude=file1  ......
```
注意：
```bash
1、--exclude=file1 而不是 --exclude file1
2、要排除一个目录是 --exclude=dir1,而不是 --exclude=dir1/
    一定要注意排除目录的最后不要带"/",否则exclude目录将不起作用
3、压缩目录和排除目录都需要采用同样的格式,如都采用绝对路径或者相对路径
```