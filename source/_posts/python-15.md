---
title: python基础之正则表达式
tags:
  - python
  - linux
categories: 开发
date: 2020-06-26 11:04:41
---
## re模块
常用语法
```python
import re
re.match(r"","")
# 调用模块re的match函数,第一个是正则表达式,第二个是需要处理的字符串
```
例如：
```python
import re 
a = re.match(r"hello","hello world")  
print(a.group())  # 如果打印出来有内容,表示已匹配到
```
re.match的返回值是一个对象,如果只显示匹配到的内容,可以使用对象.group()  如：print(a.group()) 

## 匹配单个字符
如果匹配正常会打印对象
```python
import re
a = re.match(r"hello","hello world")
print(a)
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    <re.Match object; span=(0, 5), match='hello'>

    进程已结束，退出代码 0

如果匹配失败会打印None
```python
import re
b =  re.match(r"test","hello world")
print(b)  # 打印变量b  会显示none
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    None

    进程已结束，退出代码 0

. 匹配任意一个字符(除了\n)
```python
import re
a = re.match(r"速度与激情.","速度与激情8")  
print(a.group())
b = re.match(r"速度与激情.","速度与激情a")
print(b.group())
c = re.match(r"速度与激情.","速度与激情aa")
print(c.group())   #只会匹配到速度与激情a  最后一个a 不会被匹配
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    速度与激情8
    速度与激情a
    速度与激情a

    进程已结束，退出代码 0

[] 匹配[]中列举的字符
```python
import re
a = re.match(r"速度与激情[1-8]","速度与激情8") 
print(a.group())  # 如果想排除4、5  可以写成[1-36-8] 这个意思是1-3  6-8
b = re.match(r"速度与激情[1-8]","速度与激情9")
print(b.group())  # 因为[]只有1-8 所以这里会报错
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    Traceback (most recent call last):
    速度与激情8
      File "E:/程序代码/hexo/test.py", line 5, in <module>
        print(b.group())
    AttributeError: 'NoneType' object has no attribute 'group'

    进程已结束，退出代码 1

