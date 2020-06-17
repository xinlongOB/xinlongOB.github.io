---
title: Python基础之字符串
tags:
  - python
  - liunx
categories: 开发
date: 2020-05-26 17:01:34
---
## print函数

```python
print('hello world!')
	print('hello', 'world!')  # 逗号自动添加默认的分隔符：空格
	print('hello' + 'world!')  # 加号表示字符拼接
	print('hello', 'world', sep='***')  # 单词间用***分隔
	print('#' * 50)  # *号表示重复50遍
	print('how are you?', end='') # 默认print会打印回车，end=''表示不要回车
```

## 双引号和单引号的作用

```python
    str = "我是谁"
    print(str)

    str1 = '我是"谁"'
    print(str1)
```

运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    我是谁
    我是"谁"

    进程已结束，退出代码 0

## 从字符串中提取字符
```python
    str = "hello python"
    ##提取里面的y
    print(str[7])
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    y

    进程已结束，退出代码 0

## 循环遍历字符串中的每一个字符
```python
    str = "hello"

    for c in str:
        print(c)

```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    h
    e
    l
    l
    o

    进程已结束，退出代码 0

## 统计字符串长度
```python
    str = "hello world"
    print(len(str))
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    11

    进程已结束，退出代码 0

## 统计一个字符在字符串出现的次数(如果不存在不会报错,会显示出现0次)
```python
    str = "hello world"
    # 查看字符"l"  在字符串中出现的次数
    print(str.count("l"))
    # 如果不存在则显示0次
    print(str.count("a"))
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    3
    0

    进程已结束，退出代码 0

## 取出索引(如果不存在则报错)
```python
    str = "hello world"
    print(str.index("d"))
    print(str.index("K"))
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    10
    Traceback (most recent call last):
      File "E:/程序代码/hexo/test.py", line 4, in <module>
        print(str.index("K"))
    ValueError: substring not found

    进程已结束，退出代码 1

## 字符串操作--判断类型
判断字符串中是否只包含空格,如果只包含空格会显示True,否则打印false (\t \n \r都属于空格)
```python
    space_str = " \t \n \r"
    print(space_str.isspace())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    True

    进程已结束，退出代码 0

判断字符串至少有一个字符并且所有字符都为字母或数字则返回True   （有一个空格或者符号都返还false）
```python
    alnum = "skill2"
    print(alnum.isalnum())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    True

    进程已结束，退出代码 0

判断字符串至少有一个字符并且所有字符都为字母则返回True   （有一个空格、数字或者符号都返还false）
```python
    alpha = "string"
    print(alpha.isalpha())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    True

    进程已结束，退出代码 0

判断字符串只包含数字则返还True
```python
    digit = "942868591"
    print(digit.isdigit())    
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    True

    进程已结束，退出代码 0

注：

    isdigit   isdecimal  isnumeric    三个方法都不能判断小数
    isdigit 可以判断unicode字符串   例如：\u00b2      isnumeric  可以判断中文数字  例如：一千零一

