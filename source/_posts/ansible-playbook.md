---
title: 自动化运维工具——ansible-playbook
tags:
  - ansible
  - linux
date: 2020-07-08 17:45:48
---
## playbook简介
playbook配置文件使用YAML语法,具有简洁明了、结构清晰等特点,playbook配置文件类似于shell脚本,是一个YAML格式的文件,用于保存针对特定需求的任务列表,前面介绍的ansible命令虽然可以完成各种任务,但是当配置一些复杂任务时,逐条输入就显得效率非常低下了,更有效的方案是在playbook配置文件中放置所有的任务代码,利用ansible-playbook命令执行该文件,可以实现自动化运维,YAML文件的扩展名通常为.yaml或.yml
## playbook的核心元素
```bash
    Hosts 执行的远程主机列表
    Tasks 任务集
    Varniables 内置变量或自定义变量在playbook中调用
    Templates 模板，即使用模板语法的文件，比如配置文件等
    Handlers 和notity结合使用，由特定条件触发的操作，满足条件方才执行，否则不执行
    tags 标签，指定某条任务执行，用于选择运行playbook中的部分代码。

可以理解为
    hosts ： 指明任务运行在哪些主机上
    tasks ： 任务，由模块和定义的参数组成；
    variables ：变量
    templates ：模板文件，表示包含了模板语法的文本文件；
    handlers ： 处理器，触发器，有特定条件触发的任务；
    roles ：角色，由以上所组成的特定结构  
```
## playbook的基础组件
```bash
hosts ：指明运行任务的目标主机，可以是单个也或是冒号隔开的多个主机组；
remote_user ：在远程主机上执行任务的用户；可以定义成全局指定，也可以单任务指定；
sudo_user ：指明以sudo方式运行的方式
tasks ：任务列表；
```
任务的运行顺序
```bash
运行任务是按照任务自上而下在每个主机中挨个运行每个任务 的方式运行的，就是把一个任务在第一台主机上运行完，再去在第二台主机上运行，以 此类推的方式
```
## playbook语法
playbook使用yaml语法格式,后缀可以是yaml,也可以是yml
```bash

    在单一一个playbook文件中，可以连续三个连子号(---)区分多个play。还有选择性的连续三个点好(...)用来表示play的结尾，也可省略。
    次行开始正常写playbook的内容，一般都会写上描述该playbook的功能。
    使用#号注释代码。
    缩进必须统一，不能空格和tab混用。
    缩进的级别也必须是一致的，同样的缩进代表同样的级别，程序判别配置的级别是通过缩进结合换行实现的。
    YAML文件内容和Linux系统大小写判断方式保持一致，是区分大小写的，k/v的值均需大小写敏感
    k/v的值可同行写也可以换行写。同行使用:分隔。
    v可以是个字符串，也可以是一个列表
    一个完整的代码块功能需要最少元素包括 name: task

```
## 示例
安装 httpd 修改端口并启动
```bash

  httpd.yaml
    - hosts：linux
      remote_user：root

    tasks：
    - name：install httpd
      yum：name=httpd state=present
    - name：install configure file
      copy：src=/etc/httpd/conf/httpd.conf dest=/etc/httpd/conf/httpd.conf
    - name : httpd start
      service: name=httpd state=started

# 运行
ansible-playbook  -i hosts/hosts   game   httpd.yaml
```
示例
```bash
# 创建playbook文件
[root@ansible ~]# cat playbook01.yml
---                       #固定格式
- hosts: 192.168.1.31     #定义需要执行主机
  remote_user: root       #远程用户
  vars:                   #定义变量
    http_port: 8088       #变量

  tasks:                             #定义一个任务的开始
    - name: create new file          #定义任务的名称
      file: name=/tmp/playtest.txt state=touch   #调用模块，具体要做的事情
    - name: create new user
      user: name=test02 system=yes shell=/sbin/nologin
    - name: install package
      yum: name=httpd
    - name: config httpd
      template: src=./httpd.conf dest=/etc/httpd/conf/httpd.conf
      notify:                 #定义执行一个动作(action)让handlers来引用执行，与handlers配合使用
        - restart apache      #notify要执行的动作，这里必须与handlers中的name定义内容一致
    - name: copy index.html
      copy: src=/var/www/html/index.html dest=/var/www/html/index.html
    - name: start httpd
      service: name=httpd state=started
  handlers:                                    #处理器：更加tasks中notify定义的action触发执行相应的处理动作
    - name: restart apache                     #要与notify定义的内容相同
      service: name=httpd state=restarted      #触发要执行的动作

#测试页面准备
[root@ansible ~]# echo "<h1>playbook test file</h1>" >>/var/www/html/index.html
#配置文件准备
[root@ansible ~]# cat httpd.conf |grep ^Listen
Listen {{ http_port }}

#执行playbook， 第一次执行可以加-C选项，检查写的playbook是否ok
[root@ansible ~]# ansible-playbook playbook01.yml
PLAY [192.168.1.31] *********************************************************************************************
TASK [Gathering Facts] ******************************************************************************************
ok: [192.168.1.31]
TASK [create new file] ******************************************************************************************
changed: [192.168.1.31]
TASK [create new user] ******************************************************************************************
changed: [192.168.1.31]
TASK [install package] ******************************************************************************************
changed: [192.168.1.31]
TASK [config httpd] *********************************************************************************************
changed: [192.168.1.31]
TASK [copy index.html] ******************************************************************************************
changed: [192.168.1.31]
TASK [start httpd] **********************************************************************************************
changed: [192.168.1.31]
PLAY RECAP ******************************************************************************************************
192.168.1.31               : ok=7    changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 


# 验证上面playbook执行的结果
[root@ansible ~]# ansible 192.168.1.31 -m shell -a 'ls /tmp/playtest.txt && id test02'
192.168.1.31 | CHANGED | rc=0 >>
/tmp/playtest.txt
uid=990(test02) gid=985(test02) 组=985(test02)

[root@ansible ~]# curl 192.168.1.31:8088
<h1>playbook test file</h1>
```
可以分开写到多个目录文件中,如图：
<br/>![](../1.png)<br/>
![](../2.png)

