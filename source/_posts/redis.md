---
title: redis基础配置-运维篇
tags:
  - redis
  - Linux
categories: 运维
date: 2020-06-06 15:16:56
---
## Redis 必知
<br/>redis基本的数据结构<br/>
最最最重要的并且是最基础的知识--记不住千万别说了解redis,本人有被羞辱的案例

    string：字符串
    hash：散列
    list：列表
    set：集合
    sorted set：有序集合

## Redis 简介
 Redis 是完全开源免费的，遵守BSD协议，是一个高性能的key-value数据库。
<br/> Redis 与其他 key - value 缓存产品有以下三个特点：<br/>

    Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
    Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
    Redis支持数据的备份，即master-slave模式的数据备份。 

## Redis 优势
性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
<br/>丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。<br/>
原子 – Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。
<br/>丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性<br/>

## Linux 下安装redis
<br/>下载地址：http://redis.io/download，下载最新稳定版本。<br/>

    $ wget http://download.redis.io/releases/redis-2.8.17.tar.gz
    $ tar xzf redis-2.8.17.tar.gz
    $ cd redis-2.8.17
    $ make

 make完后 redis-2.8.17目录下会出现编译后的redis服务程序redis-server,还有用于测试的客户端程序redis-cli,两个程序位于安装目录 src 目录下：

下面启动redis服务

    $ cd src
    $ ./redis-server

注意这种方式启动redis 使用的是默认配置。也可以通过启动参数告诉redis使用指定配置文件使用下面命令启动。 

    $ cd src
    $ ./redis-server ../redis.conf

 redis.conf 是一个默认的配置文件。我们可以根据需要使用自己的配置文件。
<br/>启动redis服务进程后，就可以使用测试客户端程序redis-cli和redis服务交互了。 比如：<br/>

    $ redis-cli -h  192.168.1.163
    192.168.1.163:6379> AUTH mima
    OK
    192.168.1.163:6379> keys *

