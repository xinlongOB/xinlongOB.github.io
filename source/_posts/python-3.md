---
title: Python基础之列表
tags:
  - python
  linux
categories: 开发
date: 2020-04-23 10:38:18
---
## 定义一个列表
```python
name_list = ["zhangsan","lisi","wangwu","wangxiaoer","wangwu","wangxiaoer"]
num_list = ["1","3","2","0"]
```
## 增加列表内的指定参数索引和参数
```python
name_list = ["zhangsan","lisi","wangwu","wangxiaoer","wangwu","wangxiaoer"]
name_list.insert(0,"long")
print(name_list)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    ['long', 'zhangsan', 'lisi', 'wangwu', 'wangxiaoer', 'wangwu', 'wangxiaoer']

    进程已结束，退出代码 0

## 增加另一个列表内容到此列表中
```python
name_list = ["zhangsan","lisi","wangwu","wangxiaoer","wangwu","wangxiaoer"]
num_list = ["1","2","3"]
name_list.extend(num_list)
print(name_list)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    ['zhangsan', 'lisi', 'wangwu', 'wangxiaoer', 'wangwu', 'wangxiaoer', '1', '2', '3']

    进程已结束，退出代码 0

## 增加一个参数到列表的尾部
```python
name_list = ["zhangsan","lisi","wangwu","wangxiaoer","wangwu","wangxiaoer"]
name_list.append("long")
print(name_list)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    ['zhangsan', 'lisi', 'wangwu', 'wangxiaoer', 'wangwu', 'wangxiaoer', 'long']

    进程已结束，退出代码 0

## 删除列表内指定的参数
```python
name_list = ["zhangsan","lisi","wangwu","wangxiaoer","wangwu","wangxiaoer"]
name_list.remove("lisi")
print(name_list)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    ['zhangsan', 'wangwu', 'wangxiaoer', 'wangwu', 'wangxiaoer']

    进程已结束，退出代码 0

## 删除列表内最后一个参数
```python
name_list = ["zhangsan","lisi","wangwu","wangxiaoer","wangwu","wangxiaoer"]
name_list.pop()
print(name_list)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    ['zhangsan', 'lisi', 'wangwu', 'wangxiaoer', 'wangwu']

    进程已结束，退出代码 0

## 修改列表内的参数
```python
name_list = ["zhangsan","lisi","wangwu","wangxiaoer","wangwu","wangxiaoer"]
name_list[0] = "xinxin"
print(name_list)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    ['xinxin', 'lisi', 'wangwu', 'wangxiaoer', 'wangwu', 'wangxiaoer']

    进程已结束，退出代码 0

## 清空列表中的数据
```python
name_list = ["zhangsan","lisi","wangwu","wangxiaoer","wangwu","wangxiaoer"]
name_list.clear()
print(name_list)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    []

    进程已结束，退出代码 0

## 统计列表长度
```python
name_list = ["zhangsan","lisi","wangwu","wangxiaoer","wangwu","wangxiaoer"]
lenght = len(name_list)
print(lenght)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    6

    进程已结束，退出代码 0

## 统计数据在列表中出现的次数
```python
name_list = ["zhangsan","lisi","wangwu","wangxiaoer","wangwu","wangxiaoer"]
num = name_list.count("wangxiaoer")
print(num)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    2

    进程已结束，退出代码 0

## 列表中数据排序
```python
name_list = ["CC","BB","AA","DD","WW","YY"]
num_list = ["1","3","2","0"]
print(num_list)   # 排序前打印
print(name_list)  # 排序前打印
num_list.sort()   # 排序
name_list.sort()  # 排序
print(num_list)   # 排序后打印
print(name_list)   # 排序后打印
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    ['1', '3', '2', '0']          #  排序前打印
    ['CC', 'BB', 'AA', 'DD', 'WW', 'YY']    # 排序前打印
    ['0', '1', '2', '3']         # 排序后打印
    ['AA', 'BB', 'CC', 'DD', 'WW', 'YY']         # 排序后打印

    进程已结束，退出代码 0

## 打印列表中的数据
```python
alist = [10, 20, 30, 'bob', 'alice', [1,2,3]]
# 打印最后一项
print(alist[-1])
# 因为最后一列是列表,列表还可以继续取下标
print(alist[-1][-1])
# 10 是否在列表中
print(10 in alist)
# 字符 "qq" 是否在列表中
print("qq" in alist)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    [1, 2, 3]
    3
    True
    False

    进程已结束，退出代码 0