## playbook的运行方式
通过ansible-playbook命令运行,格式：
```bash
ansible-playbook <filename.yml> ... [options]
```
```bash
[root@ansible PlayBook]# ansible-playbook -h
#ansible-playbook常用选项：
--check  or -C    #只检测可能会发生的改变，但不真正执行操作
--list-hosts      #列出运行任务的主机
--list-tags       #列出playbook文件中定义所有的tags
--list-tasks      #列出playbook文件中定义的所以任务集
--limit           #主机列表 只针对主机列表中的某个主机或者某个组执行
-f                #指定并发数，默认为5个
-t                #指定tags运行，运行某一个或者多个tags。（前提playbook中有定义tags）
-v                #显示过程  -vv  -vvv更详细
```
## 其他技巧
```bash
    --inventory=path，指定inventory文件，默认是在/etc/ansible/hosts下面。  可以简写为 -i  ~/hosts/hosts
    --verbose，显示详细的输出，使用-vvvv显示精确到每分钟的输出。
    --extra-vars=vars：定义在playbook使用的变量。
    --forks：指定并发的线程数，默认是5.
    --connection=type:指定远程连接主机的方式，默认是ssh，设置为local时，则只在本地执行playbook、
```

## tasks任务列表
每一个task必须有一个名称name,这样在运行playbook时,从其输出的任务执行信息中可以很清楚的辨别是属于哪一个task的,如果没有定义 name,action的值将会用作输出信息中标记特定的task,
每一个playbook中可以包含一个或者多个tasks任务列表,每一个tasks完成具体的一件事,（任务模块）比如创建一个用户或者安装一个软件等,在hosts中定义的主机或者主机组都将会执行这个被定义的tasks
例如
```bash
tasks:
  - name: create new file
    file: path=/tmp/test01.txt state=touch
  - name: create new user
    user: name=test001 state=present
```
## Handlers与Notify
很多时候当我们某一个配置发生改变,我们需要重启服务,(比如httpd配置文件文件发生改变了)这时候就可以用到handlers和notify了；
(当发生改动时)notify actions会在playbook的每一个task结束时被触发,而且即使有多个不同task通知改动的发生,notify actions知会被触发一次；
比如多个resources指出因为一个配置文件被改动，所以apache需要重启，但是重新启动的操作知会被执行一次。
```bash
[root@ansible ~]# cat httpd.yml 
#用于安装httpd并配置启动
---
- hosts: 192.168.1.31
  remote_user: root

  tasks:
  - name: install httpd
    yum: name=httpd state=installed
  - name: config httpd
    template: src=/root/httpd.conf dest=/etc/httpd/conf/httpd.conf
    notify:
      - restart httpd   
  - name: start httpd
    service: name=httpd state=started

  handlers:
    - name: restart httpd   # 这个命名要和 notify后面相同
      service: name=httpd state=restarted

#这里只要对httpd.conf配置文件作出了修改，修改后需要重启生效，在tasks中定义了restart httpd这个action，然后在handlers中引用上面tasks中定义的notify
```

