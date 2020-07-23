---
title: python基础之字典
tags:
  - python
  - linux
categories: 开发
date: 2020-06-14 11:10:59
---
## 字典是一个无序的数据集合
使用print函数打印字典的时候顺序会和定义的有差别
```python
xinlong = {"name" : "xinlong",
           "age" : 22,
           "xingbie" : "True",
           "height" : "180",
           "weight" : "130"
}
print(xinlong)
# 打印weight
print(xinlong["weight"])
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    {'name': 'xinlong', 'age': 22, 'xingbie': 'True', 'height': '180', 'weight': '130'}
    130

    进程已结束，退出代码 0

## get()方法语法：
```python
dict.get(key, default=None)

参数：
    key -- 字典中要查找的键。
    default -- 如果指定键的值不存在时，返回该默认值值。
```
```python
info = {'name':'班长', 'id':100, 'sex':'f', 'address':'北京'}
age = info.get('id')
print(age)
num = info.get("aaa")
print(num)
list = info.get("aaa","test")
print(list)
```
运行结果：

        D:\软件下载\python.exe E:/资料/python/LeetCode/1544.py
        100
        None
        test

        进程已结束，退出代码 0

## 字典的基本使用-增加
```python
xinlong = {"name" : "xin"}
print(xinlong)
xinlong["age"]="22"
print(xinlong)
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    {'name': 'xin'}
    {'name': 'xin', 'age': '22'}

    进程已结束，退出代码 0

## 字典的基本使用-修改
如果key不存在 就会增加一个键值对  如果存在就会修改已存在的键值对
```python
xinlong = {"name" : "xin"}
print(xinlong)
xinlong["name"]="xiaoxiao"
print(xinlong)
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    {'name': 'xin'}
    {'name': 'xiaoxiao'}

    进程已结束，退出代码 0

## 字典的基本使用-删除
再删除指定键值对的时候如果不存在则报错
```python
xinlong = {"name" : "xin",
           "age" : 22}
print(xinlong)
xinlong.pop("age")
print(xinlong)
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    {'name': 'xin', 'age': 22}
    {'name': 'xin'}

    进程已结束，退出代码 0

## 统计键值对数量
```python
xinlong = {"name" : "xin",
           "age" : 22}
lens = len(xinlong)
print(lens)
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    2

    进程已结束，退出代码 0

## 合并字典
如果被合并的字典中包含已存在的键值对,会覆盖原有的键值对
```python
xinlong = {"name" : "xin",
           "age" : 22}
mihua = {"server" : 1,
         "name" : "kingdom",
         "start" : "num"}
xinlong.update(mihua)
print(xinlong)
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    {'name': 'kingdom', 'age': 22, 'server': 1, 'start': 'num'}

    进程已结束，退出代码 0

## 清空字典
```python
mihua = {"server" : 1,
         "name" : "kingdom",
         "start" : "num"}

mihua.clear()
print(mihua)
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    {}

    进程已结束，退出代码 0

## 字典的循环遍历
```python
mihua = {"server" : 1,
         "name" : "kingdom",
         "start" : "num"}

for k in  mihua:
    print("%s - %s" % (k,mihua[k]))
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    server - 1
    name - kingdom
    start - num

    进程已结束，退出代码 0

```python
list = [
    {"name" : "xin",
           "age" : 22
    },
    {"name" : "long",
           "age" : 21
    }
]

for  list_info  in  list:
    print(list_info)
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    {'name': 'xin', 'age': 22}
    {'name': 'long', 'age': 21}

    进程已结束，退出代码 0