---
title: linux三剑客以及其他工具详解
tags:
  - shell
  - linux
categories: 运维
date: 2020-07-14 14:06:22
---
## cat
-E 显示行结束符$
```bash
[sgsm@localhost test2]$ cat  -E  test 
zhi dao xian zai wo dou mei you ming bai $
wei he  dang chu xuan ze li kai $
[sgsm@localhost test2]$ 
```
-n 对显示出的每一行进行编号(包含空行)
```bash
[sgsm@localhost test2]$ cat  -n  test 
     1  zhi dao xian zai wo dou mei you ming bai 
     2  wei he  dang chu xuan ze li kai 
     3
     4
[sgsm@localhost test2]$ 
```
-A 显示所有控制符(^I 代表table键)
```bash
[sgsm@localhost test2]$ cat   -A test
zhi dao xian zai wo dou mei you ming bai$
wei he ^I dang chu xuan ze li kai$
$
$
[sgsm@localhost test2]$ 
```
-b  非空行编号
```bash
[sgsm@localhost test2]$ cat  -n test 
     1  zhi dao xian zai wo dou mei you ming bai
     2  wei he   dang chu xuan ze li kai
     3
     4
[sgsm@localhost test2]$ 
[sgsm@localhost test2]$ cat  -b test  
     1  zhi dao xian zai wo dou mei you ming bai
     2  wei he   dang chu xuan ze li kai


[sgsm@localhost test2]$ 
```
-s 压缩连续的空行成一行
```bash
[sgsm@localhost test2]$ cat  -n test 
     1  zhi dao xian zai wo dou mei you ming bai
     2  wei he   dang chu xuan ze li kai
     3
     4
[sgsm@localhost test2]$ cat  -s test  
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai

[sgsm@localhost test2]$ 
```
## more
(空格一页一页的翻,回车一行一行的翻) q 退出
less ：一页一页地查看文件
## head
-c #：指定获取前#字节
```bash
[sgsm@localhost test2]$ head  -c 10   test 
zhi dao xi[sgsm@localhost test2]$ 
[sgsm@localhost test2]$ head  -c 15   test  
zhi dao xian za[sgsm@localhost test2]$ 
[sgsm@localhost test2]$ head  -c 20   test   
zhi dao xian zai wo [sgsm@localhost test2]$ 
[sgsm@localhost test2]$
```
-n#：指定获取前#行  (n 可以省略    -#)
```bash
[sgsm@localhost test2]$ head  -n  10  /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
uucp:x:10:14:uucp:/var/spool/uucp:/sbin/nologin
[sgsm@localhost test2]$ 
[sgsm@localhost test2]$ head  -10  /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
uucp:x:10:14:uucp:/var/spool/uucp:/sbin/nologin
```
## tail
-c#：指定获取后#字节
```bash
[sgsm@localhost test2]$ tail  -c 10  test 
 li kai
[sgsm@localhost test2]$ cat test 
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai
[sgsm@localhost test2]$ 
```
-n#：指定获取后#行 (n 可以省略    -#)
```bash
[sgsm@localhost test2]$ tail -n 10  /etc/fstab 
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/VolGroup-lv_root /                       ext4    defaults        1 1
UUID=f027c061-3a2c-463f-aadb-6019110bb2ae /boot                   ext4    defaults        1 2
/dev/mapper/VolGroup-lv_home /home                   ext4    defaults        1 2
/dev/mapper/VolGroup-lv_swap swap                    swap    defaults        0 0
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
[sgsm@localhost test2]$ 
[sgsm@localhost test2]$ tail -10  /etc/fstab   
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/VolGroup-lv_root /                       ext4    defaults        1 1
UUID=f027c061-3a2c-463f-aadb-6019110bb2ae /boot                   ext4    defaults        1 2
/dev/mapper/VolGroup-lv_home /home                   ext4    defaults        1 2
/dev/mapper/VolGroup-lv_swap swap                    swap    defaults        0 0
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
[sgsm@localhost test2]$ 
```
-f：跟踪显示文件新追加的内容,常用日志监控
```bash
# 常用组合 tail  -f  -n #  filename
tail -f -n 30 *07-10*
```
## cut
-d 指名分隔符,默认tab    
-f #  ：第#个字段
```bash
[sgsm@localhost test2]$ cut -d'/' -f3  /etc/passwd
bin
sbin
sbin
adm:
spool
bin
sbin
sbin
spool
sbin
games:
ftp:
[sgsm@localhost test2]$ 
```
#,#,#：离散的多个字段,例如1,3,7
```bash
[sgsm@localhost test2]$ cut -d' ' -f1,2,3 test 
zhi dao xian
wei he 
but i no

[sgsm@localhost test2]$ 
```
#-#：连续的多个字段,例如1-4(也可以和离散字段一起使用例如：1-4,7)
```bash
[sgsm@localhost test2]$ cut -d' ' -f1-4 test      
zhi dao xian zai
wei he   dang
but i no caer

[sgsm@localhost test2]$ 
```
-c ：按字符切割
```bash
[sgsm@localhost test2]$ cut -c 5-10 test           
dao xi
he       d
i no c

[sgsm@localhost test2]$ 
```
## wc
```bash
# 显示格式： 行数    总单次数   总字符数    文件
[sgsm@localhost test2]$ wc  test    
 4 22 90 test
[sgsm@localhost test2]$ 
```
选项：-l  只计数行数(包含空行)
```bash
[sgsm@localhost test2]$ wc -l test 
4 test
[sgsm@localhost test2]$ 
```
-w 只计数单词总数
```bash
[sgsm@localhost test2]$ wc -w  test 
22 test
[sgsm@localhost test2]$ 
```
-c 只计数字节总数
```bash
[sgsm@localhost test2]$ wc -c test  
90 test
[sgsm@localhost test2]$ 
```
-m 只计数字符总数
```bash
[sgsm@localhost test2]$ wc -m test  
90 test
[sgsm@localhost test2]$ 
```
## sort
文本排序: 把整理过的文本显示在输出,不改变原始文件
```bash
# 默认按照首字母排序
[sgsm@localhost test2]$ sort  test 

but i no caer 
wei he   dang chu xuan ze li kai
zhi dao xian zai wo dou mei you ming bai
[sgsm@localhost test2]$ 
```
常用选项 -r  执行反方向(由上至下)整理
```bash
[sgsm@localhost test2]$ sort -r test 
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai
but i no caer
[sgsm@localhost test2]$ 
```
-n  执行按数字大小整理
```bash
[sgsm@localhost test2]$ sort  -n  aa 
1234
1253
1255
2353
4321
5678
6456
[sgsm@localhost test2]$ 
```
-f   选项忽略字符串中的字符大小写
-u   删除输出中的重复行(重复行全部不显示)
-t   c 使用c作为字段界定符
-k   X 按照使用c字符分隔的X列来整理能够使用多次。
```bash
# 以空格分割  排序第三段 
[sgsm@localhost test2]$ sort  -t" " -k3  aa   
4 3 21
1 2 34
1 2 53
2 3 53
1 2 55
6 4 56
5 6 78
[sgsm@localhost test2]$ sort  -t" " -k3  -r  aa     # 以相反方式显示第三段的排序
5 6 78
6 4 56
1 2 55
2 3 53
1 2 53
1 2 34
4 3 21
[sgsm@localhost test2]$ 
```
## uniq
uniq -c 显示每行重复出现的次数
```bash
[sgsm@localhost test2]$ uniq -c  test 
      1 zhi dao xian zai wo dou mei you ming bai
      1 wei he   dang chu xuan ze li kai
      1 but i no caer 
      2 san san
      2 xiao xiao
[sgsm@localhost test2]$ 
```
-d 仅显示重复过的行
```bash
[sgsm@localhost test2]$ uniq -d test   
san san
xiao xiao
[sgsm@localhost test2]$ cat test 
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai
but i no caer
san san
san san
xiao xiao
xiao xiao
[sgsm@localhost test2]$ 
```
-u 仅显示不曾重复的行
```bash
[sgsm@localhost test2]$ cat test 
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai
but i no caer
san san
san san
xiao xiao
xiao xiao
[sgsm@localhost test2]$ uniq -u test  
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai
but i no caer
```
常和sort命令一起配合使用,例： sort 文件名 |uniq -c
## grep
--color=auto: 对匹配到的文本着色显示
```bash
[sgsm@localhost test2]$ grep  --color=auto  ming    test.txt 
zhi dao xian zai wo dou mei you ming bai
[sgsm@localhost test2]$ 
```
-v: 显示不能够被pattern 匹配到的行(不包含匹配字的行)
```bash
[sgsm@localhost test2]$ grep  --color=auto  -v  ming    test.txt 
wei he   dang chu xuan ze li kai
but i no caer
san san
san san
xiao xiao
xiao xiao
[sgsm@localhost test2]$ 
```
-i: 忽略字符大小写
```bash
[sgsm@localhost test2]$ grep  --color=auto  -i MING   test.txt          
zhi dao xian zai wo dou mei you ming bai
[sgsm@localhost test2]$ 
```
-n: 显示匹配的行号
```bash
[sgsm@localhost test2]$ grep  --color=auto  -n san   test.txt          
4:san san
5:san san
[sgsm@localhost test2]$ 
```
-c: 统计匹配的行数
```bash
[sgsm@localhost test2]$ grep  --color=auto  -c san   test.txt  
2
[sgsm@localhost test2]$ 
```
-o: 仅显示匹配到的字符串
```bash
[sgsm@localhost test2]$ grep  -o san  test.txt                      
san
san
san
san
[sgsm@localhost test2]$ 
```
-A #:  匹配行的后#行
```bash
[sgsm@localhost test2]$ grep  -A 1 caer  test.txt     
but i no caer
san san
[sgsm@localhost test2]$
```
-B #： 匹配行的前#行
```bash
[sgsm@localhost test2]$ grep  -B 1 caer  test.txt  
wei he   dang chu xuan ze li kai
but i no caer
[sgsm@localhost test2]$
```
-C #:  匹配行的前后各#行
```bash
[sgsm@localhost test2]$ grep  -C 1 caer  test.txt  
wei he   dang chu xuan ze li kai
but i no caer
san san
[sgsm@localhost test2]$ 
```
-e ：实现多个选项间的逻辑 or 关系
```bash
[sgsm@localhost test2]$ grep  -e san  -e  xiao  test.txt          
san san
san san
xiao xiao
xiao xiao
[sgsm@localhost test2]$ 
```
-w ：整行匹配整个单词
```bash
[sgsm@localhost test2]$ grep   an  test.txt 
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai
but i no caer an
an an
san san
san san
[sgsm@localhost test2]$ grep  -w  an  test.txt 
but i no caer an
an an
[sgsm@localhost test2]$ 
```
-E ：使用扩展正则表达式
![](../1.png)
![](../2.png)
```bash
[sgsm@localhost test2]$ grep   --color=auto  b.[[:alnum:]]  test.txt    
zhi dao xian zai wo dou mei you ming bai
but i no caer an
[sgsm@localhost test2]$ 
```
## sed
用法：sed 选项 地址命令  文件
-n 不输出模式空间内容到屏幕,即不自动打印(默认会把源文件打印)
```bash
[sgsm@localhost test2]$ sed  2p test.txt 
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai
wei he   dang chu xuan ze li kai
but i no caer an
an an
san san
san san
xiao xiao
xiao xiao
[sgsm@localhost test2]$ sed   -n  2p test.txt 
wei he   dang chu xuan ze li kai
[sgsm@localhost test2]$ 
```
-e 多点编辑(同grep -e 用法一样)
```bash
[sgsm@localhost test2]$ sed   -n  -e  2p -e 4p test.txt   
wei he   dang chu xuan ze li kai
an an
[sgsm@localhost test2]$
```
-f 文件 ：从指定文件中读取编辑脚本
-r：支持使用扩展正则表达式
-i：原文编辑(会写入文件中)
###  Script：地址命令
地址定界：不指定地址,全文进行处理

          单地址       
