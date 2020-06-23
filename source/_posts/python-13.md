---
title: python基础之常用模块
tags:
  - python
  - Linux
categories: 开发
date: 2020-06-18 18:06:21
---
## 列出指定目录下的所有文件
```python
import  os
print(os.listdir("../hexo"))  # 列出指定目录下的所有文件并打印
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    ['test.py', 'test2.py']

    进程已结束，退出代码 0


## 删除文件
```python
import  os
print(os.listdir("../hexo")) 
os.remove("pwd.py")  # 删除pwd.py文件
print(os.listdir("../hexo")) 
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    ['pwd.py', 'test.py', 'test2.py']
    ['test.py', 'test2.py']

    进程已结束，退出代码 0

```python
import os
os.unlink("pwd.py")  # 删除pwd.py文件   与remove相同
```
## 重命名文件 
```python
import  os
print(os.listdir("../hexo"))
os.rename("test.py","numcount.py")  # (原来名字,新名字)
print(os.listdir("../hexo"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    ['test.py', 'test2.py']
    ['numcount.py', 'test2.py']

    进程已结束，退出代码 0

## 改变当前工作目录
```python
import  os
print(os.listdir("../hexo"))
os.chdir("../python")  # 切换工作目录至python下
print(os.listdir())
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    ['numcount.py', 'test2.py']
    ['video.html',  '自动偷取.js']

    进程已结束，退出代码 0

## 获取当前文件路径
```python
import  os
print(os.getcwd())
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    E:\资料\python\hexo

    进程已结束，退出代码 0

## 新建目录
```python
import  os
os.mkdir("../hexo/mkdir")  # 在hexo目录下创建名为mkdir的目录
print(os.listdir())
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    ['mkdir', 'numcount.py', 'test2.py']

    进程已结束，退出代码 0

