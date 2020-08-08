---
title: Python报"TypeError:abytes-likeobjectisrequired,not'str'"解决办法
tags:
  - python
  - linux
categories: 开发
date: 2020-08-06 13:42:18
---
解决办法非常的简单，只需要用上python的bytes和str两种类型转换的函数encode()、decode()即可！

str通过encode()方法可以编码为指定的bytes；

反过来，如果我们从网络或磁盘上读取了字节流，那么读到的数据就是bytes。要把bytes变为str，就需要用decode()方法；

例：
```python
str = 'this is test'

str = str.encode()
```