```bash
#  #：指定的行
[sgsm@localhost test2]$ sed   -n 2p  test.txt                 
wei he   dang chu xuan ze li kai
[sgsm@localhost test2]$
```
            
```bash
# /pattern/：被此处模式所能够匹配到的行
[sgsm@localhost test2]$ sed   -n /san/p test.txt 
san san
san san
[sgsm@localhost test2]$ 
```
         地址范围
```bash
# #,#  从#--#行(可选范围)
[sgsm@localhost test2]$ sed   -n 2,4p test.txt   
wei he   dang chu xuan ze li kai
but i no caer an
an an
[sgsm@localhost test2]$ 
```
```bash
#  #,+# 从#到+#行
[sgsm@localhost test2]$ sed   -n 2,+6p test.txt   
wei he   dang chu xuan ze li kai
but i no caer an
an an
san san
san san
xiao xiao
xiao xiao
[sgsm@localhost test2]$ 
```
```bash
# /pat1/,/pat2/ (从第一个到第二个关键字)
[sgsm@localhost test2]$ sed   -n /dang/,/xiao/p test.txt                
wei he   dang chu xuan ze li kai
but i no caer an
an an
san san
san san
xiao xiao
[sgsm@localhost test2]$ 
```
```bash
#   #,/pat1/ (从#行到第一个关键字)
[sgsm@localhost test2]$ sed   -n 5,/xiao/p test.txt       
san san
san san
xiao xiao
[sgsm@localhost test2]$ 
```
         步进
