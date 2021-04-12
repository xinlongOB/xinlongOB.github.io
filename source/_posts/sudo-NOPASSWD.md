---
title: CentOS下使用免密使用sudo命令
tags:
  - sudo
  - linux
categories: 运维
date: 2021-04-12 16:17:04
---
配置文件
```bash
## Allow root to run any commands anywhere 
root    ALL=(ALL)       ALL

## Allows members of the 'sys' group to run networking, software, 
## service management apps and more.
# %sys ALL = NETWORKING, SOFTWARE, SERVICES, STORAGE, DELEGATING, PROCESSES, LOCATE, DRIVERS

## Allows people in group wheel to run all commands
%wheel  ALL=(ALL)       ALL

## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

## Allows members of the users group to mount and unmount the 
## cdrom as root
# %users  ALL=/sbin/mount /mnt/cdrom, /sbin/umount /mnt/cdrom

## Allows members of the users group to shutdown this system
# %users  localhost=/sbin/shutdown -h now

## Read drop-in files from /etc/sudoers.d (the # here does not mean a comment)
#includedir /etc/sudoers.d
```
上面配置中  %wheel  ALL=(ALL)       ALL  代表wheel组里面所有人可以使用所有命令

完整配置
```bash
[root@ipason ~]# groupadd   test
[root@ipason ~]# useradd   -G  test  name1
[root@ipason ~]# id name1
uid=501(name1) gid=502(name1) groups=502(name1),501(test)

vim  /etc/sudoers
    # %test  ALL=(ALL)       ALL
    %test  ALL=(ALL)       NOPASSWD: ALL

```
如果也可以在 /etc/sudoers.d 下面新建文件
```bash
vim   test_sudoers 
    %test  ALL=(ALL)       NOPASSWD: ALL
```