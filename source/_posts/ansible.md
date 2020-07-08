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
## command 模块
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
## copy 模块
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
拉取文件
```bash
                                                                    # 远程文件的路径                      本地路径
[sgsm@ecs-d37b ~]$ ansible -i ~/hosts/hosts  game    -m  fetch  -a "src=/data/script/autoStart5.sh   dest=/home/sgsm"     

game-5 | CHANGED => {
    "changed": true, 
    "checksum": "408270467e7ce32d5e9dec89c727848590805b3b", 
    "dest": "/home/sgsm/game-5/data/script/autoStart5.sh", 
    "md5sum": "2b8f241ecf586915f3ec39c0032841a5", 
    "remote_checksum": "408270467e7ce32d5e9dec89c727848590805b3b", 
    "remote_md5sum": null
}
[sgsm@ecs-d37b ~]$ ll  /home/sgsm
total 40
drwxr-xr-x 3 sgsm users 4096 Jul  7 18:44 game-5      # 拉取后会在本地路径生成一个以远程服务器分组命名的目录

[sgsm@ecs-d37b ~]$ tree  /home/sgsm/game-5/      # 目录下是远程服务器文件的全路径--(目录自动层层创建)
/home/sgsm/game-5/
└── data
    └── script
        └── autoStart5.sh

2 directories, 1 file
[sgsm@ecs-d37b ~]$ 
```
## cron 模块
该模块适用于管理cron计划任务的,其使用的语法跟我们的crontab文件中的语法一致,同时,可以指定以下选项：
```bash
day=        #日应该运行的工作( 1-31, , /2, )
hour=       # 小时 ( 0-23, , /2, )
minute=     #分钟( 0-59, , /2, )
month=      # 月( 1-12, *, /2, )
weekday=    # 周 ( 0-6 for Sunday-Saturday,, )
job=        #指明运行的命令是什么
name=       #定时任务描述
reboot      # 任务在重启时运行，不建议使用，建议使用special_time
special_time #特殊的时间范围，参数：reboot（重启时），annually（每年），monthly（每月），weekly（每周），daily（每天），hourly（每小时）
state       #指定状态，present表示添加定时任务，也是默认设置，absent表示删除定时任务
user        # 以哪个用户的身份执行
```
添加计划任务
```bash
 #                                                                名称             每五分钟        命令
[sgsm@ecs-d37b ~]$ ansible -i hosts/hosts  game   -m cron -a 'name="View time" minute=*/5   job="/usr/bin/date"'                                                                       
game-5 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "envs": [], 
    "jobs": [
        "View time"
    ]
}
# 验证
[sgsm@ecs-d37b ~]$ ansible -i hosts/hosts   game   -m shell -a  "crontab  -l"
game-5 | CHANGED | rc=0 >>
#Ansible: remove 5 server gamelog
0 4 * * * /data/script/remove_5_game_log.sh > /dev/null
#Ansible: View time
*/5 * * * * /usr/bin/date 
```
删除计划任务
```bash
#                                                                   名称             每五分钟        命令          删除
[sgsm@ecs-d37b ~]$ ansible -i hosts/hosts  game   -m cron -a 'name="View time" minute=*/5   job="/usr/bin/date"  state=absent' 

game-5 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "envs": [], 
    "jobs": [
        "remove 5 server gamelog", 
    ]
}
# 验证
[sgsm@ecs-d37b ~]$ ansible -i hosts/hosts   game   -m shell -a  "crontab  -l"
game-5 | CHANGED | rc=0 >>
#Ansible: remove 5 server gamelog
0 4 * * * /data/script/remove_5_game_log.sh > /dev/null
```
## yum 模块
顾名思义,该模块主要用于软件的安装,其选项如下：
```bash
name=　　       # 所安装的包的名称
state=　　      # present--->安装， latest--->安装最新的, absent---> 卸载软件。
update_cache　　# 强制更新yum的缓存
conf_file　　   # 指定远程yum安装时所依赖的配置文件（安装本地已有的包）。
disable_pgp_check　　# 是否禁止GPG checking，只用于present or latest。
disablerepo　　 # 临时禁止使用yum库。 只用于安装或更新时。
enablerepo　　  # 临时使用的yum库。只用于安装或更新时。
```
安装lsof工具
```bash
[root@localhost ~]# ansible -i hosts/hosts   9999   -m yum  -a "name=lsof  state=present"

game-9999 | CHANGED => {
    "ansible_facts": {
        "pkg_mgr": "yum"
    }, 
    "changed": true, 
    "msg": "", 
    "rc": 0, 
    "results": [
        "Loaded plugins: fastestmirror, product-id, search-disabled-repos, subscription-\n              : manager\n\nThis system is not registered with an entitlement server. You can use subscription-manager to register.\n\nLoading mirror speeds from cached hostfile\n * base: mirrors.bfsu.edu.cn\n * epel: hkg.mirror.rackspace.com\n * extras: mirrors.bfsu.edu.cn\n * updates: mirrors.bfsu.edu.cn\nResolving Dependencies\n--> Running transaction check\n---> Package lsof.x86_64 0:4.87-6.el7 will be installed\n--> Finished Dependency Resolution\n\nDependencies Resolved\n\n================================================================================\n Package         Arch              Version                Repository       Size\n================================================================================\nInstalling:\n lsof            x86_64            4.87-6.el7             base            331 k\n\nTransaction Summary\n================================================================================\nInstall  1 Package\n\nTotal download size: 331 k\nInstalled size: 927 k\nDownloading packages:\nRunning transaction check\nRunning transaction test\nTransaction test succeeded\nRunning transaction\n  Installing : lsof-4.87-6.el7.x86_64                                       1/1 \n  Verifying  : lsof-4.87-6.el7.x86_64                                       1/1 \n\nInstalled:\n  lsof.x86_64 0:4.87-6.el7                                                      \n\nComplete!\n"
    ]
}
```
指定用户安装软件
```bash
[sgsm@ecs-d37b ~]$ ansible -i hosts/hosts  game   -u root  -m  yum  -a  "name=elinks state=present"
 [WARNING]: Found both group and host with same name: web

 [WARNING]: Found both group and host with same name: video

 [WARNING]: Found both group and host with same name: pay

 [WARNING]: Found both group and host with same name: pvp

game-5 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "changes": {
        "installed": [
            "elinks"
        ]
    }, 
    "msg": "", 
    "rc": 0, 
    "results": [
        "Loaded plugins: fastestmirror\nLoading mirror speeds from cached hostfile\n * base: mirror.bit.edu.cn\n * epel: mirrors.njupt.edu.cn\n * extras: mirror.bit.edu.cn\n * updates: mirror.bit.edu.cn\nResolving Dependencies\n--> Running transaction check\n---> Package elinks.x86_64 0:0.12-0.37.pre6.el7.0.1 will be installed\n--> Processing Dependency: libnss_compat_ossl.so.0()(64bit) for package: elinks-0.12-0.37.pre6.el7.0.1.x86_64\n--> Processing Dependency: libmozjs185.so.1.0()(64bit) for package: elinks-0.12-0.37.pre6.el7.0.1.x86_64\n--> Running transaction check\n---> Package js.x86_64 1:1.8.5-20.el7 will be installed\n---> Package nss_compat_ossl.x86_64 0:0.9.6-8.el7 will be installed\n--> Finished Dependency Resolution\n\nDependencies Resolved\n\n================================================================================\n Package              Arch        Version                       Repository\n                                                                           Size\n================================================================================\nInstalling:\n elinks               x86_64      0.12-0.37.pre6.el7.0.1        base      882 k\nInstalling for dependencies:\n js                   x86_64      1:1.8.5-20.el7                base      2.3 M\n nss_compat_ossl      x86_64      0.9.6-8.el7                   base       37 k\n\nTransaction Summary\n================================================================================\nInstall  1 Package (+2 Dependent packages)\n\nTotal download size: 3.2 M\nInstalled size: 9.6 M\nDownloading packages:\n--------------------------------------------------------------------------------\nTotal                                              3.3 MB/s | 3.2 MB  00:00     \nRunning transaction check\nRunning transaction test\nTransaction test succeeded\nRunning transaction\n  Installing : nss_compat_ossl-0.9.6-8.el7.x86_64                           1/3 \n  Installing : 1:js-1.8.5-20.el7.x86_64                                     2/3 \n  Installing : elinks-0.12-0.37.pre6.el7.0.1.x86_64                         3/3 \n  Verifying  : elinks-0.12-0.37.pre6.el7.0.1.x86_64                         1/3 \n  Verifying  : 1:js-1.8.5-20.el7.x86_64                                     2/3 \n  Verifying  : nss_compat_ossl-0.9.6-8.el7.x86_64                           3/3 \n\nInstalled:\n  elinks.x86_64 0:0.12-0.37.pre6.el7.0.1                                        \n\nDependency Installed:\n  js.x86_64 1:1.8.5-20.el7         nss_compat_ossl.x86_64 0:0.9.6-8.el7        \n\nComplete!\n"
    ]
}
```
## service 模块
该模块用于服务程序的管理,其主要选项如下：
```bash
arguments       #命令行提供额外的参数
enabled         #设置开机启动。
name=           #服务名称
runlevel        #开机启动的级别，一般不用指定。
sleep           #在重启服务的过程中，是否等待。如在服务关闭以后等待2秒再启动。(定义在剧本中。)
state           #有四种状态，分别为：started--->启动服务， stopped--->停止服务， restarted--->重启服务， reloaded--->重载配置
```
开启服务
```bash
[root@localhost ~]# ansible -i hosts/hosts   9999   -m service  -a "name=httpd state=started "
game-9999 | SUCCESS => {
    "changed": false, 
    "name": "httpd", 
    "state": "started", 
    "status": {
        ....
    }
}
# 验证
[root@localhost server]# lsof -i:80
COMMAND   PID   USER   FD   TYPE   DEVICE SIZE/OFF NODE NAME
httpd   12297   root    4u  IPv6 65851262      0t0  TCP *:http (LISTEN)
httpd   12315 apache    4u  IPv6 65851262      0t0  TCP *:http (LISTEN)
httpd   12316 apache    4u  IPv6 65851262      0t0  TCP *:http (LISTEN)
httpd   12317 apache    4u  IPv6 65851262      0t0  TCP *:http (LISTEN)
httpd   12318 apache    4u  IPv6 65851262      0t0  TCP *:http (LISTEN)
httpd   12319 apache    4u  IPv6 65851262      0t0  TCP *:http (LISTEN)
```
关闭服务
```bash
[root@localhost ~]# ansible -i hosts/hosts   9999   -m service  -a "name=httpd state=stopped "
game-9999 | CHANGED => {
    "changed": true, 
    "name": "httpd", 
    "state": "stopped", 
    "status": {
        ...
    }
}
# 验证
[root@localhost server]# lsof -i:80
[root@localhost server]# 
```
## user 模块
该模块主要是用来管理用户账号,其主要选项如下：
```bash
comment         # 用户的描述信息
createhome　　  # 是否创建家目录
force　　       # 在使用state=absent时, 行为与userdel –force一致.
group　　       # 指定基本组
groups　　      # 指定附加组，如果指定为(groups=)表示删除所有组
home　　        # 指定用户家目录
move_home　　   # 如果设置为home=时, 试图将用户主目录移动到指定的目录
name　　        # 指定用户名
non_unique　　  # 该选项允许改变非唯一的用户ID值
password　　    # 指定用户密码
remove　　      # 在使用state=absent时, 行为是与userdel –remove一致
shell　　       # 指定默认shell
state　　       # 设置帐号状态，不指定为创建，指定值为absent表示删除
system　　      # 当创建一个用户，设置这个用户是系统用户。这个设置不能更改现有用户
uid　　         # 指定用户的uid
```
添加一个用户并指定其 uid
```bash
[root@localhost ~]# ansible -i hosts/hosts   9999  -m user -a "name=hexo uid=11111"
game-9999 | CHANGED => {
    "changed": true, 
    "comment": "", 
    "create_home": true, 
    "group": 11111, 
    "home": "/home/hexo", 
    "name": "hexo", 
    "shell": "/bin/bash", 
    "state": "present", 
    "system": false, 
    "uid": 11111
}
# 验证
[root@localhost server]# cat /etc/passwd
------------------------------------------
hexo:x:11111:11111::/home/hexo:/bin/bash
[root@localhost server]# 
```
删除用户
```bash
[root@localhost ~]# ansible -i hosts/hosts   9999  -m user -a "name=hexo state=absent"      
game-9999 | CHANGED => {
    "changed": true, 
    "force": false, 
    "name": "hexo", 
    "remove": false, 
    "state": "absent"
}
```
## group 模块
该模块主要用于添加或删除组,常用的选项如下：
```bash
gid=　　        #设置组的GID号
name=　　       #指定组的名称
state=　　      #指定组的状态，默认为创建，设置值为absent为删除
system=　　     #设置值为yes，表示创建为系统组
```
## script 模块
该模块用于将本机的脚本在被管理端的机器上运行,该模块直接指定脚本的路径即可,我们通过例子来看一看到底如何使用的：