```bash
# 1~2 奇数行（1,3,5,7）
[sgsm@localhost test2]$ cat -n  test.txt |sed   -n '1~2'p 
     1  zhi dao xian zai wo dou mei you ming bai
     3  but i no caer an
     5  san san
     7  xiao xiao
[sgsm@localhost test2]$ 
```
```bash
# 2~2 偶数行（2,4,6,8）
[sgsm@localhost test2]$ cat -n  test.txt |sed   -n '2~2'p  
     2  wei he   dang chu xuan ze li kai
     4  an an
     6  san san
     8  xiao xiao
[sgsm@localhost test2]$ 
```
### 编辑命令
```bash
# d：删除模式空间匹配到的行
[sgsm@localhost test2]$ cat  test.txt 
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai
but i no caer an
an an
san san
san san
xiao xiao
xiao xiao
[sgsm@localhost test2]$ sed    4,6d test.txt      # 删除4-6行
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai
but i no caer an
xiao xiao
xiao xiao
[sgsm@localhost test2]$ 
```
```bash
# P：显示模式空间中的内容
[sgsm@localhost test2]$ sed  -n  4,6p test.txt   # 打印4-6-  如果不加-n 会打印所有内容   4-6行打印两次
an an
san san
san san
[sgsm@localhost test2]$
```
```bash
# a[\]：在指定行后面追加文本支持使用\n实现多行追加
[sgsm@localhost test2]$ sed    1a\test test.txt     # 在第一行后增加内容
zhi dao xian zai wo dou mei you ming bai
test
wei he   dang chu xuan ze li kai
but i no caer an
an an
san san
san san
xiao xiao
xiao xiao
[sgsm@localhost test2]$ 
[sgsm@localhost test2]$ sed    /xiao/a\test test.txt      # 在匹配行后增加的内容
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai
but i no caer an
an an
san san
san san
xiao xiao
test
xiao xiao
test
[sgsm@localhost test2]$
```
```bash
# i[\]：在行前面插入文本
[sgsm@localhost test2]$ sed    /xiao/i\test test.txt  
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai
but i no caer an
an an
san san
san san
test
xiao xiao
test
xiao xiao
[sgsm@localhost test2]$ 
```
```bash
# c[\]：替换行位单行或多行文本
[sgsm@localhost test2]$ sed    /xiao/c\test test.txt  
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai
but i no caer an
an an
san san
san san
test
test
[sgsm@localhost test2]$ 
```
```bash
w 文件名：保存模式匹配到的行至指定文件
r文件名 ：读取指定文件的文本至模式空间中匹配到的行后
=:为模式空间中的行打印行号
！：模式空间中匹配到行去反处理
```
### 查找替换
      S///:查找替换,支持使用其它分隔符,s@@@,s###