## Redis 配置文件
主要用到的配置项
```bash    
    # 绑定的IP     redis-cli 的时候需要 加-h 选项 指定ip
    bind 192.168.1.163  

    # redis监听的端口号
    port 6379        

    # 此参数确定了TCP连接中已完成队列(完成三次握手之后)的长度， 当然此值必须不大于Linux系统定义的/proc/sys/net/core/somaxconn值，默认是511，而Linux的默认参数值是128。当系统并发量大并且客户端速度缓慢的时候，可以将这二个参数一起参考设定。该内核参数默认值一般是128，对于负载很大的服务程序来说大大的不够。一般会将它修改为2048或者更大。在/etc/sysctl.conf中添加:net.core.somaxconn = 2048，然后在终端中执行sysctl -p
    tcp-backlog 511

    # 此参数为设置客户端空闲超过timeout，服务端会断开连接，为0则服务端不会主动断开连接，不能小于0
    timeout 300

    # tcp keepalive参数。如果设置不为0，就使用配置tcp的SO_KEEPALIVE值，使用keepalive有两个好处:检测挂掉的对端。降低中间设备出问题而导致网络看似连接却已经与对端端口的问题。在Linux内核中，设置了keepalive，redis会定时给对端发送ack。检测到对端关闭需要两倍的设置值
    tcp-keepalive 300

    # 是否在后台执行，yes：后台运行；no：不是后台运行
    daemonize yes

    # redis进程文件路径
    pidfile /var/run/redis.pid

    # 日志等级
    loglevel notice

    # 日志路径
    logfile /data/log/redis/redis-server.log

    # 设置db库数量，默认16个库
    databases 16

    # 在900 秒内有一个键内容发生更改触发快照机制
    save 900 1

    # 在300 秒内有10个键内容发生更改触发快照机制
    save 300 10

    # 在10000 秒内有60个键内容发生更改触发快照机制
    save 60 10000

    # 久化到 RDB 文件时，是否压缩，"yes" 为压缩，“no” 则反之
    rdbcompression yes

    # 是否开启RC64校验，默认是开启
    rdbchecksum yes

    # 快照文件名
    dbfilename dump.rdb

    # 快照文件路径
    dir /data/lib/redis

    # 如果slave-serve-stale-data设置为yes(默认设置)，从库会继续响应客户端的请求。2) 如果slave-serve-stale-data设置为no，INFO,replicaOF, AUTH, PING, SHUTDOWN, REPLCONF, ROLE, CONFIG,SUBSCRIBE, UNSUBSCRIBE,PSUBSCRIBE, PUNSUBSCRIBE, PUBLISH, PUBSUB,COMMAND, POST, HOST: and LATENCY命令之外的任何请求都会返回一个错误”SYNC with master in progress”。
    slave-serve-stale-data yes

    # 配置Redis的Slave实例是否接受写操作，即Slave是否为只读Redis。默认值为yes
    slave-read-only yes

    # 主从数据复制是否使用无硬盘复制功能。默认值为no。
    repl-diskless-sync no

    # 当启用无硬盘备份，服务器等待一段时间后才会通过套接字向从站传送RDB文件，这个等待时间是可配置的。  这一点很重要，因为一旦传送开始，就不可能再为一个新到达的从站服务。从站则要排队等待下一次RDB传送。因此服务器等待一段  时间以期更多的从站到达。延迟时间以秒为单位，默认为5秒。要关掉这一功能，只需将它设置为0秒，传送会立即启动。默认值为5
    repl-diskless-sync-delay 5

    # 同步之后是否禁用从站上的TCP_NODELAY 如果你选择yes，redis会使用较少量的TCP包和带宽向从站发送数据。但这会导致在从站增加一点数据的延时。  Linux内核默认配置情况下最多40毫秒的延时。如果选择no，从站的数据延时不会那么多，但备份需要的带宽相对较多。默认情况下我们将潜在因素优化，但在高负载情况下或者在主从站都跳的情况下，把它切换为yes是个好主意。默认值为no。
    repl-disable-tcp-nodelay no

    # 当 master 不可用，Sentinel 会根据 slave 的优先级选举一个 master 。最低的优先级的 slave ，当选 master 。而配置成 0，永远不会被选举
    slave-priority 100

    # 是否开启 AOF 日志 记录 默认 redis使用的是 rdb 方式持久化，这种方式在许多应用中已经足够用了。但是 redis 如果中途宕机，会导致可能有几分钟的数据丢失
    appendonly no

    # 指定本地数据库文件名，默认值为 appendonly.aof
    appendfilename "appendonly.aof"

    # aof 持久化策略的配置 no 表示不执行 fsync 由操作系统保证数据同步到磁盘 ,always 表示每次写入都执行 fsync ，以保证数据同步到磁盘 ,everysec 表示每秒执行一次 fsync ，可能会导致丢失这 1s 数据。
    appendfsync everysec

    # （推荐为yes） 在 aof rewrite 期间 是否对 aof 新记录的 append 暂缓使用文件同步策略 主要考虑磁盘 IO 开支和请求阻塞时间。默认为 no, 表示不暂缓新的 aof 记录仍然会被立即同步Linux 的默认fsync策略是30 秒，如果为 yes 可能丢失 30 秒数据 ，但由于yes性能较好,而且会避免出现阻塞, 因此比较推荐
    no-appendfsync-on-rewrite yes

    # 当 Aof log增长超过指定百分比例时，重写 logfile设置为0表示不自动重写 Aof 日志，重写是为了使 aof 体积保持最小，而确保保存最完整的数据
    auto-aof-rewrite-percentage 100

    # # 触发 aof rewrite 的最小文件大小
    auto-aof-rewrite-min-size 64mb

    # aof文件可能在尾部是不完整的，当redis启动的时候，aof文件的数据被载入内存。重启可能发生在redis所在的主机操作系统宕机后，尤其在ext4文件系统没有加上data=ordered选项（redis宕机或者异常终止不会造成尾部不完整现象。）出现这种现象，可以选择让redis退出，或者导入尽可能多的数据。如果选择的是yes，当截断的aof文件被导入的时候，会自动发布一个log给客户端然后load。如果是no，用户必须手动redis-check-aof修复AOF文件才可以
    aof-load-truncated yes

    # 一个lua脚本执行的最大时间，单位为ms。默认值为5000
    lua-time-limit 5000

    # 只记录大于等于下边设置的值的操作。0的话，就是关闭监视。默认延迟监控功能是关闭的，如果你需要打开，也可以通过CONFIG SET命令动态设置
    latency-monitor-threshold 0

    # 数据量小于等于hash-max-ziplist-entries的用ziplist，大于hash-max-ziplist-entries用hash
    hash-max-ziplist-entries 512

    # value大小小于等于hash-max-ziplist-value的用ziplist，大于hash-max-ziplist-value用hash
    hash-max-ziplist-value 64

    #-5:最大大小：64 KB<--不建议用于正常工作负载
    #-4:最大大小：32 KB<--不推荐
    #-3:最大大小：16 KB<--可能不推荐
    #-2:最大大小：8kb<--良好
    #-1:最大大小：4kb<--良好
    list-max-ziplist-size -2

    # 数据量小于等于set-max-intset-entries用iniset，大于set-max-intset-entries用set
    set-max-intset-entries 512

    # 数据量小于等于zset-max-ziplist-entries用ziplist，大于zset-max-ziplist-entries用zset
    zset-max-ziplist-entries 128

    # value大小小于等于zset-max-ziplist-value用ziplist，大于zset-max-ziplist-value用zset
    zset-max-ziplist-value 64

    # rename-command：命令重命名，对于一些危险命令例如  FLUSHDB（清空数据库）　FLUSHALL（清空所有记录） CONFIG（客户端连接后可配置服务器）  EVAL (Eval 命令使用 Lua 解释器执行脚本)
    rename-command FLUSHALL ""
    rename-command FLUSHDB ""
    rename-command CONFIG ""
    rename-command EVAL ""

    # redis 认证密码
    requirepass GwJMMSdSHezfeMRMP34fQ0F0F
```
## Redis 语法

