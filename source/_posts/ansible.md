---
title: 自动化运维工具——ansible
tags:
  - ansible
  - linux
categories: 运维
date: 2020-07-03 09:52:26
---
## ansible简介
ansible是最常用的自动化工具,基于python开发,集合了众多运维工具(puppet,chef,func,fabric)的优点,实现了批量系统配置、批量程序部署、批量运行命令等功能
<br/>ansible是基于paramiko开发的,并且基于模块化工作,本身没有批量部署的能力,真正具有部署的ansible所运行的模块,ansible只是提供一种框架,ansible不需要在远程主机上安装client/agents,因为他们是基于ssh来和远程主机通讯<br/>

## ansible特点
```bash
1、部署简单,只需要在主控制端部署ansible环境,被控制端无需任何操作
2、默认使用SSH协议对设备进行管理
3、有大量常规运维操作模块,可实现日常绝大部分操作
4、配置简单、功能强大、扩展性强
5、支持API及自定义模块,可通过python扩展
6、通过playbooks来定制强大的配置、状态管理
7、轻量级、无需在客户端安装agent,更新时,只需在操作机上进行一次更新即可
8、提供一个功能强大、操作性强的web管理界面和REST API接口--aws平台
```
## ansible架构图
![](../1.png)
上图我们看到主要的模块如下：
```bash
Ansible: Ansible核心程序
HostInventory：记录由Ansible管理的主机信息,包括端口、密码、ip等
Playbooks："剧本"YAML格式文件,多个任务定义在一个文件中,定义主机需要调用那些模块来完成的功能
CoreModules：核心模块,主要操作是通过调用核心模块来管理任务
CustomModules：自定义模块,完成核心模块无法完成的功能,支持多种语言
ConnectionPlugins：连接插件,ansible和host通信使用
```
## ansible任务执行模式
ansible系统由控制主机对被管理节点的操作方式可分为两类,即adhoc和playbook：
```bash
ad-hoc模式(点对点模式)
    使用单个模块,支持批量执行单条命令,ad-hoc命令是一种可以快速输入的命令,而且不需要保存起来的命令,相当于bash中的一句话shell
playbook模式(剧本模式)
    是ansible主要管理方式,也是ansible功能强大的关键所在,playbook通过多个task集合完成一类功能,如web服务的安装部署、数据库服务器的批量备份等,可以简单的把playbook理解为通过组合多条ad-hoc操作的配置文件
```
## ansible执行流程
简单的理解说就是ansible在运行时,首先读取ansible.cfg中的配置,根据规则获取Inventory中的管理主机列表,并行的在这些主机中执行配置的任务,最后等待执行返回的结果
## ansible命令执行过程
```bash
1、加载自己的配置文件,默认/etc/ansible/ansible.cfg
2、查找对应的主机配置文件,找到要执行的主机或组
3、加载自己对应的模块,如command
4、通过ansible将模块或命令生成对应的临时py文件(python脚本),并将该文件传输至远程服务器
5、对应执行用户家目录的.ansible/tmp/xxx/xxx.py文件
6、给文件 +x 执行权限
7、执行并返回结果
8、删除临时py文件,sleep 0 退出
```
## ansible安装
yum 安装是我们很熟悉的安装方式了。我们需要先安装一个epel-release包，然后再安装我们的 ansible 即可
```bash
yum install epel-release -y
yum install ansible –y
```
## ansible 程序结构
```bash
安装目录如下(yum安装)：
　　配置文件目录：/etc/ansible/
　　执行文件目录：/usr/bin/
　　Lib库依赖目录：/usr/lib/pythonX.X/site-packages/ansible/
　　Help文档目录：/usr/share/doc/ansible-X.X.X/
　　Man文档目录：/usr/share/man/man1/
```
## ansible配置文件
ansible的配置文件为 /etc/ansible/ansibel.cfg
```bash
默认配置
这里的配置项有很多，这里主要介绍一些常用的
[defaults]
#inventory      = /etc/ansible/hosts                        #被控端的主机列表文件
#library        = /usr/share/my_modules/                    #库文件存放目录
#remote_tmp     = ~/.ansible/tmp                            #临时文件远程主机存放目录
#local_tmp      = ~/.ansible/tmp                            #临时文件本地存放目录
#forks          = 5                                         #默认开启的并发数
#poll_interval  = 15                                        #默认轮询时间间隔(单位秒)
#sudo_user      = root                                      #默认sudo用户
#ask_sudo_pass = True                                       #是否需要sudo密码
#ask_pass      = True                                       #是否需要密码
#transport      = smart                                     #传输方式
#remote_port    = 22                                        #默认远程主机的端口号
建议开启修改以下两个配置参数(取消掉注释即可)
#host_key_checking = False                                  #检查对应服务器的host_key
#log_path=/var/log/ansible.log                              #开启ansible日志
```
## ansible-doc 命令
ansible-doc 命令常用于获取模块信息及其使用帮助，一般用法如下：
```bash
ansible-doc  -l  # 获取全部模块的信息
ansible-doc  -s  MOD_NAME  # 获取指定模块的使用帮助
```
## ansible 命令详解
命令的具体格式如下：
```bash
ansible <host-pattern> [-f forks] [-m module_name] [-a args]
```
常用选项
```bash
# 常用
-a MODULE_ARGS　　：模块的参数，如果执行默认COMMAND的模块，即是命令参数，如： “date”，“pwd”等等
-m MODULE_NAME ：指定模块  例如  ansible -m  shell    默认使用 command 模块
-i INVENTORY ：指定主机清单的路径，默认为/etc/ansible/hosts
-u REMOTE_USER ：远程用户，默认为 root 用户
-C ：模拟运行环境并进行预运行，可以进行查错测试
-S ：用 su 命令
-R SU_USER ：指定 su 的用户，默认为 root 用户
-U SUDO_USER ：指定 sudo 到哪个用户，默认为 root 用户
-T TIMEOUT ：指定 ssh 默认超时时间，默认为10s，也可在配置文件中修改
-u REMOTE_USER ：远程用户，默认为 root 用户
-v ：查看详细信息，同时支持-vvv，-vvvv可查看更详细信息
# 了解
-k，--ask-pass ：ask for SSH password。登录密码，提示输入SSH密码而不是假设基于密钥的验证
-K，--ask-sudo-pass ：ask for sudo password。提示密码使用sudo，sudo表示提权操作
-B SECONDS ：后台运行超时时间
-c CONNECTION ：连接类型使用
-f FORKS ：并行任务数，默认为5
-o ：压缩输出，尝试将所有结果在一行输出，一般针对收集工具使用
```
## ansible 配置公私钥
ansible 是基于 ssh 协议实现的，所以其配置公私钥的方式与 ssh 协议的方式相同，具体操作步骤如下：
```bash
# 生成私钥
ssh-keygen 
# 向主机分发私钥
ssh-copy-id root@192.168.37.122
ssh-copy-id root@192.168.37.133
```
(非交互式批量建立免密登录)[]

