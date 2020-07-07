---
title: python基础之文件操作
tags:
  - python
  - linux
categories: 开发
date: 2020-06-28 11:20:38
---
## 打开文件
在python3中,打开文件的函数是：
```python
open(file, mode='r', buffering=None, encoding=None, errors=None, newline=None, closefd=True)
```
参数说明：

    file：文件名
    mode：打开模式,默认只读模式
    encoding：打开文件的编码方式

模式介绍：

    r：只读模式(默认)
    w：只写模式,如果文件不存在就创建,如果存在,写入的数据就会覆盖原来的数据
    b：二进制模式
    t：文本模式
    +：可读可写模式
    a：追加模式,如果文件存在则文件指针指向文件末尾(追加数据),如果不存在就创建
    r+：读追加模式,先读,在追加
    w+：读写模式,先写,意味着原本内容丢失,再读

文件使用完毕后必须关闭： 文件指针.close() 

## 读操作
file.txt文件内容

```bash
my
sas
aaa
test
中文
中文
葫芦娃
```
reads()是读出全部内容
```python
print("r".center(50,'-'))
f=open("file.txt",encoding="utf-8")
print(f.read())
f.close()
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    ------------------------r-------------------------
    my
    sas
    aaa
    test
    中文
    中文
    葫芦娃

    进程已结束，退出代码 0

readline()是读出一行
```python
print("r".center(50,'-'))
f=open("file.txt",encoding="utf-8")
print(f.readline())
f.close()
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    ------------------------r-------------------------
    my


    进程已结束，退出代码 0

readlines()是读出全部内容,并整理成一个列表
```python
print("r".center(50,'-'))
f=open("file.txt",encoding="utf-8")
print(f.readlines())
f.close()
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test.py
    ------------------------r-------------------------
    ['my\n', 'sas\n', 'aaa\n', 'test\n', '中文\n', '中文\n', '葫芦娃']

    进程已结束，退出代码 0

调用read()会一次性读取文件的全部内容，如果文件有20G，内存就爆了，所以，要保险起见，可以反复调用read(size)方法，每次最多读取size个字节的内容。另外，调用readline()可以每次读取一行内容，调用readlines()一次读取所有内容并按行返回list。因此，要根据需要决定怎么调用。

如果文件很小，read()一次性读取最方便；如果不能确定文件大小，反复调用read(size)比较保险；如果是配置文件，调用readlines()最方便：
## 写操作
写文件和读文件是一样的，唯一区别是调用open()函数时，传入标识符'w'或者'wb'表示写文本文件或写二进制文件：
```python
>>> f = open('E:\python\python\test.txt', 'w')
>>> f.write('Hello, python!')
>>> f.close()
```
## with
为了便捷的关闭文件，python增加了with功能，当with体执行完将自动关闭打开的文件：
```python
with open("file.txt","r+",encoding="utf-8") as f: ##将自动执行f.close()
 f.write("金刚")
```


多个文件的读写
```python

with open('C:\Desktop\text.txt','r') as f:
    with open('C:\Desktop\text1.txt','r') as f1:
        with open('C:\Desktop\text2.txt','r') as f2　　　　　　
        ........　　　　　　　
        ........　　　　　　　
        ........
```

```python

with open(''C:\Desktop\text.txt','r') as f:
........
with open(''C:\Desktop\text1.txt','r') as f1:
........
with open('C:\Desktop\text2.txt','r') as f2:
........
```