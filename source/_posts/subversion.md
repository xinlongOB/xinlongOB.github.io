---
title: SVN 更新报错
tags:
  - subversion
  - linux
categories: 运维
date: 2020-11-30 19:02:20
---
[linux@*****-04 webapps]$ svn up
Updating '.':
Skipped 'template' -- Node remains in conflict
At revision 133.
Summary of conflicts:
  Skipped paths: 1


解决方法：
```bash
svn revert --depth=infinity template
```