连接redis

    redis-cli -h host -p port -a password

查看所有的键值对--一般大公司是禁止使用keys *

    192.168.1.163:6379> keys *

正则表达式匹配键值对

    192.168.1.163:6379> keys *info*water*
    1) "info:"
    2) "info:hero"
    3) "info:serverInfo"

查看hash值

    192.168.1.163:6379> HGETALL "xxxxxx"
    1) "1001"
    2) "\"{\\\"roleInfoList\\\": xxxxx}\""

查看有序集合

    192.168.1.163:6379> ZREVRANGE "xxxxxxxx" 0 10      # 0表示第一个      
    1) "id"
    192.168.1.163:6379> ZREVRANGE "xxxxxxxx" 0 10  WITHSCORES   #  加上WITHSCORES可以打印积分
    1) "id"
    2) "integral"

删除有序集合成员

     ZREM  key  value

删除键值

    del  key

修改集合

    hset keys   更改后的内容

备份

    redis 192.168.1.163:6379> SAVE 
    OK

## Redis迁移

先备份

    [root@izm5ea99qngm2vazfs49svz ~]# redis-cli 
    127.0.0.1:6379>  AUTH mima      #  认证
    OK
    127.0.0.1:6379>  SAVE         # 保存数据
    OK
    127.0.0.1:6379>  CONFIG GET dir       # 查看保存数据位置
    1) "dir"
    2) "/var/lib/redis"

需要先把远程服务器的redis停止  然后备份一下当前的快照 不然直接scp过去 会有问题

    cd  /var/lib/redis
    mv   dump.rdb  dump.rdb.bak

把快照文件发送到远程服务器

    cd  /var/lib/redis  
    scp    dump.rdb   user@IP:/var/lib/redis

启动redis

    sudo  redis-server   /etc/redis/redis.conf

进去redis查看是否迁移成功

    [root@test ~]# redis-cli 
    127.0.0.1:6379>  AUTH mima  
    OK
    127.0.0.1:6379>  keys *
    1) "info:"
    2) "info:hero"
    3) "info:serverInfo"


命令行执行redis语句

    redis-cli  -h  127.0.0.1  -p 6379  -a "mima"  del  

    echo  'set aaa aaaa' |redis-cli -h   127.0.0.1  -a mima

附一个redis批量操作的脚本
```bash
    server=$1     # 传入的第一个参数定义为server
    host=$2     # 传入的第二个参数定义为host
    echo   "AUTH mima  
    ZREVRANGE  chart:newbox@$server:6  0 -1 " > $1.txt        # echo 密码和 语句到$1.txt文件
    cat  $1.txt |   redis-cli  -h  $host    >  $1_linshi.txt    命令行执行redis语句   语句内容就是$1.txt文件中的内容   执行结果重定向到  $1_linshi.txt 


    cat $1_linshi.txt   |sed  1d  >  $1_redis.txt     # 删除 $1_linshi.txt  文件的第一行


    echo   "AUTH mima"  > insert.txt      # echo 密码到insert.txt文件
    for role in  `cat  $1_redis.txt`
    do
      
      echo "ZADD  chart:newbox@$server:6  0  $role "  >> insert.txt
      
    done            # for循环 $1_linshi.txt 文件   添加0 到每个键值对中
    cat  insert.txt |   redis-cli  -h  $host     执行redis语句
```