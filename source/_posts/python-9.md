---
title: python基础之基本运算
tags:
  - python
  linux
categories: 开发
date: 2020-06-16 16:08:51
---
## Python语言支持以下类型的运算符:

    算术运算符
    比较运算符
    赋值运算符
    逻辑运算符
    位运算符
    成员运算符
    身份运算符
    运算符优先级

## python算术运算符
以下假设变量： a = 10, b = 20:
```python
运算符	      描述                                                	实例
+        加 - 两个对象相加                                       a + b 结果输出 30
-        减 - 得到负数或是一个数减去另一个数                       a - b 结果输出 -10 
*        乘 - 两个数相乘是返回一个被重复若干次的字符串              a * b 结果输出 200
/	       除 - x除以y	b / a                                   输出结果 2
%	       取模 - 返回除法的余数	                                b % a 输出结果 0
**	     幂 - 返回x的y次幂	a**b 为10的20次方                    输出结果 100000000000000000000
//	     取整除 - 返回商的整数部分（向下取整）                     >>> 9//2
                                                                4
                                                                >>> -9//2
                                                                -5
```
示例：
```python
a = 21
b = 10
c = 0

c = a + b
print("c的值为 %d" % c)

c = a - b
print("c的值为 %d" % c)

c = a * b
print("c的值为 %d" % c)

c = a / b
print("c的值为 %d" % c)

c = a % b
print("c的值为 %d" % c)

a = 2
b = 3
c = a ** b
print("c的值为 %d" % c)

a = 10
b = 5
c = a // b
print("c的值为 %d" % c)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    c的值为 31
    c的值为 11
    c的值为 210
    c的值为 2
    c的值为 1
    c的值为 8
    c的值为 2

    进程已结束，退出代码 0

注意：python2.x里,整数除以整数只能得到整数,如果想要得到小数部分,把其中一个数改成浮点数即可
```python
>>> 1/2
0
>>> 1.0/2
0.5
>>> 1/float(2)
0.5
```

## python比较运算符
以下假设变量a为10，变量b为20：
```python
运算符	      描述                                                                                        	实例
==	    等于 - 比较对象是否相等	                                                                         (a == b) 返回 False。
!=	    不等于 - 比较两个对象是否不相等	                                                                 (a != b) 返回 true.
<>	    不等于 - 比较两个对象是否不相等。 python3 已废弃。	                                                 (a <> b) 返回 true。这个运算符类似 != 。
>	      大于 - 返回x是否大于y	                                                                         (a > b) 返回 False。
<	      小于 - 返回x是否小于y。所有比较运算符返回1表示真，返回0表示假。这分别与特殊的变量True和False等价。	 (a < b) 返回 true。
>=	    大于等于 - 返回x是否大于等于y。	                                                                 (a >= b) 返回 False。
<=	    小于等于 - 返回x是否小于等于y。	                                                                 (a <= b) 返回 true。 
```
示例：
```python
a = 21
b = 10
c = 0

if a == b:
    print("a 等于 b")
else:
    print("a 不等于 b")

if a != b:
    print("a 不等于 b")
else:
    print("a 等于  b")

if a < b:
    print("a 小于 b")
else:
    print("a 大于等于 b")

if a > b:
    print("a 大于 b")
else:
    print("a 小于等于 b")

# 修改变量 a 和 b 的值
a = 5
b = 20

if a <= b:
    print("a 小于等于 b")
else:
    print("a 大于 b")

if b >= a:
    print("b 大于等于 a")
else:
    print("b 小于 a")
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    a 不等于 b
    a 不等于 b
    a 大于等于 b
    a 大于 b
    a 小于等于 b
    b 大于等于 a

    进程已结束，退出代码 0

## python赋值运算符
以下假设变量a为10,变量b为20：
```python
运算符	描述	实例
=	      简单的赋值运算符	              c = a + b 将 a + b 的运算结果赋值为 c
+=	      加法赋值运算符	                  c += a 等效于 c = c + a
-=	      减法赋值运算符	                  c -= a 等效于 c = c - a
*=	      乘法赋值运算符	                  c *= a 等效于 c = c * a
/=	      除法赋值运算符	                  c /= a 等效于 c = c / a
%=	      取模赋值运算符	                  c %= a 等效于 c = c % a
**=	      幂赋值运算符	                  c **= a 等效于 c = c ** a
//=	      取整除赋值运算符	              c //= a 等效于 c = c // a
```
示例：
```python
a = 21
b = 10
c = 0

c = a + b
print("c 的值为 %d" % c)

c += a
print("c 的值为 %d" % c)

c *= a
print("c 的值为 %d" % c)

c /= a
print("c 的值为 %d" % c)

c = 2
c %= a
print("c 的值为 %d" % c)

c **= a
print("c 的值为 %d" % c)

c //= a
print("c 的值为 %d" % c)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    c 的值为 31
    c 的值为 52
    c 的值为 1092
    c 的值为 52
    c 的值为 2
    c 的值为 2097152
    c 的值为 99864

    进程已结束，退出代码 0

## python逻辑运算符
Python语言支持逻辑运算符，以下假设变量 a 为 10, b为 20:
```python
and	    x and y	    布尔"与" - 如果 x 为 False，x and y 返回 False，否则它返回 y 的计算值。 	    (a and b) 返回 20。
or	    x or y	    布尔"或" - 如果 x 是非 0，它返回 x 的值，否则它返回 y 的计算值。	            (a or b) 返回 10。
not	    not x	    布尔"非" - 如果 x 为 True，返回 False 。如果 x 为 False，它返回 True。	        not(a and b) 返回 False 
```
示例：
```python
a = 10
b = 20

