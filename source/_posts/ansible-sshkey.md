---
title: Ansible免密登录
tags:
  - ansible
  - linux
categories: 运维
date: 2020-07-21 17:34:22
---
## 首先关闭公钥认证
```bash
# 编辑/etc/ansible/ansible.cfg文件  把 host_key_checking = False 取消注释

[defaults]
host_key_checking = False
```
## 使用ssh-key产生公钥和私钥
```
ssh-keygen 
```
## 添加主机信息到hosts文件中
```bash
[root@localhost ~]# vi  hosts     
# 密码不要写到文件里面,可以执行ansible语句的时候传递
game-1 ansible_ssh_host=192.168.1.90
```
## 编写Playbook剧本文件
```bash
---
# This playbook deploys the whole application stack in this site.
- name: ssh add key
  hosts: "{{ sgsm_hosts }}"
  remote_user: root
  gather_facts: no

  tasks:

  - name: install ssh key
    authorized_key: user=root key="{{ lookup('file', '/root/.ssh/id_rsa.pub') }}" state=present
    when: add_user_flag == "root"

  - name: install ssh key
    authorized_key: user=root key="{{ lookup('file', '/home/test/.ssh/id_rsa.pub') }}" state=present
    when: add_user_flag == "test"

# 注释：hosts指定传递的分组  remote_user为以哪个用户身份执行    gather_facts 关闭认证
# tasks 定义执行的语句    when判断add_user_flag 是那个用户 
```
## 执行playbook文件
```bash
[root@localhost ~]# ansible-playbook  -i  hosts   ssh-key.yaml   --extra-vars "sgsm_hosts=game-1 ansible_ssh_pass=test2 add_user_flag=root"

PLAY [ssh add key] ************************************************************************************************************************************************************************************

TASK [install ssh key] ********************************************************************************************************************************************************************************
changed: [game-1]

TASK [install ssh key] ********************************************************************************************************************************************************************************
skipping: [game-1]

PLAY RECAP ********************************************************************************************************************************************************************************************
game-1                     : ok=1    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
```
## 测试
执行之前
```bash
[root@localhost ~]# ansible  -i hosts  game-1  -m shell -a " df -h "
The authenticity of host '192.168.1.90 (192.168.1.90)' can't be established.
ECDSA key fingerprint is SHA256:2/Ao4y6uxDcraBgV9M2m2Fr4ejfvCbTINgfPD1C046Y.
ECDSA key fingerprint is MD5:0c:d0:c6:2c:8b:1c:2a:9c:78:a6:bf:3a:3d:86:1e:5e.
Are you sure you want to continue connecting (yes/no)? yes
game-1 | UNREACHABLE! => {
    "changed": false, 
    "msg": "Failed to connect to the host via ssh: Warning: Permanently added '192.168.1.90' (ECDSA) to the list of known hosts.\r\nPermission denied (publickey,gssapi-keyex,gssapi-with-mic,password).", 
    "unreachable": true
}
[root@localhost ~]# 
```
免密之后
```bash
[root@localhost ~]# ansible  -i hosts  game-1  -m shell -a " df -h "
game-1 | CHANGED | rc=0 >>
文件系统                 容量  已用  可用 已用% 挂载点
/dev/mapper/centos-root  8.0G  1.1G  6.9G   14% /
devtmpfs                 476M     0  476M    0% /dev
tmpfs                    488M     0  488M    0% /dev/shm
tmpfs                    488M  7.8M  480M    2% /run
tmpfs                    488M     0  488M    0% /sys/fs/cgroup
/dev/sda1               1014M  130M  885M   13% /boot
tmpfs                     98M     0   98M    0% /run/user/0
```
ssh登录测试
```bash
[root@localhost ~]# ssh  192.168.1.90
Last failed login: Wed Jul 22 00:59:51 CST 2020 on tty1
There was 1 failed login attempt since the last successful login.
Last login: Tue Jul 21 17:31:35 2020 from 192.168.1.89
[root@welcome ~]# 
```

## 扩展--ansible内置变量
```bash
ansible_ssh_host
      将要连接的远程主机名.与你想要设定的主机的别名不同的话,可通过此变量设置.

ansible_ssh_port
      ssh端口号.如果不是默认的端口号,通过此变量设置.

ansible_ssh_user
      默认的 ssh 用户名

ansible_ssh_pass
      ssh 密码(这种方式并不安全,我们强烈建议使用 --ask-pass 或 SSH 密钥)

ansible_sudo_pass
      sudo 密码(这种方式并不安全,我们强烈建议使用 --ask-sudo-pass)

ansible_sudo_exe (new in version 1.8)
      sudo 命令路径(适用于1.8及以上版本)

ansible_connection
      与主机的连接类型.比如:local, ssh 或者 paramiko. Ansible 1.2 以前默认使用 paramiko.1.2 以后默认使用 'smart','smart' 方式会根据是否支持 ControlPersist, 来判断'ssh' 方式是否可行.

ansible_ssh_private_key_file
      ssh 使用的私钥文件.适用于有多个密钥,而你不想使用 SSH 代理的情况.

ansible_shell_type
      目标系统的shell类型.默认情况下,命令的执行使用 'sh' 语法,可设置为 'csh' 或 'fish'.

ansible_python_interpreter
      目标主机的 python 路径.适用于的情况: 系统中有多个 Python, 或者命令路径不是"/usr/bin/python",比如  \*BSD, 或者 /usr/bin/python
      不是 2.X 版本的 Python.我们不使用 "/usr/bin/env" 机制,因为这要求远程用户的路径设置正确,且要求 "python" 可执行程序名不可为 python以外的名字(实际有可能名为python26).

      与 ansible_python_interpreter 的工作方式相同,可设定如 ruby 或 perl 的路径....
```
## 查看所有的内置变量
```bash
ansible -i ~/hosts/hosts record  -m setup
用这个 可以看到ansible默认变量值  从而选择合适的变量

{{ansible_all_ipv4_addresses}}

{{ ansible_eth0'ipv4' }}

{{ ansible_default_ipv4['address'] }}
```