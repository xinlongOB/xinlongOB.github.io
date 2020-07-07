---
title: Python基础之元祖
tags:
  - python
  - linux
categories: 开发
date: 2020-06-14 08:38:18
---
## 定义元祖
快速查看元祖提供哪些操作   定义一个元祖   然后元祖变量名+.  例如  info = ()        info.  然后TABLE补全
```python
info_tuple = ("zhangsan",18,180)
##打印元祖中的第二个数据
print(info_tuple[1])
```
运行结果：

        D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
        18

        进程已结束，退出代码 0

## 定义一个空元祖
```python
sing_tuple = ()
```

定义一个只有一个元素的元祖(元素后面需要加"," 不然会识别为int型)
```python
i_tuple = (12)
set_tuple = (12,)
itype = type(i_tuple)
set_type = type(set_tuple)
print("此时元祖i_tuple 的类型是 %s" % itype)
print("此时元祖set_tuple 的类型是 %s" % set_type)
```
运行结果：

        D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
        此时元祖i_tuple 的类型是 <class 'int'>
        此时元祖set_tuple 的类型是 <class 'tuple'>

        进程已结束，退出代码 0

## 元祖的基本操作
```python
table = ("zhang","liang",180,18,"liang")
# 取值
print(table[1])
# 已知数据内容取出数据在元祖中的索引(从前往后取)
print(table.index("liang"))
# 统计数据个数
print(table.count("liang"))
# 统计元祖中包含元素的个数
lens = len(table)
print("当前元祖包含 %d " % lens)
```
运行结果：

        D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
        liang
        1
        2
        当前元祖包含 5 

        进程已结束，退出代码 0

## 格式化字符串后面的"()"本身就是元祖
```python
str = ("bad",21,1.85)
print("%s 的年龄是 %d 身高是%.2f" % ("bad",21,1.85))
print("%s 的年龄是 %d 身高是%.2f" % str)
```
运行结果：

        D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
        bad 的年龄是 21 身高是1.85
        bad 的年龄是 21 身高是1.85

        进程已结束，退出代码 0

## 列表和元祖之前的转换
```python
name_list = ["xinlong","long","hello"]
#当前name_list是个列表如果不希望别人修改列表可以转换成元祖，使用tuple这个函数
print(type(name_list))
# name_list 列表转换为元祖后，需要重新定义一个变量名
modiy = tuple(name_list)
# 打印转换后的类型
print(type(modiy))
print(modiy)
```
运行结果：

        D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
        <class 'list'>
        <class 'tuple'>
        ('xinlong', 'long', 'hello')

        进程已结束，退出代码 0

## 修改元祖数据的内容
如果想要修改元组数据的内容 使用list函数转为列表
```python
name_list = ("xinlong","long","hello")
print(type(name_list))
# 元祖转换为列表
remod = list(name_list)
print(type(remod))
# 修改列表内容
remod.append("test")
print(remod)
```
运行结果：

        D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
        <class 'tuple'>
        <class 'list'>
        ['xinlong', 'long', 'hello', 'test']

        进程已结束，退出代码 0

## 互换变量值
```python
a = 6
b = 100
# 解法1：使用变量
c = a
a = b
b = c
# 解法2：不使用其他变量
a = a + b
b = a - b
a = a - b
# 解法3：python专有写法  a 结束b的元素  b接受a的元素
a,b = (b,a)
# 提示等号右边是一个元祖，只是把小括号省略了
# a,b = b,a
print(a)
print(b)
```
运行结果：

        D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
        100
        6

        进程已结束，退出代码 0

