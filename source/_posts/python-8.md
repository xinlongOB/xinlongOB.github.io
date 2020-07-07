---
title: python基础之条件语句
tags:
  - python
  - linux
categories: 开发
date: 2020-06-16 15:31:13
---
## python条件语句
python条件语句是通过一条或多条语句的执行结果(True或False)来决定的代码块
<br/>Python 编程中 if 语句用于控制程序的执行，基本形式为：<br/>

```python
if 判断条件:
    执行语句...
else:
    执行语句...
```
例如：
```python
# 例1：if 基本用法
 
flag = False
name = 'luren'
if name == 'python':         # 判断变量是否为 python 
    flag = True              # 条件成立时设置标志为真
    print 'welcome boss'     # 并输出欢迎信息
else:
    print name               # 条件不成立时输出变量名称
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    luren

    进程已结束，退出代码 0

if 语句的判断条件可以用>（大于）、<(小于)、==（等于）、>=（大于等于）、<=（小于等于）来表示其关系。当判断条件为多个值时，可以使用以下形式：
```python
if 判断条件1:
    执行语句1……
elif 判断条件2:
    执行语句2……
elif 判断条件3:
    执行语句3……
else:
    执行语句4……
```
例如：
```python
# 例2：elif用法
num = 5
if num == 3:  # 判断num的值
    print("boss")
elif num == 2:
    print("user")
elif num == 1:
    print("worker")
elif num < 0:  # 值小于零时输出
    print("error")
else:
    print("roadman") # 条件均不成立时输出
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    roadman

    进程已结束，退出代码 0

## 判断多个条件可以使用or或者and
如果判断需要多个条件需同时判断时，可以使用 or （或），表示两个条件有一个成立时判断条件成功；使用 and （与）时，表示只有两个条件同时成立的情况下，判断条件才成功。
```python
# 例3：if语句多个条件

num = 9
if num >= 0 and num <= 10:       # 判断值是否在0-10之间
    print("hello")

num = 10
if num < 0 or num > 10:     # 判断值师傅小于0或大于10
    print("hello")
else:
    print("undefined")


num = 8
# 判断值是否在0-5或者10-15之间
if (num >= 0 and num <= 5) or (num >= 10 and num <= 15):
    print("hello")
else:
    print("undefined")
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    hello
    undefined
    undefined

    进程已结束，退出代码 0

## 判断练习
练习1
```python
username = input('username: ')
# getpass模块中，有一个方法也叫getpass
password = input('password: ')

if username == 'bob' and password == '123456':
    print('Login successful')
else:
    print('Login incorrect')

```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    username: aa
    password: aa
    Login incorrect

    进程已结束，退出代码 0
练习2
```python
score = int(input('分数: '))

if score >= 60 and score < 70:
    print('及格')
elif 70 <= score < 80:
    print('良')
elif 80 <= score < 90:
    print('好')
elif score >= 90:
    print('优秀')
else:
    print('你要努力了')
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    分数: 100
    优秀

    进程已结束，退出代码 0

练习3
```python

import random
	
	all_choices = ['石头', '剪刀', '布']
	computer = random.choice(all_choices)
	player = input('请出拳: ')
	
	# print('Your choice:', player, "Computer's choice:", computer)
	print("Your choice: %s, Computer's choice: %s" % (player, computer))
	if player == '石头':
	    if computer == '石头':
	        print('平局')
	    elif computer == '剪刀':
	        print('You WIN!!!')
	    else:
	        print('You LOSE!!!')
	elif player == '剪刀':
	    if computer == '石头':
	        print('You LOSE!!!')
	    elif computer == '剪刀':
	        print('平局')
	    else:
	        print('You WIN!!!')
	else:
	    if computer == '石头':
	        print('You WIN!!!')
	    elif computer == '剪刀':
	        print('You LOSE!!!')
	    else:
	        print('平局')
```