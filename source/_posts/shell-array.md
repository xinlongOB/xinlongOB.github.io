---
title: shell数组的应用
tags:
  - shell
  - linux
categories: 运维
date: 2020-08-06 14:01:30
---
## 什么是数组
Shell数组就是一个元素的集合，它有限个数的元素（变量和字符内容），用一个名字来命名，用下标进行区分，这个名字就称为数组名，组成数组的变量称之为数组元素，使用数组可以缩短和简化程序开发
## 定义与增删改查
```bash
# 查询数组所有元素
[sgsm@ipason monitorlog]$ array=(1 2 3)  
[sgsm@ipason monitorlog]$ echo ${array[*]}
1 2 3
[sgsm@ipason monitorlog]$ num=(1 2 3 4 5) 
[sgsm@ipason monitorlog]$ echo  ${num[@]}  
1 2 3 4 5
[sgsm@ipason monitorlog]$ 
```
```bash
# 显示数组的第一个元素
[sgsm@ipason monitorlog]$ array=(1 2 3)  
[sgsm@ipason monitorlog]$ echo ${array[0]}
1
```
```bash
# 获取数组的元素个数
[sgsm@ipason monitorlog]$ array=(1 2 3)  
[sgsm@ipason monitorlog]$ echo  ${#array[*]}
3
[sgsm@ipason monitorlog]$ 
```
数组的赋值
```bash
# 索引为3 的值为4
[sgsm@ipason monitorlog]$ array=(1 2 3)  
[sgsm@ipason monitorlog]$ array[3]=4
[sgsm@ipason monitorlog]$ echo  ${array[*]} 
1 2 3 4
[sgsm@ipason monitorlog]$ 
```
数组的删除
```bash
# 删除第二个元素
[sgsm@ipason monitorlog]$ echo  ${array[*]} 
1 2 3 4
[sgsm@ipason monitorlog]$ unset array[1]
[sgsm@ipason monitorlog]$ echo  ${array[*]} 
1 3 4
[sgsm@ipason monitorlog]$ 
```
数组内容的切片和替换
```bash
# 截取第2个至第四个
[sgsm@ipason monitorlog]$ a=(1 2 3 4 5 6 7)
[sgsm@ipason monitorlog]$ echo  ${a[*]}     
1 2 3 4 5 6 7
[sgsm@ipason monitorlog]$ echo  ${a[*]:1:3} 
2 3 4
[sgsm@ipason monitorlog]$ 
```
替换
```bash
# 把 "," 替换为空格
[sgsm@ipason monitorlog]$ num=(1,2,3,4,5)
[sgsm@ipason monitorlog]$ array=(${num//,/ })      
[sgsm@ipason monitorlog]$ echo  ${array[*]} 
1 2 3 4 5
[sgsm@ipason monitorlog]$ num=(1,2,3,4,5)    
[sgsm@ipason monitorlog]$ array=(${num//,/+})
[sgsm@ipason monitorlog]$ echo  ${array[*]}  
1+2+3+4+5
```
循环1,2,3,4,5 
```bash
[sgsm@ipason monitorlog]$ cat  for.sh 
#!/bin/bash
num=(1,2,3,4,5)
array=(${num//,/ })
for i in ${array[*]}
do
echo $i
done
[sgsm@ipason monitorlog]$ sh for.sh   
1
2
3
4
5
[sgsm@ipason monitorlog]$ 

```
```bash
# 替换数组里面的值
[sgsm@ipason monitorlog]$ a=(1 2 3 4 5 6 7)
[sgsm@ipason monitorlog]$ echo  ${a[*]} 
1 2 3 4 5 6 7
[sgsm@ipason monitorlog]$ echo  ${a[*]/2/b}  
1 b 3 4 5 6 7
[sgsm@ipason monitorlog]$ 
```