---
title: Shell 循环日期时间执行
tags:
  - shell
  - linux
date: 2020-07-20 20:09:22
---
因为for无法直接循环20:00:00这种格式的时间,所以需要先转为时间戳,循环时间戳然后在循环中在把时间戳转为想要的时间格式
```bash
#!/bin/bash
# shell : sh date.sh   2020-07-20 10:00:00  2020-07-20 10:00:20   1010   create
# 获取传递的时间然后转换为时间戳
date="$1 $2"
start_time=`date -d "$date" +%s`
date2="$3 $4"
end_time=`date -d "$date2" +%s`
heroid=$5
keyword=$6
# for 循环时间戳   也可以写为
# for (( i=$start_time;i<=$end_time;i+=1 ))
for i in `seq $start_time  $end_time`
do
# 把时间戳在转换为时间
logdate=`date "+%Y-%m-%d %H:%M:%S" -d @$i`
echo $logdate
logday=`echo $logdate |awk '{print $1}'`
echo $logday
cat  ../../logs/tlog-$logday.*   |grep  $logdate  |grep   $heroid   |grep   $keyword
done
```