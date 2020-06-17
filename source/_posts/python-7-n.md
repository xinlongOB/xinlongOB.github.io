---
title: python基础之变量类型
tags:
  - python
  - liunx
categories: 开发
date: 2020-06-16 14:20:24
---
## python变量类型
变量存储在内存中的值,这就意味着在创建变量时会在内存中开辟一个空间。基于变量的数据类型,解释器会分配给指定内存，并决定什么数据可以被存储在内存中,因此,变量可以指定不同的数据类型,这些变量可以存储整数、小数或字符
## 变量赋值
python中变量赋值不需要类型声明
<br/>每个变量在内存中创建,都包括变量的标识,名称和数据这些信息<br/>
每个变量在使用前都必须赋值,变量赋值以后该变量才会被创建
<br/>等号(=)用来给变量赋值<br/>
等号(=)运算符左边是一个变量名,等号(=)运算符右边是存储在变量中的值,例如：

```python
counter = 100  # 整数
miles = 1000.00   # 浮点数
name = "john"   # 字符串
print(counter)
print(miles)
print(name)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    100
    1000.0
    john

    进程已结束，退出代码 0

## 多个变量赋值
python允许你同时为多个变量赋值,例如：
```python
a = b = c = 1
# 也可以为多个对象指定多个变量
o, p , q = 100, 200, "name"
print(a)
print(b)
print(c)
print(o)
print(p)
print(q)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    1
    1
    1
    100
    200
    name

    进程已结束，退出代码 0

## 标准数据类型
在内存中存储的数据可以有多种类型
<br/>例如：一个年龄可以用数字来存储,他的名字可以用字符串来存储<br/>
python定义了一些标准类型,用于存储各种类型的数据
<br/>python有五个标准的数据类型：<br/>

    Numbers(数字)
    String(字符串)
    List(列表)
    Tuple(元祖)
    Dictionary(字典)
