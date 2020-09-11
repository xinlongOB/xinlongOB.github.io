---
title: ansible报错
tags:
  - ansible
  - linux
categories: 运维
date: 2020-09-05 14:25:22
---
![](../1.png)
原因：python库中urllib3 (1.22) or chardet (2.2.1) 的版本不兼容 解决如下：

```bash
[root@iZ2zebighqoqoun3zojd9qZ~]$ sudo pip uninstall urllib3

[root@iZ2zebighqoqoun3zojd9qZ~]$ sudo pip uninstall chardet

[root@iZ2zebighqoqoun3zojd9qZ~]$ sudo pip install requests

```