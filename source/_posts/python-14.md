---
title: python基础之抛出异常
tags:
  - python
  - liunx
categories: 开发
date: 2020-06-20 14:10:46
---
## 捕获异常
在程序开发中,如果对默认代码不能确定是否正确,可以增加try(尝试)来捕获异常,语法格式：
```python
try:
    # 不能确定正确执行的代码
    num = int(input("请输入一个整数："))
except:
    # 错误的处理代码
    print("请输入一个整数")
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    请输入一个整数：66

    进程已结束，退出代码 0

运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    请输入一个整数：aa
    请输入一个整数

    进程已结束，退出代码 0

## 错误类偶像捕获异常
在程序执行时,可能遇到不同类型的异常,并且需要针对不同的类型的异常,做出不同的响应
<br/>当python解释器抛出异常时,最后一行错误信息的第一个单词就是错误类型,如：ValueError，ZeroDivisionError<br/>
例如：

```python
try:
    num = int(input("请输入一个整数："))
    # 使用 8 除以用户输入的整数并且输出
    result = 8 / num
    print(result)
except ZeroDivisionError:
    print("除 0 错误")
except ValueError:
    print("请输入正确的整数")
```
运行结果(输入 0 )：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    请输入一个整数：0
    除 0 错误

    进程已结束，退出代码 0

运行结果(输入非整数)：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    请输入一个整数：aa
    请输入正确的整数

    进程已结束，退出代码 0

运行结果(正常)：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    请输入一个整数：10
    0.8

    进程已结束，退出代码 
    
可以填写不同类型的异常   例如ValueError，ZeroDivisionError   以此类推

## 捕获未知错误
在开发时,要预判到所有可能出现的错误,还是有一定难度的,如果需要无论出现任何错误,都不会因为python解释器抛出异常而被终止,可以在增加一个except,语法如下：
```python
try:
    pass
except  Exception  as   变量名：
    print("未知错误 %s" % 变量名)
```
例如：
```python
try:
    #不能确定正确执行的代码
    num = int(input("请输入一个整数 ："))

    #使用 8 除以用户输入的整数并且输出
    result = 8 / num
    print(result)
except ZeroDivisionError:
    print("除 0 错误")
    #以上代码之后错误类型是0时  会抛出错误  其他的都是导致异常退出
except Exception as result:
    print("未知错误 %s" % result)
    #这样其他的错误也可以被捕获到
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    请输入一个整数 ：aa
    未知错误 invalid literal for int() with base 10: 'aa'

    进程已结束，退出代码 0

## 异常捕获完整语法
语法：
```python
try:
    pass
except 1:
    pass
except 2:
    pass
except Exception as result:
    pass
else:
    #没有异常才会执行的代码
    pass

finally:
    #无论是否有异常，都会执行的代码
    pass
```
else：只有在没有异常的情况下才会执行的代码
<br/>finally：无论是否有异常都会被执行的代码<br/>
例如：

```python
try:
    #不能确定正确执行的代码
    num = int(input("请输入一个整数 ："))

    #使用 8 除以用户输入的整数并且输出
    result = 8 / num
    print(result)
except ZeroDivisionError:
    print("除 0 错误")
except ValueError:
    print("请输入正确的整数")
else:
    print("没有错误执行")
finally:
    print("全部执行")
```
运行结果(除 0 错误)：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    请输入一个整数 ：0
    除 0 错误
    全部执行

    进程已结束，退出代码 0

运行结果(没有错误):

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    请输入一个整数 ：8
    1.0
    没有错误执行
    全部执行

    进程已结束，退出代码 0

## 异常的传递
异常的传递--当函数/方法执行出现异常,会将异常传递给函数/方法的调用一方,如果传递到主程序,仍然没有异常处理,程序才会被终止
<br/>提示：<br/>
在开发中,可以在主程序中增加异常捕获,而在主函数中调用其他的函数,只要出现异常,都会传递到主函数的异常捕获中,这样就不需要在代码中增加大量的异常捕获,能够保证代码的整洁

需求：

    1、定义函数demo1()提示用户输入一个整数并且返还
    2、定义函数demo2()调用demo1()
    3、在主程序中调用demo2()

```python
#定义函数demo1 返回用户输入的整数
def demo1():
    return  int(input("请输入一个整数："))

#定义函数demo2  调用函数demo1
def demo2():
    return  demo1()

#利用异常的传递性，在主程序中捕获异常
try:
    print(demo1())
except Exception as  result:
    print("未知错误 %s" % result)
```
运行结果(正常)：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    请输入一个整数：1
    1

    进程已结束，退出代码 0

运行结果(异常)：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    请输入一个整数：a
    未知错误 invalid literal for int() with base 10: 'a'

    进程已结束，退出代码 0

## 主动抛出异常并且捕获
抛出raise异常：在开发中,除了代码执行错误python解释器会抛出异常之外,还可以根据应用程序特有的业务需求主动抛出异常

    如果函数中没有定义任何返回  如果使用print输出 就会输出一个None  （*args  是一个多值的元组参数）

主动抛出异常并且捕获,python中提供了一个Exception异常类,在开发时,如果满足特定业务需求时,希望抛出异常可以：

    1、创建一个Exception的对象
    2、使用raise 关键字抛出异常对象

需求：

    1、定义input_password函数，提示用户输入密码
    2、如果用户输入长度<8 抛出异常
    3、如果用户输入长度>=8 返回输入的密码

```python

    # 1、定义input_password函数，提示用户输入密码
    pwd = input("请输入密码：")
    # 2、如果用户输入长度 < 8  抛出异常
    if len(pwd) >= 8:
        return pwd
    print("主动抛出异常")
    ex = Exception("密码长度不够")
    raise ex

try:
    print(input_password()) # 执行函数并打印
except Exception as  result:  # 捕获所有异常 并 传递到result 变量
    print(result)
finally:   # 所有情况下都会执行的代码
    print("代码结束")

```
运行结果(正常)：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    请输入密码：12345678
    12345678
    代码结束

    进程已结束，退出代码 0

运行结果(异常)：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    请输入密码：123456
    主动抛出异常
    密码长度不够
    代码结束

    进程已结束，退出代码 0