```bash
g ：行内全局替换
[sgsm@localhost test2]$ sed  s/san/six/   test.txt   # 只替换第一个匹配到的关键字
zhi dao xian zai wo dou mei you ming bai
wei he   dang chu xuan ze li kai
but i no caer an
an an
six san
six san
xiao xiao
xiao xiao
[sgsm@localhost test2]$ 
[sgsm@localhost test2]$ sed  s/san/six/g   test.txt   # 全局替换
zhi dao xian zai wo dou mei you ming bai  
wei he   dang chu xuan ze li kai
but i no caer an
an an
six six
six six
xiao xiao
xiao xiao
[sgsm@localhost test2]$ 
```
```bash
P：显示替换成功的行
W文件名：将替换成功的行保存至文件中
```
## awk
awk是一种处理文本的编程语言工具，报告生成器，格式化文本输出  
基本用法： 
```bash
awk [options]  ’program’ var=value  file
命令  选项          程序    变量      文件
```
选项：
```bash 
       -F 指名输入时用到的字符分隔符(不指定-F 按空格分隔)
       -v  var=value：自定义变量
```
awk工作原理
第一步：执行BEGIN{action;处理内容}语句块中的语句（可省）  
第二步：从文件或标准输入（stdin）读取一行，然后执行pattern{action;处理内容}语句块，它逐行扫描文件，从第一行到最后一行重复这个过程，直到文件全部被读取完毕  
第三步：当读至输入流末尾时，执行END{action;处理内容}语句块  
BEGIN语句块在awk开始从输入流中读取之前被执行，这是一个可选的语句块，比如变量初始化、打印输出表格的表头等语句通常写在BEGIN语句块中  
END语句块在awk从输入流中读取完所有的行之后即可被执行，比如打印所有行的分析结果这类信息汇总都是在END语句块中完成，它也是一个可选语句块  
pattern语句块中的通用命令是最重要的部分，也是可选的，如果没有提供pattern语句块，则默认执行{print}，即打印每一个读取到的行，awk读取的每一行都会执行该语句块  
print格式：print item1，item2...（item条目）  