## 删除空目录(删除非空目录,使用shutil.rmtree())
```python
import  os
os.rmdir("../hexo/mkdir")   # 删除名为mkdir的空目录
print(os.listdir())
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    ['numcount.py', 'test2.py']

    进程已结束，退出代码 0

## 创建多级目录
```python
import  os
os.makedirs("../hexo/mkdir/screen")  # 创建目录mkdir 并在mkdir下创建screen
print(os.listdir("mkdir"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    ['screen']

    进程已结束，退出代码 0

## 删除多级目录
```python
import  os
os.removedirs("../hexo/mkdir/screen")  # 删除递归目录
print(os.listdir())
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    ['numcount.py', 'test2.py']

    进程已结束，退出代码 0

## 获取文件属性
```python
import  os
print(os.stat("numcount.py"))   # 获取文件属性并打印
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    os.stat_result(st_mode=33206, st_ino=17732923533531673, st_dev=1744964457, st_nlink=1, st_uid=0, st_gid=0, st_size=171, st_atime=1592297075, st_mtime=1592297075, st_ctime=1591942241)

    进程已结束，退出代码 0

## 修改文件权限
```python
import  os
import stat   # 权限模块
print(os.stat("numcount.py").st_mode)
print(oct(os.stat("numcount.py").st_mode)[-3:])
os.chmod("numcount.py",stat.S_IWRITE)   
print(oct(os.stat("numcount.py").st_mode)[-3:])
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    33060
    444
    666

    进程已结束，退出代码 0

```python
# 常用权限
stat.S_ISVTX: Save text image after execution. 在执行之后保存文字和图片
stat.S_IREAD: Read by owner. 对于拥有者读的权限
stat.S_IWRITE: Write by owner. 对于拥有者写的权限
stat.S_IEXEC: Execute by owner. 对于拥有者执行的权限
stat.S_IRWXU: Read, write, and execute by owner. 对于拥有者读写执行的权限
stat.S_IRUSR: Read by owner. 对于拥有者读的权限
stat.S_IWUSR: Write by owner. 对于拥有者写的权限
stat.S_IXUSR: Execute by owner. 对于拥有者执行的权限
stat.S_IRWXG: Read, write, and execute by group. 对于同组的人读写执行的权限
stat.S_IRGRP: Read by group. 对于同组读的权限
stat.S_IWGRP: Write by group. 对于同组写的权限
stat.S_IXGRP: Execute by group. 对于同组执行的权限
stat.S_IRWXO: Read, write, and execute by others. 对于其他组读写执行的权限
stat.S_IROTH: Read by others. 对于其他组读的权限
stat.S_IWOTH: Write by others. 对于其他组写的权限
stat.S_IXOTH: Execute by others. 对于其他组执行的权限
```
## 修改文件时间戳
参数

    path -- 文件路径

    times -- 如果时间是 None, 则文件的访问和修改设为当前时间 。 否则, 时间是一个 2-tuple数字, (atime, mtime) 用来分别作为访问和修改的时间。

```python
import  os
import time
print(os.stat("numcount.py").st_mtime)  #  打印修改前时间戳
os.utime("numcount.py",None)   # 将时间戳修改为当前时间
print(os.stat("numcount.py").st_mtime)
print(time.time())    # 查看当前时间戳
```
```python
import  os
import time
print(os.stat("numcount.py").st_mtime) #  打印修改前时间戳
os.utime("numcount.py",(1592534641,1592534641))  # 将时间戳修改为指定时间
print(os.stat("numcount.py").st_mtime)
print(time.time())
```
## 执行操作系统命令
```python
>>> import os       # 导入模块
>>> os.system("ls")    # 执行ls操作
db              nohup.out                 update.js  find_date.sh    qq.sh                
0
>>> os.system("pwd")   # 执行pwd操作
/home/sgsm
0
```
## os.path模块
os.path.split(filename) 将文件路径和文件名分割(会将最后一个目录作为文件名而分离)
```python
import  os
print(os.path.split("numcount.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    ('', 'numcount.py')

    进程已结束，退出代码 0

os.path.splitext(filename) 将文件路径和文件扩展名分割成一个元组
```python
import  os
print(os.path.splitext("numcount.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    ('numcount', '.py')

    进程已结束，退出代码 0

os.path.dirname(filename) 返回文件路径的目录部分
```python
import  os
print(os.path.abspath(__file__))  # 查看文件的绝对路径 
print(os.path.dirname("E:\资料\python\hexo\\test2.py"))   # 因为\t是特殊符号所以需要转义
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    E:\资料\python\hexo\test2.py
    E:\资料\python\hexo

    进程已结束，退出代码 0

os.path.basename(filename) 返回文件路径的文件名部分
```python
import  os
print(os.path.abspath(__file__))
print(os.path.basename("E:\资料\python\hexo\\test2.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    E:\资料\python\hexo\test2.py
    test2.py

    进程已结束，退出代码 0

os.path.join(dirname,basename) 将文件路径和文件名凑成完整文件路径
```python
import  os
print(os.path.abspath(__file__))
print(os.path.join("E:\资料\python\hexo\\test2.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    E:\资料\python\hexo\test2.py
    E:\资料\python\hexo\test2.py

    进程已结束，退出代码 0

os.path.abspath(name) 获得绝对路径   (__file__) 代表当前文件
```python
import  os
print(os.path.abspath(__file__))
print(os.path.abspath("E:\资料\python\hexo\\numcount.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    E:\资料\python\hexo\test2.py
    E:\资料\python\hexo\numcount.py

    进程已结束，退出代码 0

os.path.splitunc(path) 把路径分割为挂载点和文件名
```python
import  os
print(os.path.abspath(__file__))
print(os.path.splitunc("E:\资料\python\hexo\\numcount.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    E:\资料\python\hexo\test2.py
    ('', 'E:\\资料\\python\\hexo\\numcount.py')

    进程已结束，退出代码 0

os.path.normpath(path) 规范path字符串形式
```python
import  os
print(os.path.abspath(__file__))
print(os.path.normpath("E:\资料\python\hexo\\numcount.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    E:\资料\python\hexo\test2.py
    E:\资料\python\hexo\numcount.py

    进程已结束，退出代码 0

os.path.exists() 判断文件或目录是否存在
```python
import  os
print(os.path.exists("E:\资料\python\hexo\\numcount.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    True

    进程已结束，退出代码 0

os.path.isabs() 如果path是绝对路径，返回True
```python
import  os 
print(os.path.isabs("../numcount.py"))     # 相对路径
print(os.path.isabs("E:\资料\python\hexo\\numcount.py"))  # 绝对路径
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    False
    True

    进程已结束，退出代码 0

os.path.realpath(path) #返回path的真实路径  (realpath 返回的是 使用软链 的真实地址   abspath 返回目标地址)
```python
import  os
print(os.path.abspath("numcount.py"))
print(os.path.realpath("numcount.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    E:\资料\python\hexo\numcount.py
    E:\资料\python\hexo\numcount.py

    进程已结束，退出代码 0

os.path.relpath(path[, start]) #从start开始计算相对路径 
```python
import  os
print(os.path.relpath("E:\资料\python\hexo\\numcount.py", "E:\资料"))  # 从"E:\资料"开始计算
print(os.path.relpath("E:\资料\python\hexo\\numcount.py", ""))  # 如果不指定 默认从当前位置计算
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    python\hexo\numcount.py
    numcount.py

    进程已结束，退出代码 0

os.path.normcase(path) #转换path的大小写和斜杠
```python
import  os
print(os.path.normcase("E:\资料\python\hexo\\numcount.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    e:\资料\python\hexo\numcount.py

    进程已结束，退出代码 0

os.path.isdir() 判断name是不是一个目录，name不是目录就返回false
```python
import  os
print(os.path.isdir("E:\资料\python\hexo\\numcount.py"))
print(os.path.isdir("E:\资料\python\hexo"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    False
    True

    进程已结束，退出代码 0

os.path.isfile() 判断name是不是一个文件，不存在返回false
```python
import  os
print(os.path.isfile("E:\资料\python\hexo\\numcount.py"))
print(os.path.isfile("E:\资料\python\hexo"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    True
    False

    进程已结束，退出代码 0

os.path.islink() 判断文件是否连接文件,返回boolean
```python
import  os
print(os.path.islink("E:\资料\python\hexo\\numcount.py"))
print(os.path.islink("E:\资料\python\hexo"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    False
    False

    进程已结束，退出代码 0

os.path.ismount() 指定路径是否存在且为一个挂载点，返回boolean
```python
import  os
print(os.path.ismount("E:\资料\python\hexo\\numcount.py"))
print(os.path.ismount("E:\资料\python\hexo"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    False
    False

    进程已结束，退出代码 0

os.path.samefile() 是否相同路径的文件，返回boolean
```python
import  os
print(os.path.samefile("E:\资料\python\hexo\\numcount.py","E:\资料\python\hexo"))
print(os.path.samefile("E:\资料\python\hexo\\numcount.py","E:\资料\python\hexo\\numcount.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    False
    True

    进程已结束，退出代码 0

os.path.getatime() 返回最近访问时间 浮点型
```python
import  os
print(os.path.getatime("E:\资料\python\hexo\\numcount.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    1592534641.0

    进程已结束，退出代码 0

os.path.getmtime() 返回上一次修改时间 浮点型
```python
import  os
print(os.path.getmtime("E:\资料\python\hexo\\numcount.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    1592534641.0

    进程已结束，退出代码 0

os.path.getctime() 返回文件创建时间 浮点型
```python
import  os
print(os.path.getctime("E:\资料\python\hexo\\numcount.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    1591942241.7330246

    进程已结束，退出代码 0

os.path.getsize() 返回文件大小 字节单位
```python
import  os
print(os.path.getsize("E:\资料\python\hexo\\numcount.py"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    171

    进程已结束，退出代码 0

```python
os.path.commonprefix(list) #返回list(多个路径)中，所有path共有的最长的路径
os.path.lexists #路径存在则返回True,路径损坏也返回True
os.path.expanduser(path) #把path中包含的”~”和”~user”转换成用户目录
os.path.expandvars(path) #根据环境变量的值替换path中包含的”$name”和”${name}”
os.path.sameopenfile(fp1, fp2) #判断fp1和fp2是否指向同一文件
os.path.samestat(stat1, stat2) #判断stat tuple stat1和stat2是否指向同一个文件
os.path.splitdrive(path) #一般用在windows下，返回驱动器名和路径组成的元组
os.path.walk(path, visit, arg) #遍历path，给每个path执行一个函数详细见手册
os.path.supports_unicode_filenames() 设置是否支持unicode路径名
```

## stat模块
```python
描述os.stat()返回的文件属性列表中各值的意义
fileStats = os.stat(path) 获取到的文件属性列表
fileStats[stat.ST_MODE] 获取文件的模式
fileStats[stat.ST_SIZE] 文件大小
fileStats[stat.ST_MTIME] 文件最后修改时间
fileStats[stat.ST_ATIME] 文件最后访问时间
fileStats[stat.ST_CTIME] 文件创建时间
stat.S_ISDIR(fileStats[stat.ST_MODE]) 是否目录
stat.S_ISREG(fileStats[stat.ST_MODE]) 是否一般文件
stat.S_ISLNK(fileStats[stat.ST_MODE]) 是否连接文件
stat.S_ISSOCK(fileStats[stat.ST_MODE]) 是否COCK文件
stat.S_ISFIFO(fileStats[stat.ST_MODE]) 是否命名管道
stat.S_ISBLK(fileStats[stat.ST_MODE]) 是否块设备
stat.S_ISCHR(fileStats[stat.ST_MODE]) 是否字符设置
```

## sys模块
```python
sys.argv 命令行参数List，第一个元素是程序本身路径 
sys.path 返回模块的搜索路径，初始化时使用PYTHONPATH环境变量的值 
sys.modules.keys() 返回所有已经导入的模块列表
sys.modules 返回系统导入的模块字段，key是模块名，value是模块 
sys.exc_info() 获取当前正在处理的异常类,exc_type、exc_value、exc_traceback当前处理的异常详细信息
sys.exit(n) 退出程序，正常退出时exit(0)
sys.hexversion 获取Python解释程序的版本值，16进制格式如：0x020403F0
sys.version 获取Python解释程序的版本信息
sys.platform 返回操作系统平台名称
sys.stdout 标准输出
sys.stdout.write(‘aaa‘) 标准输出内容
sys.stdout.writelines() 无换行输出
sys.stdin 标准输入
sys.stdin.read() 输入一行
sys.stderr 错误输出
sys.exc_clear() 用来清除当前线程所出现的当前的或最近的错误信息 
sys.exec_prefix 返回平台独立的python文件安装的位置 
sys.byteorder 本地字节规则的指示器，big-endian平台的值是‘big‘,little-endian平台的值是‘little‘ 
sys.copyright 记录python版权相关的东西 
sys.api_version 解释器的C的API版本 
sys.version_info ‘final‘表示最终,也有‘candidate‘表示候选，表示版本级别，是否有后继的发行 
sys.getdefaultencoding() 返回当前你所用的默认的字符编码格式 
sys.getfilesystemencoding() 返回将Unicode文件名转换成系统文件名的编码的名字 
sys.builtin_module_names Python解释器导入的内建模块列表 
sys.executable Python解释程序路径 
sys.getwindowsversion() 获取Windows的版本 
sys.stdin.readline() 从标准输入读一行，sys.stdout.write(“a”) 屏幕输出a
sys.setdefaultencoding(name) 用来设置当前默认的字符编码(详细使用参考文档) 
sys.displayhook(value) 如果value非空，这个函数会把他输出到sys.stdout(详细使用参考文档)
```

## datetime,date,time模块
datetime.date.today() 本地日期对象,(用str函数可得到它的字面表示(2014-03-24))
```python
import time
import datetime
now = datetime.date.today()
print(now)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    2020-06-19

    进程已结束，退出代码 0

datetime.date.isoformat(obj) 当前[年-月-日]字符串表示(2014-03-24)
```python
import datetime
time = datetime.date.today()
print(datetime.date.isoformat(time))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    2020-06-19

    进程已结束，退出代码 0

datetime.date.fromtimestamp() 返回一个日期对象，参数是时间戳,返回 [年-月-日]
```python
import datetime
time = datetime.date.fromtimestamp(1592481600)
print(time)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    2020-06-18

    进程已结束，退出代码 0

datetime.date.weekday(obj) 返回一个日期对象的星期数,周一是0
```python
import datetime
time = datetime.date.fromtimestamp(1592481600)
print(time.weekday())    #  打印当前时间的星期数   周一是0  
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    3

    进程已结束，退出代码 0

datetime.date.isoweekday(obj) 返回一个日期对象的星期数,周一是1
```python
import datetime
time = datetime.date.fromtimestamp(1592481600)
print(time.isoweekday())
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    4

    进程已结束，退出代码 0

datetime.date.isocalendar(obj) 把日期对象返回一个带有年 第几周 周几的元组
```python
import datetime
time = datetime.date.today()
print(time.isocalendar())
print(datetime.date.isocalendar(time))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    (2020, 25, 5)
    (2020, 25, 5)

    进程已结束，退出代码 0

## datetime对象：
datetime.datetime.today() 返回一个包含本地时间(含微秒数)的datetime对象 2014-03-24 23:31:50.419000
```python
import datetime
time = datetime.datetime.today()
print(time)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    2020-06-19 17:58:32.643581

    进程已结束，退出代码 0

datetime.datetime.now([tz]) 返回指定时区的datetime对象   默认当前时区 2014-03-24 23:31:50.419000
```python
import datetime
time = datetime.datetime.now()
print(time)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    2020-06-19 18:01:00.380030

    进程已结束，退出代码 0

datetime.datetime.utcnow() 返回一个零时区的datetime对象   
```python
import datetime
time = datetime.datetime.utcnow()
print(time)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    2020-06-19 10:03:02.280002

    进程已结束，退出代码 0

datetime.fromtimestamp(timestamp[,tz]) 按时间戳返回一个datetime对象，可指定时区,可用于strftime转换为日期表示 
```python
import datetime
import  time
time = datetime.datetime.fromtimestamp(1592481600)
print(time)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    2020-06-18 20:00:00

    进程已结束，退出代码 0

datetime.utcfromtimestamp(timestamp) 按时间戳返回一个UTC-datetime对象
```python
import datetime
import  time
time = datetime.datetime.utcfromtimestamp(1592481600)
print(time)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    2020-06-18 12:00:00

    进程已结束，退出代码 0

datetime.datetime.strptime(‘2014-03-16 12:21:21‘,”%Y-%m-%d %H:%M:%S”) 将字符串转为datetime对象
<br/>可以自定义格式     就是将时间戳转换成你想要的时间格式<br/>

```python
import datetime
time = datetime.datetime.strptime("2020-05-01 10:00:00","%Y-%m-%d %H:%M:%S")
print(time)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    2020-05-01 10:00:00

    进程已结束，退出代码 0

datetime.datetime.strftime(datetime.datetime.now(), ‘%Y%m%d %H%M%S‘) 将datetime对象转换为str表示形式
<br/>可以自定义格式     就是将时间戳转换成你想要的时间格式<br/>

```python
import datetime
import  time
time = datetime.datetime.fromtimestamp(1592481600)
print(time.strftime("%Y%m%d %H%M%S"))
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    20200618 200000

    进程已结束，退出代码 0

datetime.date.today().timetuple() 转换为时间戳datetime元组对象，可用于转换时间戳
```python
import datetime
import  time
time = datetime.datetime.today(1592481600)
print(time.timetuple())
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    time.struct_time(tm_year=2020, tm_mon=6, tm_mday=18, tm_hour=20, tm_min=0, tm_sec=0, tm_wday=3, tm_yday=170, tm_isdst=-1)

    进程已结束，退出代码 0

datetime.datetime.now().timetuple()
```python
import datetime
import  time
time = datetime.datetime.now()
print(time.timetuple())
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    time.struct_time(tm_year=2020, tm_mon=6, tm_mday=19, tm_hour=19, tm_min=15, tm_sec=6, tm_wday=4, tm_yday=171, tm_isdst=-1)

    进程已结束，退出代码 0


time.mktime(timetupleobj) 将datetime元组对象转为时间戳
```python
import datetime
import  time
aa = (2020, 6, 19, 19, 15, 6,4, 171,-1)  # 定义时间元祖
time = time.mktime(aa)      # 转换元祖为时间戳
print(time)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    1592565306.0

    进程已结束，退出代码 0

time.time() 当前时间戳
```python
import datetime
import  time
time = time.time()
print(time)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    1592565722.2161117

    进程已结束，退出代码 0

time.localtime()  当前时间  以元祖格式显示
```python
import datetime
import  time
time = time.localtime()
print(time)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    time.struct_time(tm_year=2020, tm_mon=6, tm_mday=19, tm_hour=19, tm_min=22, tm_sec=50, tm_wday=4, tm_yday=171, tm_isdst=0)

    进程已结束，退出代码 0

time.gmtime()  和 time.localtime()  时区不同
```python
import datetime
import  time
qq = time.gmtime()
print(qq)
aa = time.localtime()
print(aa)
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    time.struct_time(tm_year=2020, tm_mon=6, tm_mday=19, tm_hour=11, tm_min=31, tm_sec=3, tm_wday=4, tm_yday=171, tm_isdst=0)
    time.struct_time(tm_year=2020, tm_mon=6, tm_mday=19, tm_hour=19, tm_min=31, tm_sec=3, tm_wday=4, tm_yday=171, tm_isdst=0)

    进程已结束，退出代码 0


## datetime模块案例
```python
import datetime
import  time

# datetime.date
localtime = datetime.date.today()   # 获取当前时间
print(localtime.strftime("%Y-%m-%d"))   # 打印当前时间的年月日
print(localtime.fromtimestamp(1592681600))  # 打印时间戳的年月日
print(localtime.isoweekday())  # 查看今天周几    周一为1
print(localtime.weekday())   # 查看今天周几  周一为0
print(localtime.isocalendar()) # 以元祖格式显示 年  第几周   周几
print(localtime.isoformat())  # 打印当前时间的年月日

# datetime.datetime
d_localtime = datetime.datetime.now() # 获取当前时间
print(d_localtime)  # 打印当前时间(包含微秒)
print(d_localtime.year,"-",d_localtime.month,"-",d_localtime.day)  # 打印年月日
print(d_localtime.hour,":",d_localtime.minute,":",d_localtime.second)   # 打印时分秒
print(datetime.datetime.utcnow()) # 打印零时区的当前时间
print(d_localtime.strptime("2020-10-01 10:10:10","%Y-%m-%d %H:%M:%S"))  # 打印自定义时间
print(d_localtime.strftime("%H:%M:%S")) # 打印自定义格式
print(d_localtime.timetuple())  # 以元祖格式显示

# time
now = aa = (2020, 6, 19, 19, 15, 6,4, 171,-1)  # 定义时间元祖
print(time.mktime(now))   # 元祖转换为时间戳
print(time.time())  # 打印当前时间戳
print(time.localtime())  #  打印当前时间 以元祖的格式
print(time.gmtime())  # 打印零时区的时间   以元祖的格式
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/test2.py
    2020-06-20
    2020-06-21
    6
    5
    (2020, 25, 6)
    2020-06-20
    2020-06-20 09:37:43.631672
    2020 - 6 - 20
    9 : 37 : 43
    2020-06-20 01:37:43.631672
    2020-10-01 10:10:10
    09:37:43
    time.struct_time(tm_year=2020, tm_mon=6, tm_mday=20, tm_hour=9, tm_min=37, tm_sec=43, tm_wday=5, tm_yday=172, tm_isdst=-1)
    1592565306.0
    1592617063.6546738
    time.struct_time(tm_year=2020, tm_mon=6, tm_mday=20, tm_hour=9, tm_min=37, tm_sec=43, tm_wday=5, tm_yday=172, tm_isdst=0)
    time.struct_time(tm_year=2020, tm_mon=6, tm_mday=20, tm_hour=1, tm_min=37, tm_sec=43, tm_wday=5, tm_yday=172, tm_isdst=0)

    进程已结束，退出代码 0

## hashilb,md5模块
hashlib.md5(‘md5_str‘).hexdigest() 对指定字符串md5加密
```python
import hashlib   # 导入模块
str = "test"  #  定义需要加密的字符串
str1 = hashlib.md5()  #  md5转码utf-8
str1.update(str.encode("utf-8"))  # 必须指定转码格式
print(str1.hexdigest()) # 加密字符串
print(str1.digest())
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/hexo.py
    098f6bcd4621d373cade4e832627b4f6
    b"\t\x8fk\xcdF!\xd3s\xca\xdeN\x83&'\xb4\xf6"

    进程已结束，退出代码 0

## random模块
```python
import random
# 产生0-1的随机浮点数
print(random.random())
# 产生指定范围内的随机浮点数
print(random.uniform(1,2))
# 产生指定范围内的随机整数
print(random.randint(5,10))
# 从一个指定步长的集合中产生随机数
print(random.randrange(10,30,5)) # (从10-30 步长为5 产生随机数 10，15,20,25,30)
# 从序列中获取一个随机元素  random.choice(sequence)
# 参数sequence表示一个有序类型。这里要说明 一下：sequence在python不是一种特定的类型，而是泛指一系列的类型。list, tuple, 字符串都属于sequence
str = "test","test2","test3","test4"
print(random.choice(str))

# 将一个列表中的元素打乱
list = ["a","b","c","d","e","f","g"]
random.shuffle(list)
print(list)

# 从序列中随机获取指定长度的片段
list = ["a","b","c","d","e","f","g"]
print(random.sample(list,4))   # 打印序列中的前四个
num = ["1","2","3","4","5","6"]
print(random.sample(num,3))  # 打印序列中的前三个
```
运行结果：

    D:\软件下载\python.exe E:/资料/python/hexo/hexo.py
    0.630213372552819
    1.6560316051826613
    7
    25
    test2
    ['d', 'g', 'b', 'f', 'e', 'a', 'c']
    ['g', 'b', 'e', 'f']
    ['1', '6', '5']

    进程已结束，退出代码 0


