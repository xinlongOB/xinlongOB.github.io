---
title: python基础之while循环
tags:
  - python
  - Linux
categories: 开发
date: 2020-06-17 16:32:05
---
## while循环语句
python编程中while用于循环执行程序,即在某条件下,循环执行某段程序,以处理需要重复处理的相同任务,格式为：
```python
while 判断条件:
    执行语句
```
执行语句可以是单个语句或语句块。判断条件可以是任何表达式，任何非零、或非空（null）的值均为true。当判断条件假 false 时，循环结束。
```python
count = 0
while count < 9:
    print("the count is %d" % count)
    count += 1
print("Good bye !")
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    the count is 0
    the count is 1
    the count is 2
    the count is 3
    the count is 4
    the count is 5
    the count is 6
    the count is 7
    the count is 8
    Good bye !

    进程已结束，退出代码 0

while 语句时还有另外两个重要的命令 continue，break 来跳过循环，continue 用于跳过该次循环，break 则是用于退出循环，此外"判断条件"还可以是个常值，表示循环必定成立，具体用法如下：
```python
# 打印1-10所有的双数
i = 1     # 定义变量
while i < 10:   # while判断条件 如果 i 小于10 就执行
    i += 1      # 计数器 i + 1 
    if i%2 > 0:    # 判断如果 i 除以 2 余数 大于 0 就退出
        continue    
    print(i,end=",")   #  打印所有双数  以","分割   end="" 代表不换行
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    2,4,6,8,10,
    进程已结束，退出代码 0

无限循环
```python
# 无限循环
while True:
    print("hello")
```

## 循环使用else语句
在 python 中，while … else 在循环条件为 false 时执行 else 语句块：
```python
count = 0
while count < 5:
    print("%d 小于 5" % count)
    count += 1
else:
    print("%d 大于等于 5" % count)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    0 小于 5
    1 小于 5
    2 小于 5
    3 小于 5
    4 小于 5
    5 大于等于 5

    进程已结束，退出代码 0

## 循环练习
```python
import random
	
	num = random.randint(1, 10)
	counter = 0
	
	while counter < 5:
	    answer = int(input('guess the number: '))
	    if answer > num:
	        print('猜大了')
	    elif answer < num:
	        print('猜小了')
	    else:
	        print('猜对了')
	        break
	    counter += 1
	else:  # 循环被break就不执行了，没有被break才执行
	    print('the number is:', num)
```