要点：  
1. 逗号分隔符  
2. 输出的各item可以是字符串，也可以是数值；当前记录的字段、变量或awk的表达式  
3. 如省略item，相当于print $0  
实例
无论输入什么都打印hello,awk
```bash
[sgsm@centos ~]$   awk '{print  "hello,awk"}' 
nishi
hello,awk
ok
hello,awk
^C
[sgsm@centos ~]$ 
```
指定分隔符打印所有
```bash
[sgsm@centos ~]$ awk -F: '{print}' /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
```
指定分隔符打印指定列
```bash
[sgsm@centos ~]$ awk -F: '{print $1}'  /etc/passwd
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
uucp
operator
games
gopher
ftp
```
指定分隔符打印第一列和第七列以table键分开
```bash
[sgsm@centos ~]$ awk -F: '{print $1 " \t " $7}'  /etc/passwd 
root     /bin/bash
bin      /sbin/nologin
daemon   /sbin/nologin
adm      /sbin/nologin
lp       /sbin/nologin
sync     /bin/sync
shutdown         /sbin/shutdown
```
变量：内置和自定义变量
FS：输入字符分隔符,默认为空白字符
```bash
[sgsm@centos ~]$ awk -v  FS=':' '{print $1,$3,$7}' /etc/passwd   
root 0 /bin/bash
bin 1 /sbin/nologin
daemon 2 /sbin/nologin
adm 3 /sbin/nologin
lp 4 /sbin/nologin
sync 5 /bin/sync
shutdown 6 /sbin/shutdown

[sgsm@centos ~]$ awk -F: '{print $1,$3,$7}' /etc/passwd          
root 0 /bin/bash
bin 1 /sbin/nologin
daemon 2 /sbin/nologin
adm 3 /sbin/nologin
lp 4 /sbin/nologin
sync 5 /bin/sync
shutdown 6 /sbin/shutdown
```
OFS:输出字符分隔符,默认为空白字符
```bash
[sgsm@centos ~]$ awk -v FS=':' -v OFS='--' '{print$1,$3,$7}' /etc/passwd
root--0--/bin/bash
bin--1--/sbin/nologin
daemon--2--/sbin/nologin
adm--3--/sbin/nologin
lp--4--/sbin/nologin
sync--5--/bin/sync
shutdown--6--/sbin/shutdown
```
NF:字段数量
```bash
# 打印列数
[sgsm@centos ~]$ awk -F: '{print NF}' /etc/passwd
7
7
7
7
7
7
# 打印最后一列内容
[sgsm@centos ~]$ awk -F: '{print $NF}' /etc/passwd
/bin/bash
/sbin/nologin
/sbin/nologin
/sbin/nologin
/sbin/nologin
/bin/sync
/sbin/shutdown
# 打印倒数第二列内容
[sgsm@centos ~]$ awk -F: '{print $(NF-1)}' /etc/passwd  
/root
/bin
/sbin
/var/adm
/var/spool/lpd
/sbin
/sbin
/sbin
```
NR：行号
```bash
# 打印第二行
[sgsm@centos ~]$ awk  'NR==2{print }' /etc/passwd    
bin:x:1:1:bin:/bin:/sbin/nologin
```
FILENAME：当前文件名  
```bash
[root@docker ~]# awk '{print FILENAME}' /etc/fstab  
/etc/fstab
/etc/fstab
/etc/fstab
...
```
ARGC：命令行参数的个数  
```bash
[root@docker ~]# awk '{print ARGC}' /etc/fstab /etc/inittab  
3
3
3
...
[root@docker ~]# awk 'BEGIN{print ARGC}' /etc/fstab /etc/inittab  
3
```
ARGV：数组，保存的是命令行所给定的各参数（0为第一个参数，1为第二个参数，以此类推）  
```bash
[root@docker ~]# awk 'BEGIN{print ARGV[0]}' /etc/fstab /etc/inittab  
awk
[root@docker ~]# awk 'BEGIN{print ARGV[1]}' /etc/fstab /etc/inittab  
/etc/fstab
```