\d 匹配数字 0-9
```python
import re
a = re.match(r"速度与激情\d","速度与激情8")
print(a.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    速度与激情8

    进程已结束，退出代码 0

\D 匹配非数字
```python
import re
a = re.match(r"速度与激情\D","速度与激情八")
print(a.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    速度与激情八

    进程已结束，退出代码 0

\s 匹配空白 即 空格 tab键
```python
import re
a = re.match(r"速度与激情\s\D","速度与激情 八")
print(a.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    速度与激情 八

    进程已结束，退出代码 0

\S 匹配非空白
```python
import re
a = re.match(r"速度与激情\S","速度与激情八")
print(a.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    速度与激情八

    进程已结束，退出代码 0

\w 匹配单个字符 即a-z、A-Z、0-9 包括中文字符
```python
import re
a = re.match(r"速度与激情\w\w","速度与激情八a")
print(a.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    速度与激情八a

    进程已结束，退出代码 0

\W 匹配非单词字符
```python
import re
a = re.match(r"速度与激情\W","速度与激情#")
print(a.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    速度与激情#

    进程已结束，退出代码 0

## 匹配多个字符
```python
aa = re.match(r"速度与激情\d{1,3}","速度与激情1")
print(aa.group())

bb = re.match(r"速度与激情\d{1,3}","速度与激情12")
print(bb.group())

cc = re.match(r"速度与激情\d{1,3}","速度与激情125")
print(cc.group())

# 以此证明 \d 后面大括号里面是匹配的位数 最少1位  最多3位

dd = re.match(r"\d{11}","12345678901")
print(dd.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    速度与激情1
    速度与激情12
    速度与激情125
    12345678901

    进程已结束，退出代码 0

*   匹配前一个字符出现0次或者无限次
```python
import re
aa = re.match(r"速度与激情\d*","速度与激情111111")
print(aa.group())
bb = re.match(r"速度与激情\d*","速度与激情")
print(bb.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    速度与激情111111
    速度与激情

    进程已结束，退出代码 0

+   匹配前一个字符出现1次或者无限次(至少出现1次否则报错)
```python
import re
aa = re.match(r"速度与激情\d+","速度与激情111111")
print(aa.group())
bb = re.match(r"速度与激情\d+","速度与激情")
print(bb.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    速度与激情111111
    Traceback (most recent call last):
      File "E:/程序代码/hexo/test.py", line 5, in <module>
        print(bb.group())
    AttributeError: 'NoneType' object has no attribute 'group'

    进程已结束，退出代码 1

？  匹配前一个字符出现1次或者0次
```python
import re
ee = re.match(r"010-\d{8}","010-12345678")
print(ee.group())

ff = re.match(r"010-?\d{8}","010-12345678")  # 这样-就可以不输入了
print(ff.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    010-12345678
    010-12345678

    进程已结束，退出代码 0

{m} 匹配前一个字符出现m次
```python
import re
ee = re.match(r"010-\d{8}","010-12345678")
print(ee.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    010-12345678

    进程已结束，退出代码 0

{m，n} 匹配前一个字符出现从m次到n次
```python
import re
gg = re.match(r"\d{3,4}-?\d{8}","0530-12345678")
print(gg.group())
dd = re.match(r"\d{3,4}-?\d{8}","010-12345678")
print(dd.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    0530-12345678
    010-12345678

    进程已结束，退出代码 0

## 匹配结尾开头
```python
import re

def main():
    names = ["age","_age","1age","age1","a_age","age_1_","age!","a#123","_____"]
    for name in names:
        ret = re.match(r"[a-zA-Z][a-zA-Z_]*",name)
        if ret:   # 判断是否有值   有值就打印出来
                print("变量名：%s 符合要求....通过正则匹配出来的数据是%s" % (name,ret.group()))
        else:
                print("变量名：%s 不符合要求")

if __name__ == "__main__":
    main()
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    变量名：age 符合要求....通过正则匹配出来的数据是age
    变量名：%s 不符合要求
    变量名：%s 不符合要求
    变量名：age1 符合要求....通过正则匹配出来的数据是age
    变量名：a_age 符合要求....通过正则匹配出来的数据是a_age
    变量名：age_1_ 符合要求....通过正则匹配出来的数据是age_
    变量名：age! 符合要求....通过正则匹配出来的数据是age
    变量名：a#123 符合要求....通过正则匹配出来的数据是a
    变量名：%s 不符合要求

    进程已结束，退出代码 0

匹配结尾
```python
import re

def main():
    names = ["age","_age","1age","age1","a_age","age_1_","age!","a#123","_____"]
    for name in names:
        ret = re.match(r"[a-zA-Z][a-zA-Z0-9_]*$",name)  # 匹配a-zA-Z开头一直到a-zA-Z0-9_结尾
        if ret:
                print("变量名：%s 符合要求....通过正则匹配出来的数据是%s" % (name,ret.group()))
        else:
                print("变量名：%s 不符合要求")

if __name__ == "__main__":
    main()

```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    变量名：age 符合要求....通过正则匹配出来的数据是age
    变量名：%s 不符合要求
    变量名：%s 不符合要求
    变量名：age1 符合要求....通过正则匹配出来的数据是age1
    变量名：a_age 符合要求....通过正则匹配出来的数据是a_age
    变量名：age_1_ 符合要求....通过正则匹配出来的数据是age_1_
    变量名：%s 不符合要求
    变量名：%s 不符合要求
    变量名：%s 不符合要求

    进程已结束，退出代码 0

转义匹配
```python
import re

def main():
    email = input("请输入一个邮箱地址: ")

    ret = re.match(r"[a-zA-Z0-9_]{4,20}@163\.com$",email)
    if ret:
        print("%s 符合要求" % email)
    else:
        print("%s 不符合要求"  % email)

if __name__ == "__main__":
    main()
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    请输入一个邮箱地址: laowang@163acom
    laowang@163acom 不符合要求

    进程已结束，退出代码 0

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    请输入一个邮箱地址: laowang@163.com
    laowang@163.com 符合要求

    进程已结束，退出代码 0

| 匹配左右任意一个表达式
```python
import  re
# 判断多个类型邮箱
a = re.match(r"[a-zA-Z0-9_]{4,20}@(163|126)\.com$","laowang@126.com")
print(a.group())
b = re.match(r"[a-zA-Z0-9_]{4,20}@(163|126)\.com$","laowang@163.com")
print(b.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    laowang@126.com
    laowang@163.com

    进程已结束，退出代码 0

(ab) 分组-将括号中字符作为一个分组 可以使用group(1)或者group(2)取出
```python
import  re
# 判断多个类型邮箱  使用分组()保存数据
a = re.match(r"[a-zA-Z0-9_]{4,20}@(163|126)\.com$","laowang@126.com")
print(a.group(1))
b = re.match(r"([a-zA-Z0-9_]{4,20})@(163|126)\.com$","laowang@163.com")
print(b.group(1))
```
运行结果:

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    126
    laowang

    进程已结束，退出代码 0

\num 引用分组num匹配到的字符
```python
import  re
# \num 引用分组num匹配到的字符
html_str = "<h1>hello test</h1>"
# 可以使用 \num  判断<h1> 是否是一对
a = re.match(r"<(\w*)>.*</\1>",html_str)
print(a.group())

html_str1 = "<body><h1>hello test</h1></body>"
# body 是第一个分组   h1 是第二个 所以 写为</\2></\1>
b = re.match(r"<(\w*)><(\w*)>.*</\2></\1>",html_str1)
print(b.group())

# 扩展 为分组命名
# (?P<name>)  分组起名
#（?P=name）引用别名为name分组匹配到的字符串
html_str3 = "<body><h1>hello test</h1></body>"
c = re.match(r"<(?P<NAME1>\w*)><(?P<NAME2>\w*)>.*</(?P=NAME2)></(?P=NAME1)>",html_str1)
print(c.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test.py
    <h1>hello test</h1>
    <body><h1>hello test</h1></body>
    <body><h1>hello test</h1></body>

    进程已结束，退出代码 0

## re模块高级用法
re.match(pattern, string, flags=0) 从字符串的起始位置匹配，如果起始位置匹配不成功的话，match()就返回none

re.search(pattern, string, flags=0) 扫描整个字符串并返回第一个成功的匹配
```python
import re
ret = re.search(r"\d+","阅读次数为 9999")  # 返回第一个成功的匹配
print(ret.group())
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test2.py
    9999

    进程已结束，退出代码 0

re.findall(pattern, string, flags=0) 找到RE匹配的所有字符串，并把他们作为一个列表返回
```python
import re
ret = re.findall(r"\d+","python = 9999, c = 7890, c++ = 12345")
print(ret)
```
运行结果：

    D:\python练习\venv\Scripts\python.exe E:/程序代码/hexo/test2.py
    ['9999', '7890', '12345']

    进程已结束，退出代码 0
    
re.finditer(pattern, string, flags=0) 找到RE匹配的所有字符串，并把他们作为一个迭代器返回
```python
```

re.sub(pattern, repl, string, count=0, flags=0) 替换匹配到的字符串
```python
```