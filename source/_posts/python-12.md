---
title: python基础之模块导入
tags:
  - python
  linux
categories: 开发
date: 2020-06-18 16:59:23
---
## 模块的概念

    模块是python程序架构的一个核心概念
    每一个以扩展名py结尾的python源代码文件都是一个模块
    模块名同样也是一个标识符。需要符合标识符的命名规则
    在模块中定义的全局变量、函数、类都是提供给外界直接使用的工具
    模块就好比是工具包，要想使用这个工具包中的工具，就需要先导入这个模块

## import语句
当解释器遇到 import 语句，如果模块在当前的搜索路径就会被导入,搜索路径是一个解释器会先进行搜索的所有目录的列表。如想要导入模块 support，需要把命令放在脚本的顶端
<br/>想使用 Python 源文件，只需在另一个源文件里执行 import 语句，语法如下：<br/>

```python
# 提示：在导入模块时，每个导入应该独占一行
import 模块名1
import 模块名2
```
导入之后,通过模块名.  使用模块提供的工具--全局变量、函数、类

## import导入指定别名
import  模块名  as  模块别名
```python
import  test_1  as  T1
T1.hello()
```
## from ... import 局部导入
如果希望从某一模块中，导入部分工具，就可以使用from...import 的方式
<br/>from  模块名  import 工具名<br/>
使用from导入后  不需要通过模块名.   的方式调用

```python
from test_2  import hello
#直接调用  不需要通过模块名.   的方式调用
hello()
```
注意：如果两个模块，存在同名的函数，那么后导入模块的函数，会覆盖先导入的函数
```python
from ... import ... as ...
```
## 路径搜索和搜索路径
上面提到的都是导入同级目录下的模块,如果不在同一级目录下：
<br/>import  module_name 实际上是找 module_name.py文件,是文件就一定有路径<br/>
导入模块就是：找到.py文件的位置,把他执行一遍   使用sys.path.

```python
import sys
print(sys.path)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    ['E:\\资料\\python\\hexo', 'E:\\资料\\python', 'E:\\资料\\python\\面向对象\\模块', 'E:\\资料\\python\\自走棋', 'E:\\资料\\python\\飞机大战', 'D:\\软件下载\\python36.zip', 'D:\\软件下载\\DLLs', 'D:\\软件下载\\lib', 'D:\\软件下载', 'C:\\Users\\Administrator\\AppData\\Roaming\\Python\\Python36\\site-packages', 'D:\\软件下载\\lib\\site-packages', 'D:\\install\\PyCharm 2018.3.4\\helpers\\pycharm_matplotlib_backend']

    进程已结束，退出代码 0

里面的''指的是当前路径,这也是为何查找模块先从当前目录查找的原因
```python
import  os    # 导入os模块
print(os.path.abspath(__file__))   # 当前文件绝对路径
print(os.path.dirname(os.path.abspath(__file__)))  # 获取目录名
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    E:\资料\python\hexo\test2.py
    E:\资料\python\hexo

    进程已结束，退出代码 0

## 导入优化
```python
import module_test
# import 导入情况,如果重复调用,python就会重复查找,避免重复查找,可以使用from的方式导入
from module_test  import  test
