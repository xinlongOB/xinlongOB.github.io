---
title: python基础之函数
tags:
  - python
  linux
categories: 开发
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
注意：使用多个遍历接收结果时,变量的个数和元祖中的元素个数保持一致

```python
def measure():
    # 测量温度和湿度
    print("测量开始...")
    temp = 36
    wetness = 50
    print("测量结束...")
    return  temp,wetness
gl_wen,gl_shi = measure()
print("温度为 %d" % gl_wen)
print("湿度为 %d" % gl_shi)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    测量开始...
    测量结束...
    温度为 36
    湿度为 50

    进程已结束，退出代码 0

在函数内部,针对参数使用赋值语句,不会修改到外部的实参变量
```python
def demo(num,num_list):
    print("函数内部的代码")
    num = 100
    num_list= [1,2,3]

    print(num)
    print(num_list)
    print("函数执行完成")

gl_num = 99
gl_list = [4,5,6]
demo(gl_num,gl_list)
print(gl_num)
print(gl_list)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    函数内部的代码
    100
    [1, 2, 3]
    函数执行完成
    99
    [4, 5, 6]

    进程已结束，退出代码 0

使用方法修改数据内容,会影响到外部的数据 (此修改不是重新定义变量,而是直接修改append或者是remove)
```python
def demo(num_list):
    print("函数内部的代码")
    gl_list.append("999")
    # 在列表中新增999
    # 使用方法修改了数据的内容,会影响到外部的数据

    print(num_list)
    print("函数执行完成")

gl_list = [4,5,6]
print(gl_list)
demo(gl_list)
print(gl_list)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    [4, 5, 6]
    函数内部的代码
    [4, 5, 6, '999']
    函数执行完成
    [4, 5, 6, '999']

    进程已结束，退出代码 0

列表变量使用 + 不会做相加在赋值的操作
```python
def demo(num,num_list):
    print("函数开始")
    # num += num  就是 num = num + num 数字先相加在赋值
    num += num

    # num_list = num_list + num_list
    # 列表变量使用 + 不会做相加在赋值的操作
    # 本质是在调用列表的extend方法(增加另一个列表内容到此列表中  list.extend(list))
    # num_list += num_list 这样相当于调用extend方法会修改外部变量

    #  num_list = num_list + num_list  这样只是赋值  不会修改
    num_list = num_list + num_list
    print(num)
    print(num_list)
    print("函数完成")

gl_num = 9
gl_list = [1,2,3]
demo(gl_num,gl_list)
print(gl_num)
print(gl_list)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    函数开始
    18
    [1, 2, 3, 1, 2, 3]
    函数完成
    9
    [1, 2, 3]

    进程已结束，退出代码 0

## 缺省参数
缺省参数,就是讲常见的值设为参数的缺省值,从而简化函数的调用
```python
gl_list = [6,3,9]
#默认按照升序排序
gl_list.sort()
print(gl_list)
##如果需要降序排序，需要执行reverse参数
gl_list.sort(reverse=True)
print(gl_list)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    [3, 6, 9]
    [9, 6, 3]

    进程已结束，退出代码 0

定义函数指定缺省参数,需要在末尾指定,不然后面无法写参数
```python

def print_info(name,gender=True):
    # :param name：同学姓名
    # :param gender：True 男生 False 女生
    # :return
    gender_test = "男生"  # 默认参数为男生
    print(gender)
    # if gender != True:
    if not gender:    # 如果gender 不等于True  则赋值 gender_test = 女生
        gender_test = "女生"
    print("%s 是 %s " % (name,gender_test))

#提示：在指定缺省参数的默认值时，应该使用最常见的值作为默认值
print_info("小明")
print_info("小红","女")
print_info("小明")
print_info("老王")
print_info("小美",False)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    True
    小明 是 男生 
    女
    小红 是 男生 
    True
    小明 是 男生 
    True
    老王 是 男生 
    False
    小美 是 女生 

    进程已结束，退出代码 0