# ansible常用模块
主机连通性测试  -m  ping 
```bash
[sgsm@ecs-d37b ~]$ ansible -i hosts/hosts  game   -m  ping   
game-5 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
# -i 指定主机清单
# game   主机清单中的分组
```
## command模块
```bash
[sgsm@ecs-d37b ~]$ ansible -i hosts/hosts  game   -m  command  -a "df -h"               
game-5 | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        40G   27G   11G  72% /
devtmpfs        3.9G     0  3.9G   0% /dev
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           3.9G   49M  3.8G   2% /run
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
tmpfs           783M     0  783M   0% /run/user/1000
```
该模块常用命令
```bash
chdir　　　　　　 # 在执行命令之前，先切换到该目录
executable # 切换shell来执行命令，需要使用命令的绝对路径
free_form 　 # 要执行的Linux指令，一般使用Ansible的-a参数代替。
creates 　# 一个文件名，当这个文件存在，则该命令不执行,可以
用来做判断
removes # 一个文件名，这个文件不存在，则该命令不执行
# 注意：该命令不支持 | 管道命令
```

chdir : 先切换到/data/ 目录，再执行“ls -l”命令
```bash
[sgsm@ecs-d37b ~]$ ansible -i hosts/hosts  game   -m  command  -a "chdir=/data/  ls -l"

game-5 | CHANGED | rc=0 >>
total 32
drwxr-xr-x 6 sgsm sgsm 4096 Oct  2  2019 backups
drwxr-xr-x 3 sgsm sgsm 4096 Sep  3  2019 copy
drwxr-xr-x 3 sgsm sgsm 4096 Sep  3  2019 install
drwxr-xr-x 4 root root 4096 Sep  3  2019 lib
drwxr-xr-x 4 root root 4096 Sep  3  2019 log
drwxr-xr-x 2 sgsm sgsm 4096 Sep  3  2019 package
drwxr-xr-x 3 sgsm sgsm 4096 Jul  7 04:30 script
drwxr-xr-x 5 sgsm sgsm 4096 Sep  3  2019 sgsm
```
creates : 如果/data/script/findDatas2.js存在，则不执行“ls -l”命令
```bash
[sgsm@ecs-d37b ~]$ ansible -i hosts/hosts  game   -m  command  -a "creates=/data/script/findDatas2.js  ls -1"

game-5 | SUCCESS | rc=0 >>
skipped, since /data/script/findDatas2.js exists
```
removes ： 如果/data/script/findDatas2.js存在，则执行“cat /data/script/test.sh”命令
```bash
[sgsm@ecs-d37b ~]$ ansible -i hosts/hosts  game   -m  command  -a "removes=/data/script/findDatas2.js  cat /data/script/findDatas2.js"

game-5 | CHANGED | rc=0 >>
#!/bin/bash
echo "test"
```
## shell 模块
shell模块可以在远程主机上调用shell解释器运行命令，支持shell的各种功能，例如管道等。
```bash
[sgsm@ecs-d37b ~]$ ansible -i  hosts/hosts   game  -m shell -a  "ps -aux  |grep mongo"

game-5 | CHANGED | rc=0 >>
root      1834  0.3 12.4 1512160 995052 ?      Sl   Jun08 136:05 mongod -f /etc/mongod.conf
sgsm     24901  0.0  0.0 113176  1212 pts/0    S+   11:05   0:00 /bin/sh -c ps -aux  |grep mongo
sgsm     24903  0.0  0.0 112712   960 pts/0    S+   11:05   0:00 grep mongo
```
只要是我们的shell命令，都可以通过这个模块在远程主机上运行
## copy模块
这个模块用于将文件复制到远程主机,同时支持给定内容生成文件和修改权限等,其相关选项如下：
```bash
src　　　　           #被复制到远程主机的本地文件。可以是绝对路径，也可以是相对路径。如果路径是一个目录，则会递归复制，用法类似于"rsync"
content　　　         #用于替换"src"，可以直接指定文件的值
dest　　　　          #必选项，将源文件复制到的远程主机的绝对路径
backup　　　          #当文件内容发生改变后，在覆盖之前把源文件备份，备份文件包含时间信息
directory_mode　　　　#递归设定目录的权限，默认为系统默认权限
force　　　　         #当目标主机包含该文件，但内容不同时，设为"yes"，表示强制覆盖；设为"no"，表示目标主机的目标位置不存在该文件才复制。默认为"yes"
others　　　　        #所有的 file 模块中的选项可以在这里使用
owner                #置文件/目录的所属用户，将被馈送到chown
```
```bash
                                                                # 文件路径                     远程主机文件路径               用户        是否备份
[sgsm@ecs-d37b ~]$ ansible -i ~/hosts/hosts game -m copy -a "src=/data/script/findDatas2.js dest=/data/script/findDatas2.js owner=sgsm backup=yes"

game-5 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "checksum": "c1a80b1a9a0261aaf02bc44f37f2d332a671b613", 
    "dest": "/data/script/findDatas2.js", 
    "gid": 100, 
    "group": "users", 
    "mode": "0644", 
    "owner": "sgsm", 
    "path": "/data/script/findDatas2.js", 
    "size": 1009, 
    "state": "file", 
    "uid": 1000
}
```
给定内容生成文件，并制定权限
```bash
                                                                # 设置内容            目标必须是个文件名      权限
[sgsm@ecs-d37b ~]$ ansible -i ~/hosts/hosts game -m copy -a "content='I am keer\n' dest=/data/script/name mode=666"
game-5 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "checksum": "0421570938940ea784f9d8598dab87f07685b968", 
    "dest": "/data/script/name", 
    "gid": 100, 
    "group": "users", 
    "md5sum": "497fa8386590a5fc89090725b07f175c", 
    "mode": "0666", 
    "owner": "sgsm", 
    "size": 10, 
    "src": "/home/sgsm/.ansible/tmp/ansible-tmp-1594091785.39-273500225924687/source", 
    "state": "file", 
    "uid": 1000
}                           
# 验证
[sgsm@ecs-d37b ~]$ ansible -i  hosts/hosts  game -m  shell -a "cat  /data/script/name  ; ls -l  /data/script/name"
game-5 | CHANGED | rc=0 >>
I am keer
-rw-rw-rw- 1 sgsm users 10 Jul  7 11:16 /data/script/name
```
## file 模块
该模块主要用于设置文件的属性,比如创建文件、创建链接文件、删除文件等,下面是一些常见的命令：
```bash
orce        #需要在两种情况下强制创建软链接，一种是源文件不存在，但之后会建立的情况下；另一种是目标软链接已存在，需要先取消之前的软链，然后创建新的软链，有两个选项：yes|no
group       #定义文件/目录的属组。后面可以加上mode：定义文件/目录的权限
owner       #定义文件/目录的属主。后面必须跟上path：定义文件/目录的路径
recurse     #递归设置文件的属性，只对目录有效，后面跟上src：被链接的源文件路径，只应用于state=link的情况
dest        #被链接到的路径，只应用于state=link的情况

state       #状态，有以下选项：

    directory：如果目录不存在，就创建目录
    file：即使文件不存在，也不会被创建
    link：创建软链接
    hard：创建硬链接
    touch：如果文件不存在，则会创建一个新的文件，如果文件或目录已存在，则更新其最后修改时间
    absent：删除目录、文件或者取消链接文件
```
创建目录
```bash
[sgsm@ecs-d37b ~]$ ansible -i  hosts/hosts  game  -m  file  -a  "path=/home/sgsm/test state=directory"
game-5 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "gid": 100, 
    "group": "users", 
    "mode": "0755", 
    "owner": "sgsm", 
    "path": "/home/sgsm/test", 
    "size": 4096, 
    "state": "directory", 
    "uid": 1000
}
# 验证
[sgsm@ecs-d37b ~]$ ansible -i  hosts/hosts  game -m  shell -a "ls -l  /home/sgsm"
game-5 | CHANGED | rc=0 >>
total 19604
drwxr-xr-x 2 sgsm users     4096 Jul  7 11:41 test
-rw-r--r-- 1 sgsm users     5926 Aug 19  2019 twodata-huawei.sh
```
创建链接文件
```bash
[sgsm@ecs-d37b ~]$ ansible -i  hosts/hosts  game  -m  file  -a  "path=/home/sgsm/qq2 src=/home/sgsm/qq state=link"              
game-5 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "dest": "/home/sgsm/qq2", 
    "gid": 100, 
    "group": "users", 
    "mode": "0777", 
    "owner": "sgsm", 
    "size": 13, 
    "src": "/home/sgsm/qq", 
    "state": "link", 
    "uid": 1000
}
# 验证
[sgsm@ecs-d37b ~]$ ansible -i  hosts/hosts  game -m  shell -a "ls -l  /home/sgsm"                                 
game-5 | CHANGED | rc=0 >>
total 59604
-rw-r--r-- 1 sgsm users       20 May 19 14:24 qq
lrwxrwxrwx 1 sgsm users       13 Jul  7 11:46 qq2 -> /home/sgsm/qq
drwxr-xr-x 2 sgsm users     4096 Jul  7 11:41 test
-rw-r--r-- 1 sgsm users     5926 Aug 19  2019 twodata-huawei.sh
```
删除文件
```bash
[sgsm@ecs-d37b ~]$ ansible -i  hosts/hosts  game  -m  file  -a  "path=/home/sgsm/qq2  state=absent"               
game-5 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "path": "/home/sgsm/qq2", 
    "state": "absent"
}
# 验证
[sgsm@ecs-d37b ~]$ ansible -i  hosts/hosts  game -m  shell -a "ls -l  /home/sgsm"        
game-5 | CHANGED | rc=0 >>
total 59604
-rw-r--r-- 1 sgsm users       20 May 19 14:24 qq
drwxr-xr-x 2 sgsm users     4096 Jul  7 11:41 test
-rw-r--r-- 1 sgsm users     5926 Aug 19  2019 twodata-huawei.sh
```
## fetch 模块
该模块用于从远程某主机获取（复制）文件到本地,有两个选项：
```bash
dest：用来存放文件的目录
src：在远程拉取的文件，并且必须是一个file，不能是目录
```