首先,我们随便写个测试脚本,并给其加上执行权限：
```bash
[sgsm@ecs-d37b ~]$ chmod a+x  shell.sh 
[sgsm@ecs-d37b ~]$ cat  shell.sh 
#!/bin/bash
df -h >> /tmp/shell.log

a=1
b=2
if [ $a -eq $b ];then
echo "no no no "  >> /tmp/shell.log
else
echo "yes yes yes "  >> /tmp/shell.log
fi
[sgsm@ecs-d37b ~]$ 
```
然后,我们直接运行命令来实现在被管理端执行该脚本：
```bash
[sgsm@ecs-d37b ~]$ ansible -i hosts/hosts   game  -m  script -a "shell.sh"

game-5 | CHANGED => {
    "changed": true, 
    "rc": 0, 
    "stderr": "Shared connection to 192.168.9.20 closed.\r\n", 
    "stderr_lines": [
        "Shared connection to 192.168.9.20 closed."
    ], 
    "stdout": "", 
    "stdout_lines": []
}
# 验证
[sgsm@ecs-d37b ~]$ ansible -i hosts/hosts   game  -m  shell  -a "cat  /tmp/shell.log"

game-5 | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        40G   28G   11G  73% /
devtmpfs        3.9G     0  3.9G   0% /dev
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           3.9G   49M  3.8G   2% /run
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
tmpfs           783M     0  783M   0% /run/user/1000
yes yes yes 
```