## 多值参数
```python
def demo(num,*nums,**person):
    print(num)
    print(nums)
    print(person)

demo(2,3,4,5,name = "小明",age = 18)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    2
    (3, 4, 5)
    {'name': '小明', 'age': 18}

    进程已结束，退出代码 0

多值参数--数字累加
*args:args中保存的是没有利用的所有多余参数，保存方式为元组，**args即输入多余参数有变量名，就保存在**args中保存，保存方式为字典
```python
def sum_numbers(*args):    # 传递一个元祖
    num = 0      # 定义 num = 0
    print(args)
    for n in  args:     # 循环相加
        num += n
    return num          # 返还相加后的数
result = sum_numbers(1,2,3,4,5)   # 调用函数传递元祖 赋值给result
print(result)    #  打印结果
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    (1, 2, 3, 4, 5)
    15

    进程已结束，退出代码 0

## 递归
递归就是函数内部调用自己的函数被称之为递归

        1、必须有一个明确的结束条件
        2、每次进入更深一层递归时，问题规模(计算量)相比上次递归都应有所减少
        3、递归效率不高，递归层次过多会导致栈溢出（在计算机中，函数调用是通过栈（stack）这种数据结构实现的，每当进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减一层栈帧。
            由于栈的大小不是无限的，所以，递归调用的次数过多，会导致栈溢出
        python默认的层次是1000(传递的数大于1000就会报错)    centos系统是根据limit决定

递归还有两个名词用来概括递归实现的过程：

        递推：像上边递归实现所拆解，递归每一次都是基于上一次进行下一次的执行，这叫递推

        回溯：则是在遇到终止条件，则从最后往回返一级一级的把值返回来，这叫回溯
        
```python
def sum_numbers(num):
    print(num)
    #递归的出口，当参数满足某个条件，不在执行参数
    if num == 1:
        return
    #递归的特点就是自己调用自己
    sum_numbers(num - 1)

sum_numbers(3)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    3
    2
    1

    进程已结束，退出代码 0

递归是一个编程技巧,在处理不确定的循环条件时是格外有用,例如：遍历整个文件目录的结构：
```python
def sum_numbers(num):
    # 1、定义出口
    #递归的出口，当参数满足某个条件，不在执行参数
    if num == 1:
        return 1
    # 2、数字累加 num + (1...num -1)
    # 假设sum_numbers 能够正确的处理 1...num -1
    temp = sum_numbers(num - 1)
    # 两个数字相加
    return num + temp

result = sum_numbers(100)
print(result)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    5050

    进程已结束，退出代码 0

示例1：
```python
# 1！+2！+3！+4！+5！+...+n!
def factorial(n):
    ''' n表示要求的数的阶乘 '''
    if n==1:
        return n # 阶乘为1的时候，结果为1,返回结果并退出
    n = n*factorial(n-1) # n! = n*(n-1)!
    return n  # 返回结果并退出
res = factorial(5) #调用函数，并将返回的结果赋给res
print(res) # 打印结果
```
示例2：
```python
# 1，1，2，3，5，8，13，21，34，55，试判断数列第十五个数是哪个？
def fabonacci(n):
    ''' n为斐波那契数列 '''
    if n <= 2:
        ''' 数列前两个数都是1 '''
        v = 1
        return v # 返回结果，并结束函数
    v = fabonacci(n-1)+fabonacci(n-2) # 由数据的规律可知，第三个数的结果都是前两个数之和，所以进行递归叠加
    return v  # 返回结果，并结束函数
print(fabonacci(15)) # 610    调用函数并打印结果
```
示例3：
```python
data = [1,3,6,13,56,123,345,1024,3223,6688]
def dichotomy(min,max,d,n):
    '''
    min表示有序列表头部索引
    max表示有序列表尾部索引
    d表示有序列表
    n表示需要寻找的元素
    '''
    mid = (min+max)//2
    if mid==0:
        return 'None'
    elif d[mid]<n:
        print('向右侧找！')
        return dichotomy(mid,max,d,n)
    elif d[mid]>n:
        print('向左侧找！')
        return dichotomy(min,mid,d,n)
    else:
        print('找到了%s'%d[mid])
        return 
res = dichotomy(0,len(data),data,222)
print(res)
```