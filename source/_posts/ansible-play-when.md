---
title: ansible-playbook之when条件语句
tags:
  - ansible
  - linux
categories: 运维
date: 2020-08-26 14:04:18
---
## When语句
有时候用户有可能需要某一个主机越过某一个特定的步骤.这个过程就可以简单的像在某一个特定版本的系统上少装了一个包一样或者像在一个满了的文件系统上执行清理操作一样.
这些操作在Ansible上,若使用when语句都异常简单.When语句也含Jinja2表达式
例
```bash
tasks:
  - name: "shutdown Debian flavored systems"
    command: /sbin/shutdown -t now
    when: ansible_os_family == "Debian"
```
如果你在RedHat系列linux系统执行，就不会被执行


判断变量是否已经定义
```bash
tasks:
    - shell: echo "I've got '{{ foo }}' and am not afraid to use it!"
      when: foo is defined
    - fail: msg="Bailing out. this play requires 'bar'"
      when: bar is not defined
```
例如：
```bash
# copy file to targets
- name: del old package tar file
  file: path="/test/test1.txt" state=absent 
  when: sgsm_state_tar == "del" 

- name: copy file to targets
  copy: src=/test/test1.txt dest=/test
  when: ecy_intranet is not defined
  
# Intranet copy
- name: copy file to targets 
  copy: src=/data/test1.txt dest=/test
  when: ecy_intranet is defined
```
```bash
[root@localhost /home/sgsm/support_ecy/ansible]# ansible-playbook -i hosts-ecy copy.yml --extra-vars "sgsm_flag=support_ecy sgsm_hosts=aa sgsm_release_version=trunk_ecy sgsm_state_tar=del  ecy_intranet=yes"

PLAY [copy file to targets] ************************************************************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************************************
ok: [aa-jump-1]

TASK [copy : del old package tar file] *************************************************************************************************************************************************************
changed: [aa-jump-1]

TASK [copy : copy file to targets] *****************************************************************************************************************************************************************
skipping: [aa-jump-1]

TASK [copy : copy file to targets in intranet] *****************************************************************************************************************************************************
changed: [aa-jump-1]

PLAY RECAP *****************************************************************************************************************************************************************************************
aa-jump-1               : ok=3    changed=2    unreachable=0    failed=0   

[root@localhost /home/sgsm/support_ecy/ansible]# 
```
如果传递了ecy_intranet这个变量     ecy_intranet is not defined  这个tasks就不会执行了

如果没有传递ecy_intranet 这个变量   ecy_intranet is defined  这个 tasks就不会执行了