## 自定义变量  
1. `-v var=value`变量名区分大小写  
2. 在program中直接定义  
   
示例：  
```bash
[root@docker ~]# awk -v test='hello gawk' '{print tast}' /etc/fstab  

...
[root@docker ~]# awk -v test='hello gawk' 'BEGIN{print test}'  
hello gawk
[root@docker ~]# awk 'BEGIN{test="hello gawk";print test}'  
hello gawk
```

# awk格式化  

`printf`命令  
格式输出：`printf "FORMAT",item1,item2…`  
必须指定FORMAT（格式）  
不会自动换行，需要显示给出换行控制符 `\n`  
FORMAT中需要分别为后面每个item指定格式符  
格式符：与item一一对应  
`%d`,`%i`：显示十进制整数  
`%s`：显示字符串  
`%%`：显示%自身  

修饰符  
`#`：打印#个字符宽度（可以为小数）  
`-`：左对齐%-15s（默认不带 - 右对齐）  
`+`：显示数值的正负符号%+d  
```bash
[root@docker ~]# awk -F: '{printf "%s",$1}' /etc/passwd  
rootbindaemonadmlpsyncshutdownhaltmailoperatorgamesftpnobodydbussystemd-coredumpsystemd-resolvetsspolkitdlibstoragemgmtcockpit-wscockpit-wsinstancesssdsshdchronyrngdunboundnginx[root@docker ~]#   
[root@docker ~]# awk -F: '{printf "USERNAME:%s\n",$1}' /etc/passwd  
USERNAME:root
USERNAME:bin
USERNAME:daemon
...
[root@docker ~]# awk -F: '{printf "USERNAME:%s UID:%d\n",$1,$3}' /etc/passwd  
USERNAME:root UID:0
USERNAME:bin UID:1
USERNAME:daemon UID:2
...
[root@docker ~]# awk -F: '{printf "USERNAME:%15s UID:%d\n",$1,$3}' /etc/passwd  
USERNAME:           root UID:0
USERNAME:            bin UID:1
USERNAME:         daemon UID:2
...
[root@docker ~]# awk -F: '{printf "USERNAME:%-15s UID:%d\n",$1,$3}' /etc/passwd  
USERNAME:root            UID:0
USERNAME:bin             UID:1
USERNAME:daemon          UID:2
...
```
  
# awk操作符  
## 算数操作符：  
`x+y`,`x-y`,`x*y`,`x/y`,`x^y`,`x%y`（x%y，即x÷y取余数）  
```bash
[root@docker ~]# awk 'BEGIN{print 1+2}'
3
[root@docker ~]# awk 'BEGIN{print 1-2}'
-1
[root@docker ~]# awk 'BEGIN{print 1*2}'
2
[root@docker ~]# awk 'BEGIN{print 1/2}'
0.5
[root@docker ~]# awk 'BEGIN{print 1^2}'
1
[root@docker ~]# awk 'BEGIN{print 1%2}'
1
[root@docker ~]# awk 'BEGIN{print 3%2}'
1
[root@docker ~]# awk 'BEGIN{print 3%1}'
0
[root@docker ~]# awk 'BEGIN{print 5%3}'
2
```
`-x`：转换为负数（-用双引号引起来，可以在0前面加上-号）  
```bash
[root@docker ~]# awk -F: '{print -$3}' /etc/passwd
0
-1
-2
...
```

`+x`：转换为数值（同-，可以用双引号引起来）  
```bash
[root@docker ~]# awk -F: '{print +$3}' /etc/passwd
0
1
2
...
```
## 字符串操作符：没有符号的操作符，字符串连接  

