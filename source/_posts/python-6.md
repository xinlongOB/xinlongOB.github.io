---
title: python基础之变量
tags:
  - python
  - Linux
categories: 开发
date: 2020-06-14 11:45:15
---
在函数中定义的变量,不能在其他地方引用
```python
def demo():
    num = 21
    print(num)

if __name__ ==  "__main__":
    demo()
# 在函数外面引用报错
print(num)
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    21
    Traceback (most recent call last):
      File "E:/程序代码/hexo/test.py", line 9, in <module>
        print(num)
    NameError: name 'num' is not defined

    进程已结束，退出代码 1

## 全局变量
```python
num = 100
def demo1():
    print("demo1 ==> %d" % num)

def demo2():
    print("demo2 ==> %d" % num)

demo1()
demo2()
print("直接调用的输出为 %d" % num)
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    demo1 ==> 100
    demo2 ==> 100
    直接调用的输出为 100

    进程已结束，退出代码 0

```python
num = 100
def demo1():
    print("demo1 ==> %d" % num)

def demo2():
    num = 20
    print("demo2 ==> %d" % num)
demo1()
demo2()
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    demo1 ==> 100
    demo2 ==> 20

    进程已结束，退出代码 0