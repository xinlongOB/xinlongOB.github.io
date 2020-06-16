---
title: python基础之函数
tags:
  - python
  - liunx
date: 2020-06-14 12:21:21
---
## python函数
函数是组织好的、可重复使用的、用来实现单一或相关联功能的代码段
<br/>函数能提高应用的模块性,和代码的重复利用率。python提供了许多内置函数比如print(),可以自己创建函数,成伟用户自定义函数<br/>
## 定义函数
你可以定义一个由自己想要功能的函数，以下是简单的规则：

    函数代码块以 def 关键词开头，后接函数标识符名称和圆括号()。
    任何传入参数和自变量必须放在圆括号中间。圆括号之间可以用于定义参数。
    函数的第一行语句可以选择性地使用文档字符串—用于存放函数说明。
    函数内容以冒号起始，并且缩进。
    return [表达式] 结束函数，选择性地返回一个值给调用方。不带表达式的return相当于返回 None。

语法：
```python
def functionname( parameters ):
   "函数_文档字符串"
   function_suite
   return [expression]
```
实例：
```python
def printme(str):
   "打印传入的字符串到标准显示设备上"
   print(str)
   return
```
## 函数调用
定义一个函数只给了函数一个名称，指定了函数里包含的参数，和代码块结构。
<br/>这个函数的基本结构完成以后，你可以通过另一个函数调用执行，也可以直接从Python提示符执行。<br/>
如下实例调用了printme（）函数：

```python
# 定义函数
def printme(str):
    "打印任何传入的字符串"
    print(str)
    return

# 调用函数
printme("我要调用用户自定义函数!")
printme("再次调用同一函数")
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    我要调用用户自定义函数!
    再次调用同一函数

    进程已结束，退出代码 0

## 参数传递
在 python 中，类型属于对象，变量是没有类型的：
```python
a=[1,2,3]
 
a="Runoob"
```
以上代码中，[1,2,3] 是 List 类型，"Runoob" 是 String 类型，而变量 a 是没有类型，她仅仅是一个对象的引用（一个指针），可以是 List 类型对象，也可以指向 String 类型对象。

## 函数案例
```python
def test(zifu,cishu):
    print(zifu * cishu)
test("-",20)
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    --------------------

    进程已结束，退出代码 0

```python
# 定义name_list字典
name_list = [
    {"name" : "小小",
     "age" : "20"},
    {"name" : "yy",
     "age" : "20"}
]

# 定义test 函数 循环字典 并且判断如果name == 小小  则修改name
# 修改name的值由find 函数传递
def test():
    for all in name_list:
        if all["name"] == "小小":
            all["name"] = find(all["name"],"请输入 ：")

    print(name_list)

# 定义 find函数  被test函数条用  aa是传递的第一个参数默认为"小小"，nishi为用户输入的函数
def find(aa,nishi):
        re = input(nishi)
        if len(re) > 0:   # 判断输入的数据个数是否大于0  大于0 返还输入值给调用方 如果不大于0 则返还原参数给调用方
            return  re
        else:
            return aa
test()
```
运行结果(传输参数)：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    请输入 ：鑫龙
    [{'name': '鑫龙', 'age': '20'}, {'name': 'yy', 'age': '20'}]

    进程已结束，退出代码 0

运行结果(直接回车)：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    请输入 ：
    [{'name': '小小', 'age': '20'}, {'name': 'yy', 'age': '20'}]

    进程已结束，退出代码 0

## 使用元祖接受调用函数之后返回的多个值
```python
def measure():
    # 测量温度和湿度
    print("测量开始...")
    temp = 36
    wetness = 50
    print("测量结束...")
    # 元祖可以包含多个数据，因此可以使用元祖让函数一次返回多个值
    # 如果函数返还的类型是元祖,小括号可以省略
    return temp,wetness

result = measure()
print(result)
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    测量开始...
    测量结束...
    (36, 50)

    进程已结束，退出代码 0

如果需要单独处理温度或者湿度
```python
def measure():
    # 测量温度和湿度
    print("测量开始...")
    temp = 36
    wetness = 50
    print("测量结束...")
    # 元祖可以包含多个数据，因此可以使用元祖让函数一次返回多个值
    # 如果函数返还的类型是元祖,小括号可以省略
    return temp,wetness

result = measure()
print("温度为 %s " % result[0])
print("湿度为 %s " % result[1])
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    测量开始...
    测量结束...
    温度为 36 
    湿度为 50 

    进程已结束，退出代码 0

使用多个变量一次接受函数返回结果
<br/>如果函数返回的类型是元祖,希望单独处理元祖中的元素,可以使用多个变量一次接受函数的返回结果<br/>