if a and b:
    print("变量 a 和 b 都为true")
else:
    print("变量 a 和 b 有一个不为 true")

if a or b:
    print("变量 a 和 b 都为 true，或其中一个变量为true")
else:
    print("变量 a 和 b 都不为true")

# 修改变量 a 的值
a = 0
if a and b:
    print("变量 a 和 b 都为true")
else:
    print("变量 a 和 b 有一个不为true")

if a or b:
    print("变量 a 和 b 都为true，活其中一个变量为true")
else:
    print("变量 a 和 b 都不为true")

if not(a and b):
    print("变量 a 和 b 都为 false，或其中一个变量为 false")
else:
    print("变量 a 和 b 都为 true")
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    变量 a 和 b 都为true
    变量 a 和 b 都为 true，或其中一个变量为true
    变量 a 和 b 有一个不为true
    变量 a 和 b 都为true，活其中一个变量为true
    变量 a 和 b 都为 false，或其中一个变量为 false

    进程已结束，退出代码 0

## python成员运算符
除了以上的一些运算符之外，Python还支持成员运算符，测试实例中包含了一系列的成员，包括字符串，列表或元组。
```python
运算符              	描述	                                                                  实例
in	        如果在指定的序列中找到值返回 True，否则返回 False。 	            x 在 y 序列中 , 如果 x 在 y 序列中返回 True。
not in	    如果在指定的序列中没有找到值返回 True，否则返回 False。         	x 不在 y 序列中 , 如果 x 不在 y 序列中返回 True。
```
示例：
```python
a = 10
b = 20
list = [1,2,3,4,5]

if (a in list):
    print("变量 a  在指定的列表 list 中")
else:
    print("变量 a  不在指定的列表 list 中")

if (b not in list):
    print("变量 b 不在指定的列表 list 中")
else:
    print("变量 b 在指定的列表 list 中")

# 修改变量a 的值
a = 2
if (a in list):
    print("变量 a  在指定的列表 list 中")
else:
    print("变量 a  不在指定的列表 list 中")
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    变量 a  不在指定的列表 list 中
    变量 b 不在指定的列表 list 中
    变量 a  在指定的列表 list 中

    进程已结束，退出代码 0

## python身份运算符
身份运算符用于比较两个对象的存储单元
```python
运算符	                      描述	                                        实例
is	        is 是判断两个标识符是不是引用自一个对象	                        x is y, 类似 id(x) == id(y) , 如果引用的是同一个对象则返回 True，否则返回 False
is not	    is not  是判断两个标识符是不是引用自不同对象                   	x is not y ， 类似 id(a) != id(b)。如果引用的不是同一个对象则返回结果 True，否则返回 False。 
```
示例：
```python
a = 20
b = 20

if (a is b):
    print("a 和 b 有相同的标识")
else:
    print("a 和 b 没有相同的标识")

if (a is not b):
    print( "a 和 b 没有相同的标识")
else:
    print("a 和 b 有相同的标识")

# 修改变量 b 的值
b = 30
if (a is b):
    print("a 和 b 有相同的标识")
else:
    print("a 和 b 没有相同的标识")

if (a is not b):
    print("a 和 b 没有相同的标识")
else:
    print("a 和 b 有相同的标识")
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    a 和 b 有相同的标识
    a 和 b 有相同的标识
    a 和 b 没有相同的标识
    a 和 b 没有相同的标识

    进程已结束，退出代码 0

## python运算符优先级
以下表格列出了从最高到最低优先级的所有运算符：
```python
运算符      	                          描述
** 	                              指数 (最高优先级)
~ + -     	                      按位翻转, 一元加号和减号 (最后两个的方法名为 +@ 和 -@)
* / % //    	                  乘，除，取模和取整除
+ - 	                          加法减法
>> <<       	                  右移，左移运算符
& 	                              位 'AND'
^ | 	                          位运算符
<= < > >=         	              比较运算符
<> == != 	                      等于运算符
= %= /= //= -= += *= **= 	      赋值运算符
is is not               	      身份运算符
in not in                         成员运算符
not and or 	                      逻辑运算符