## 赋值操作符：  
`=`，`+=`，`-=`，`*=`，`/=`，`%=`，`^=`，`++`，`--`（++每次加1，--每次减1）  
```bash
[root@docker ~]# awk 'BEGIN{a=1;print a++,a}'
1 2
[root@docker ~]# awk 'BEGIN{a=1;print a--,a}'
1 0
[root@docker ~]# awk 'BEGIN{a=1;print --a,a}'
0 0
[root@docker ~]# awk 'BEGIN{a=1;print ++a,a}'
2 2
```

## 比较操作符：  
`>`，`>=`，`<`，`<=`，`!=`，`==`  
```bash
[root@docker ~]# awk -F: '$3 > 999{print $3}' /etc/passwd
65534
[root@docker ~]# awk -F: '$3 >= 999{print $3}' /etc/passwd
65534
999
[root@docker ~]# awk -F: '$3 < 3{print $3}' /etc/passwd
0
1
2
[root@docker ~]# awk -F: '$3 <= 3{print $3}' /etc/passwd
0
1
2
3
[root@docker ~]# awk -F: '$3 != 2{print $3}' /etc/passwd
0
1
3
...
[root@docker ~]# awk -F: '$3 == 2{print $3}' /etc/passwd
2
```

## 模式匹配符：  
`~`：左边包含右边匹配到的内容  
`!~`：左边不包含右边匹配到的内容  
```bash
[root@docker ~]# awk '$0 ~ /root/{print}' /etc/passwd   # 支持正则表达式
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
[root@docker ~]# cat /etc/passwd | awk '$0 !~ /^root/'
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
...
```

## 逻辑操作符  
或 `||`，与 `&&`，非`!`  
```bash
[root@docker ~]# awk -F: '$3>=0 && $3<=3{print $1,$3}' /etc/passwd
root 0
bin 1
daemon 2
adm 3
[root@docker ~]# awk -F: '$3==0 || $3<=3{print $1,$3}' /etc/passwd
root 0
bin 1
daemon 2
adm 3
[root@docker ~]# awk -F: '!($3>=3){print $1,$3}' /etc/passwd
root 0
bin 1
daemon 2
```
  
`PATTERN`：根据pattern条件，过滤匹配的行，再做处理  
如果未指定：空模式，匹配每一行  
`/正则表达式/`：仅处理能够被模式匹配到的行，需要用 `/ /` 括起来  
```bash
[root@docker ~]# awk '/^UUID/{print $1}' /etc/fstab
UUID=8b1f186e-ffcb-4b8e-a9e2-caba58a3a850
[root@docker ~]# awk '!/^UUID/{print $1}' /etc/fstab

...
/dev/mapper/cl-root
/dev/mapper/cl-swap
```
  
关系表达式：结果有"真"有"假"；结果为"真"才会被处理；  
真：结果为非0值，非空字符串  
假：结果为空字符串  
示例：  
```bash
[root@docker ~]# awk -F: '$3>1000{print $1,$3}' /etc/passwd
nobody 65534
[root@docker ~]# awk -F: '$NF~/bash$/{print $1,$NF}' /etc/passwd
root /bin/bash
```
  
`line ranges`：行范围  
`startline,endline`：`/pat1/,/pat2/`不支持直接给出数字格式  
```bash
[root@docker ~]# awk -F: '/^root/,/^lp/{print $1}' /etc/passwd
root
bin
daemon
adm
lp
[root@docker ~]# awk -F: '(NR>=2&&NR<=5){print $1}' /etc/passwd
bin
daemon
adm
lp
```
  
`BEGIN/END`模式  
`BEGIN{}`：仅在开始处理文件中的文本之前执行一次  
`END{}`：仅在文本处理完成之后执行一次  
```bash
[root@docker ~]# awk -F: 'BEGIN{print "USER UID"}(NR>=2&&NR<=4){print $1":"$3}END{print "end file"}' /etc/passwd
USER UID
bin:1
daemon:2
adm:3
end file
[root@docker ~]# cat /etc/passwd | head -3 | awk -F: '{print "USER UID";print $1":"$3}END{print "end file"}'
USER UID
root:0
USER UID
bin:1
USER UID
daemon:2
end file
[root@docker ~]# awk -F: 'BEGIN{print "USER UID \n ---------"}{print $1,$3}' /etc/passwd
USER UID 
 ---------
root 0
bin 1
...
[root@docker ~]# awk -F: 'BEGIN{print "USER UID \n ---------"}{print $1,$3}'END'{print "========="}' /etc/passwd
USER UID 
 ---------
root 0
...
nginx 990
=========
```























# find