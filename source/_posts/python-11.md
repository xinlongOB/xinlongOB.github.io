---
title: python基础之for循环
tags:
  - python
  - liunx
categories: 开发
date: 2020-06-17 17:12:31
---
## for循环语句
python中for循环可以遍历任何序列的项目,如一个列表或者一个字符串,语法：
```python
for name i list:
      print(name)
```
示例:
```python
for letter in "python":
    print("当前字母 %s" % letter)

list = ["banana","apple","mango"]
for  fruit in list:
    print("当前水果 %s" % fruit)

print("Good bye")
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    当前字母 p
    当前字母 y
    当前字母 t
    当前字母 h
    当前字母 o
    当前字母 n
    当前水果 banana
    当前水果 apple
    当前水果 mango
    Good bye

    进程已结束，退出代码 0

## 通过序列索引迭代
```python

list = ["banana","apple","mango"]
for  fruit in range(len(list)):
    print("当前水果 %s" % list[fruit])

print("Good bye")
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    当前水果 banana
    当前水果 apple
    当前水果 mango
    Good bye

    进程已结束，退出代码 0

以上实例我们使用了内置函数 len() 和 range(),函数 len() 返回列表的长度，即元素的个数。 range返回一个序列的数。

## 循环使用else语句
```python

for i in range(2,20):
    if i%2 == 0:
        print("%d 是一个双数" % i)
    else:
        print("%d 是一个单数" % i)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    2 是一个双数
    3 是一个单数
    4 是一个双数
    5 是一个单数
    6 是一个双数
    7 是一个单数
    8 是一个双数
    9 是一个单数
    10 是一个双数
    11 是一个单数
    12 是一个双数
    13 是一个单数
    14 是一个双数
    15 是一个单数
    16 是一个双数
    17 是一个单数
    18 是一个双数
    19 是一个单数

    进程已结束，退出代码 0

## 跳出当前循环和退出循环
break退出循环
```python
for letter in "python":
    if letter == "h":
        break
    print("当前字母 %s" % letter)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    当前字母 p
    当前字母 y
    当前字母 t

    进程已结束，退出代码 0

continue跳出当前循环
```python
for letter in "python":
    if letter == "h":
        continue
    print("当前字母 %s" % letter)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    当前字母 p
    当前字母 y
    当前字母 t
    当前字母 o
    当前字母 n

    进程已结束，退出代码 0