## 完整剧本案例
![](../2.png)
```bash
## 入口文件
[sgsm@iZ2zebukn5j9itcxgqgn2rZ ansible]$ cat monitor-restart.yml 
---

- name: monitor-restart release game
  hosts: "{{ sgsm_hosts }}"
  remote_user: sgsm

  roles:
      - { role: monitor-restart }
[sgsm@iZ2zebukn5j9itcxgqgn2rZ ansible]$ 


├── monitor-restart.yml     # 入口文件
├── roles					# role  固定格式
│   ├── monitor-restart			# 入口文件制定的目录
│   │   ├── defaults			
│		│	    └── main.yml		# 定义变量文件
            # sgsm 密码
            sgsm_password: "xxxxxx"
            # 基础目录
            common_dir: /data
            # sgsm 项目目录
            #sgsm_dir: "{{ common_dir  }}/sgsm"
            sgsm_dir: "{{ common_dir  }}/sgsm/{{ sgsm_server_uid }}"
            #install package dir 
            common_package_dir: "{{ common_dir }}/package"
            #install package dir 
            common_install_dir: "{{ common_dir }}/install"
            # common lib dir 数据目录
            common_lib_dir: "{{ common_dir  }}/lib"
            # common log_dir 日志目录
            common_log_dir: "{{ common_dir  }}/log"
            # sgms game server run dir 
            sgsm_game_dir: "{{sgsm_dir}}/server"
            # sgsm 发布版本号
            sgsm_release_version: xxxx
            #sgsm script dir 
            sgsm_script_dir: "{{ common_dir }}/script"
            
│   │   ├── files
│		│	    └── test			# ansible中unarchive、copy等模块会自动来这里找文件,只需写文件名
│   │   ├── handlers
│		│	    └── main.yml			# 监听 存放tasks中的notify指定的内容
│   │   ├── meta
│   │   ├── tasks					# 任务元素
│   │   │   ├── main.yml			# 主程序文件
                ---
                - name: monitor-restart
                  include: "sgsm_monitor.yml"
                  when: sgsm_flag == "monitor"
│   │   │     └── sgsm_monitor.yml	# 主程序文件include的文件 (include之后就会消失,import_tasks取代)
                  ---

                  - name: generate monitor.sh
                    template:
                      src: monitor.sh
                      dest: "{{ sgsm_script_dir }}/monitor{{ sgsm_server_uid }}.sh"
                      mode: 0644

│   │   ├── templates
│   │   │     └── monitor.sh			# 模板文件 需要复制到远程服务器的文件
                #!/bin/bash 
                mongod=`ps -aux |grep mongo |wc  -l`     #定义变量
                a=`ps -aux |grep node |grep mail |awk  '{print $(NF-5)}'  |awk  -F"/" '{print $4}' |sort -n |sed -n '1p'` #定义第一个区服
                b=`ps -aux |grep node |grep mail |awk  '{print $(NF-5)}'  |awk  -F"/" '{print $4}' |sort -n |sed -n '2p'` #定义第二个区服
                c=`ps -aux |grep node |grep mail |awk  '{print $(NF-5)}'  |awk  -F"/" '{print $4}' |sort -n |sed -n '3p'` #定义第三个区服

                if [ $mongod  -lt 2 ] ;then   #if 判断  查看mongod的状态
                echo "数据库宕机，正在重启...." && echo {{sgsm_password}} |sudo  -S  systemctl restart mongod  #如果mongo宕机，就重启
                sleep 5
                cd {{ sgsm_game_dir }}/{{ sgsm_release_version }}/server/
                sh stop-server.sh
                sh start-server.sh
                echo "数据库和服务器重启完毕，已恢复正常"
                echo "====================================="

                elif [ $mongod  -ge 2 ] ;then   #if再次判断
                echo "数据库正常，服务器宕机，正在重启...."    #如果进程还在输出正在运行
                cd {{ sgsm_game_dir }}/{{ sgsm_release_version }}/server/
                sh stop-server.sh
                sh start-server.sh
                echo "服务器重启完毕，已恢复正常"
                echo "======================================"
                else
                echo  "reading"
                fi  #if判断结尾
│   │   └── vars
│   │       └── login.yml

# 这个剧本主要的作用是监控,ansible批量把脚本放到远程服务器上等待被调用 -- {{ sgsm_hosts }} 和 {{ sgsm_server_uid }}  在hosts文件中定义
```
调用监控的脚本
```
#/bin/bash
export LANG=zh_CN.UTF-8
a="$1"
echo "$1"  >> /home/sgsm/monitorlog/aa.txt
if [ ! -n "$1" ];then
echo "参数不存在"
else
array=(${a//,/ })    # 传递多个以逗号分隔的参数  使用数组接收 逗号换成空格后的数据    格式：${value//pattern/string}   进行变量内容的替换,把与pattern匹配的部分替换为string的内容
for i in ${array[@]}    # ${nums[@]}  或者 ${nums[*]} 可以获取所有元素 然后for循环
do
ansible -i /home/sgsm/hosts/hosts game-$i -m  command -a "sh /data/script/monitor$i.sh"  >> /home/sgsm/monitorlog/monitor"$1".log
done
###  cat /home/sgsm/monitorlog/monitor$1.log  | mail -s '米花互动服务器监控系统通知邮件---mixtureios2' -c  lichenglong@mihuahd.com  liangxinlong@mihuahd.com   wangzhicheng@mihuahd.com   caojichao@mihuahd.com    dingxue@mihuahd.com
cat /home/sgsm/monitorlog/monitor$1.log  | mail -s "monitor mails"    xxx@163.com
rm -rf  /home/sgsm/monitorlog/monitor"$1".log
fi
```