判断字符串是标题化的(每个单词的首字母大写)则返回 True  (单词首字母不是大写则返回false）
```python
    title = "Hello World"
    print(title.istitle())
    title1 = "Hello world"
    print(title1.istitle())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    True
    False

    进程已结束，退出代码 0

判断字符串全是小写
```python
    lower = "abc"
    print(lower.islower())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    True

    进程已结束，退出代码 0

判断字符串全是大写
```python
    upper = "PWD"
    print(upper.isupper())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    True

    进程已结束，退出代码 0

## 字符串操作--查找和替换
检查字符串师傅以na开头,是则返还True
```python
    start = "name is xxx"
    print(start.startswith("na"))
```
运行结果

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    True

    进程已结束，退出代码 0

检查字符串是否是以xxx结束,是则返回True
```python
    start = "name is xxx"
    print(start.endswith("xxx"))
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    True

    进程已结束，退出代码 0

检测 server 是否包含在 string 中，如果 start 和 end 指定范围，则检查是否包含在指定范围内，如果是返回开始的索引值，否则返回 -1
```python
    find = "all the servers is Ok and set ableConnect"
    ##查找find字符串中是否包含server
    print(find.find("server"))
    ##查找find字符串中是否包含server   开始索引0   结束索引20
    print(find.find("server",0,20))
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    8
    8

    进程已结束，退出代码 0

rfind()类似于find(),不过是从右边开始查找
```python
    rfind = "all the servers is Ok and set ableConnect"
    print(rfind.rfind("server"))
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    8

    进程已结束，退出代码 0

index()和find()方法类型,不过如果str不在string中会报错
```python
    index = "all the servers is Ok and set ableConnect"
    print(index.index("server"))
    print(index.index("problem"))
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    Traceback (most recent call last):
    8
      File "E:/程序代码/hexo/test.py", line 3, in <module>
        print(index.index("problem"))
    ValueError: substring not found

    进程已结束，退出代码 1

把 string 中的 all 替换成 two，如果 num 指定，则替换不超过 num 次
```python
    replace = "all the servers is Ok and set ableConnect  all  all  all"
    ##把 string 中的 all 全部替换成 two
    print(replace.replace("all","two"))
    ##把 string 中的前两个 all 替换成 two
    print(replace.replace("all","two",2))
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    two the servers is Ok and set ableConnect  two  two  two
    two the servers is Ok and set ableConnect  two  all  all

    进程已结束，退出代码 0

## 字符串操作--大小写转换
把字符串的第一个字符大写
```python
    capitalize = "all the servers is Ok and set ableConnect"
    print(capitalize.capitalize())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    All the servers is ok and set ableconnect

    进程已结束，退出代码 0

把字符串的每个单词首字母大写
```python
    title = "all the servers is Ok and set aBleConnect"
    print(title.title())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    All The Servers Is Ok And Set Ableconnect

    进程已结束，退出代码 0

转换 string 中所有大写字符为小写
```python
    lower = "All The Servers Is Ok And Set Ableconnect"
    print(lower.lower())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    all the servers is ok and set ableconnect

    进程已结束，退出代码 0

转换 string 中所有小写字母为大写
```python
    upper = "all the servers is ok and set ableconnect"
    print(upper.upper())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    ALL THE SERVERS IS OK AND SET ABLECONNECT

    进程已结束，退出代码 0

翻转 string 中的大小写
```python
    swapcase = "A b C d E f"
    print(swapcase.swapcase())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    a B c D e F

    进程已结束，退出代码 0
    
## 字符串操作--文本对齐
返回一个原字符串左对齐      例如str.ljust(width[, fillchar])      width是个数  fillchar是填充符 默认空格
 ```python
    just = "all the servers is Ok and set ableConnect"
    ## 49 意思就是加上原来的字符串 一共的长度  +为填充符
    print(just.ljust(49,"+"))
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    all the servers is Ok and set ableConnect++++++++

    进程已结束，退出代码 0

返回一个原字符串右对齐
 ```python
    just = "all the servers is Ok and set ableConnect"
    print(just.rjust(49,"+"))
    print(just.center(49,"+"))
  ```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    ++++++++all the servers is Ok and set ableConnect
    ++++all the servers is Ok and set ableConnect++++

    进程已结束，退出代码 0

## 字符串操作--去除空白字符
```python
    str = "       all the servers is Ok and set ableConnect      "
    print(str)
    ##截掉 string 左边（开始）的空白字符
    print(str.lstrip())
    ##截掉 string 右边（开始）的空白字符
    print(str.rstrip())
    ##截掉 string 左右两边的空白字符
    print(str.strip())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
          all the servers is Ok and set ableConnect      
    all the servers is Ok and set ableConnect      
          all the servers is Ok and set ableConnect
    all the servers is Ok and set ableConnect

进程已结束，退出代码 0

## 字符串操作--拆分和连接
把字符串 string 分成一个 3 元素的元组 (str前面, str, str后面)
```python
    partition = "all the servers is Ok and set ableConnect"
    print(partition.partition("the"))
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    ('all ', 'the', ' servers is Ok and set ableConnect')

    进程已结束，退出代码 0

rpartition()类似于 partition() 方法，不过是从右边开始查找
```python
    partition = "all the and servers is Ok and set ableConnect"
    print(partition.rpartition("and"))
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    ('all the and servers is Ok ', 'and', ' set ableConnect')

    进程已结束，退出代码 0

string.split(str="", num) | 以 str 为分隔符拆分 string，如果 num 有指定值，则仅分隔 num + 1 个子字符串，str 默认包含 '\r', '\t', '\n' 和空格
```python
    pome = "登鹤雀楼 \t  王之涣  \t  白日依山尽  \t \n  黄河入海流  \t  \t 欲穷千里目   \n  更上一层楼"
    print(pome)
    print("-"*50)
    pome_list = pome.split()
    print(pome_list)
    print("-"*50)
    lastpome = " ".join(pome_list)
    print(lastpome)
    print("-"*50)
    print(lastpome.replace(" ","\n"))
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    登鹤雀楼 	  王之涣  	  白日依山尽  	 
      黄河入海流  	  	 欲穷千里目   
      更上一层楼
    --------------------------------------------------
    ['登鹤雀楼', '王之涣', '白日依山尽', '黄河入海流', '欲穷千里目', '更上一层楼']
    --------------------------------------------------
    登鹤雀楼 王之涣 白日依山尽 黄河入海流 欲穷千里目 更上一层楼
    --------------------------------------------------
    登鹤雀楼
    王之涣
    白日依山尽
    黄河入海流
    欲穷千里目
    更上一层楼

    进程已结束，退出代码 0

## 字符串切片
```python
    num_str = "0123456789"
    ##1、截取从2 - 5位置的字符串
    print(num_str[2:6])
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    2345

    进程已结束，退出代码 0
```pyton
    ##2、截取2 - 末尾的字符串
    print(num_str[2:])
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    23456789

    进程已结束，退出代码 0


```pyton
    ##3、截取从开始 - 5 位置的字符串
    print(num_str[:6])
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    012345

    进程已结束，退出代码 0

```pyton
    ##4、截取完整的字符串
    print(num_str[:])
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    0123456789

    进程已结束，退出代码 0

```pyton
    ##5、从开始位置，每隔一个字符截取字符串
    print(num_str[::2])
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    02468

    进程已结束，退出代码 0

```pyton
    ##6、从索引 1 开始，每隔一个取一个
    print(num_str[1::2])
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    13579

    进程已结束，退出代码 0

```pyton
    ##7、截取从2 - 末尾 -1  的字符串
    print(num_str[2:-1])
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    2345678

    进程已结束，退出代码 0

```pyton
    ##8、截取字符串末尾两个字符
    print(num_str[-2:])
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    89

    进程已结束，退出代码 0

```pyton
    ##9、字符串的逆序
    print(num_str[-1::-1])
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    9876543210

    进程已结束，退出代码 0

