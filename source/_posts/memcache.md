---
title: Memcache 数据库缓存服务器
tags:
  - memcache
  - linux
date: 2020-07-23 14:14:00
---
## Memcached 的特征
Memcached作为高速运行的分布式缓存服务器,具有以下特征
```bash
    协议简单
    基于libevent的事件处理
    内置内存存储方式
    Memcached不互相通信的分布式
```
协议简单
    Memcached的服务器客户端通信并不适用复杂的XML等格式,而是使用简单的基于文本行的协议,因此,通过telnet也能在memcached上保存数据、取得数据。下面是例子
```bash
    $ telnet localhost 11211
	    Trying 127.0.0.1...
	    Connected to localhost.localdomain (127.0.0.1).
	    Escape character is '^]'.
	    set test 0 0 3        保存命令
	    bar                   数据
	    STORED                结果
	    get test              取得命令
	    VALUE test 0 3        数据
	    bar                   数据
```
基于libevent的事件处理

    libevent是个程序库,他将linux的epoll、BSD类操作系统的kqueue等事件处理功能封装成统一的接口,
    即使对服务器的连接数增加,也能发挥0（1）的性能Memcache使用这个libevent库,因此能在linux、
    BSD、Solaris等操作系统上发挥其高性能

内置内存存储方式

    为了提高性能,memcached中保存的数据都存储在memcache内置的内存存储空间中,由于数据仅存在于内存中,
    因此重启memcached、重启操作系统会导致全部数据消失,另外,内容量达到指定值之后,就基于LRU
    （least Recently Used）算法自动删除不使用的缓存

Memcached不互相通信的分布式

    Memcached尽管是“分布式”缓存服务器,但服务器端并没有分布式功能,各个memcached不会互相通信以共享信息,
    那么,怎样进行分布式呢？完全取决于客户端的实现

## Memcached 的安装
安装依赖库
```bash
yum install libevent  libevent-devel  -y
```
安装memcache
```bash
yum install memcached   telnet -y
```
启动memcached服务
```bash
memcached -m 16m -p 11211 -d -u root -c 8192
# 查看端口是否被监听
[root@localhost ~]# lsof  -i :11211
COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
memcached 1453 root   26u  IPv4  24874      0t0  TCP *:memcache (LISTEN)
memcached 1453 root   27u  IPv6  24875      0t0  TCP *:memcache (LISTEN)
memcached 1453 root   28u  IPv4  24878      0t0  UDP *:memcache 
memcached 1453 root   29u  IPv6  24879      0t0  UDP *:memcache 
[root@localhost ~]# 
```
```bash
启动选项：

    -d    是启动一个守护进程；
    -m    是分配给Memcache使用的内存数量,单位是MB；
    -u    是运行Memcache的用户；
    -l    是监听的服务器IP地址,可以有多个地址；
    -p    是设置Memcache监听的端口,最好是1024以上的端口,（默认设置为：11211）；
    -U    UDP监听端口（默认：11211,0 时关闭）
    -c    max simultanous  connectios  最大并发  连接数（default：1024）
    -P    是设置保存Memcache的pid文件,将PID写入文件<file>,这样可以使得后边进行快速进程终止,需要与-d一起使用
```
## Memcached 连接
我们可以通过 telnet 命令并指定主机ip和端口来连接 Memcached 服务, 语法：
```bash
telnet  HOST PORT
```
实例
```bash
telnet 127.0.0.1 11211

Trying 127.0.0.1...

Connected to 127.0.0.1.

Escape character is '^]'.

set foo 0 0 3                                                   保存命令

bar                                                             数据

STORED                                                          结果

get foo                                                         取得命令

VALUE foo 0 3                                                   数据

bar                                                             数据

END                                                             结束行

quit                                                            退出
```
## Memcached 命令
### 存储命令-set
Memcached set 命令用于将 value(数据值)存储在指定的 key(键) 中,如果set的key已经存在,该命令可以更新该key所对应的原来的数据,也就是实现更新的作用
set 命令的基本语法格式如下：
```bash
    set key flags exptime bytes [noreply] 
    value 
```
参数说明如下：
```bash
    key：键值 key-value 结构中的 key,用于查找缓存值。
    flags：可以包括键值对的整型参数,客户机使用它存储关于键值对的额外信息 。
    exptime：在缓存中保存键值对的时间长度（以秒为单位,0 表示永远）
    bytes：在缓存中存储的字节数
    noreply（可选）： 该参数告知服务器不需要返回数据
    value：存储的值（始终位于第二行）（可直接理解为key-value结构中的value）
```
实例：
```bash
## 以下实例中我们设置：
    key → runoob
    flag → 0
    exptime → 900 (以秒为单位)
    bytes → 9 (数据存储的字节数)
    value → memcached


[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
set runoob 0 900 9    # 创建一个键 0是标示位     900 为生存周期    9位长度
memcached     # key的值
STORED    # 如果数据设置成功,则输出：STORED

get runoob     # 查看键值
VALUE runoob 0 9
memcached
END
quit
Connection closed by foreign host.
[root@localhost ~]# 
```
### 存储命令-add
Memcached add 命令用于将 value(数据值) 存储在指定的 key(键) 中,如果 add 的 key 已经存在,则不会更新数据(过期的 key 会更新),之前的值将仍然保持相同,并且您将获得响应 NOT_STORED
add 命令的基本语法格式如下：
```bash
    add key flags exptime bytes [noreply]
    value
```
参数说明如下：
```bash
    key：键值 key-value 结构中的 key,用于查找缓存值。
    flags：可以包括键值对的整型参数,客户机使用它存储关于键值对的额外信息 。
    exptime：在缓存中保存键值对的时间长度（以秒为单位,0 表示永远）
    bytes：在缓存中存储的字节数
    noreply（可选）： 该参数告知服务器不需要返回数据
    value：存储的值（始终位于第二行）（可直接理解为key-value结构中的value）
```
实例：
```bash
## 以下实例中我们设置：

    key → new_key
    flag → 0
    exptime → 900 (以秒为单位)
    bytes → 10 (数据存储的字节数)
    value → data_value

[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
add new_key 0 900 10
data_value
STORED

get new_key
VALUE new_key 0 10
data_value
END
quit
Connection closed by foreign host.
[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
add new_key 0 900 10
data_value
NOT_STORED   # 再次添加报错  NOT_STORED
```
### 存储命令-replace
Memcached replace 命令用于替换已存在的 key(键) 的 value(数据值),如果 key 不存在,则替换失败,并且您将获得响应 NOT_STORED
replace 命令的基本语法格式如下：
```bash
    replace key flags exptime bytes [noreply]
    value
```
参数说明如下：
```bash
    key：键值 key-value 结构中的 key,用于查找缓存值
    flags：可以包括键值对的整型参数,客户机使用它存储关于键值对的额外信息
    exptime：在缓存中保存键值对的时间长度（以秒为单位,0 表示永远）
    bytes：在缓存中存储的字节数
    noreply（可选）： 该参数告知服务器不需要返回数据
    value：存储的值（始终位于第二行）（可直接理解为key-value结构中的value）
```
实例：
```bash
## 以下实例中我们设置：

    key → mykey
    flag → 0
    exptime → 900 (以秒为单位)
    bytes → 10 (数据存储的字节数)
    value → data_value

[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
add mykey 0 900 10     # 添加
data_value
STORED

get mykey         # 查看
VALUE mykey 0 10
data_value
END

replace mykey 0 900 16      # 替换
some_other_value
STORED

get mykey       # 查看
VALUE mykey 0 16
some_other_value
END
```
### 存储命令-append 
Memcached append 命令用于向已存在 key(键) 的 value(数据值) 后面追加数据
append 命令的基本语法格式如下：
```bash
    append key flags exptime bytes [noreply]
    value
```
参数说明如下：
```bash
    key：键值 key-value 结构中的 key,用于查找缓存值。
    flags：可以包括键值对的整型参数,客户机使用它存储关于键值对的额外信息 。
    exptime：在缓存中保存键值对的时间长度（以秒为单位,0 表示永远）
    bytes：在缓存中存储的字节数
    noreply（可选）： 该参数告知服务器不需要返回数据
    value：存储的值（始终位于第二行）（可直接理解为key-value结构中的value）
```
实例：
```bash

    首先我们在 Memcached 中存储一个键 runoob,其值为 memcached。
    然后,我们使用 get 命令检索该值。
    然后,我们使用 append 命令在键为 runoob 的值后面追加 "redis"。
    最后,我们再使用 get 命令检索该值。

[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
set runoob 0 900 9   # 添加
memcached
STORED

get runoob      # 查看
VALUE runoob 0 9
memcached
END

append runoob 0 900 5   # 追加
redis
STORED

get runoob      # 查看
VALUE runoob 0 14
memcachedredis
END
```
### 存储命令-prepend 
Memcached prepend 命令用于向已存在 key(键) 的 value(数据值) 前面追加数据
prepend 命令的基本语法格式如下：
```bash
    prepend key flags exptime bytes [noreply]
    value
```
参数说明如下：
```bash
    key：键值 key-value 结构中的 key,用于查找缓存值。
    flags：可以包括键值对的整型参数,客户机使用它存储关于键值对的额外信息 。
    exptime：在缓存中保存键值对的时间长度（以秒为单位,0 表示永远）
    bytes：在缓存中存储的字节数
    noreply（可选）： 该参数告知服务器不需要返回数据
    value：存储的值（始终位于第二行）（可直接理解为key-value结构中的value）
```
实例：
```bash

    首先我们在 Memcached 中存储一个键 runoob,其值为 memcached。
    然后,我们使用 get 命令检索该值。
    然后,我们使用 prepend 命令在键为 runoob 的值前面追加 "redis"。
    最后,我们再使用 get 命令检索该值。

[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
set runoob 0 900 9      # 添加
memcached
STORED

get runoob          # 查看
VALUE runoob 0 9
memcached
END

prepend runoob 0 900 5    # 在前面追加
redis
STORED

get runoob          # 查看
VALUE runoob 0 14
redismemcached
END
```
输出信息说明：
```bash
    STORED：保存成功后输出。
    NOT_STORED：该键在 Memcached 上不存在。
    CLIENT_ERROR：执行错误。
```
### 存储命令-cas
Memcached CAS（Check-And-Set 或 Compare-And-Swap） 命令用于执行一个"检查并设置"的操作
它仅在当前客户端最后一次取值后,该key 对应的值没有被其他客户端修改的情况下, 才能够将值写入。
检查是通过cas_token参数进行的, 这个参数是Memcach指定给已经存在的元素的一个唯一的64位值。
CAS 命令的基本语法格式如下：
```bash
    cas key flags exptime bytes unique_cas_token [noreply]
    value
```
参数说明如下：
```bash
    key：键值 key-value 结构中的 key,用于查找缓存值。
    flags：可以包括键值对的整型参数,客户机使用它存储关于键值对的额外信息 。
    exptime：在缓存中保存键值对的时间长度（以秒为单位,0 表示永远）
    bytes：在缓存中存储的字节数
    unique_cas_token通过 gets 命令获取的一个唯一的64位值。
    noreply（可选）： 该参数告知服务器不需要返回数据
    value：存储的值（始终位于第二行）（可直接理解为key-value结构中的value）
```
实例:
```bash
要在 Memcached 上使用 CAS 命令,你需要从 Memcached 服务商通过 gets 命令获取令牌（token）。

gets 命令的功能类似于基本的 get 命令。两个命令之间的差异在于,gets 返回的信息稍微多一些：64 位的整型值非常像名称/值对的 "版本" 标识符。

实例步骤如下：

    如果没有设置唯一令牌,则 CAS 命令执行错误。
    如果键 key 不存在,执行失败。
    添加键值对。
    通过 gets 命令获取唯一令牌。
    使用 cas 命令更新数据
    使用 get 命令查看数据是否更新
```
```bash
[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
cas tp 0 900 9
ERROR             <− 缺少 token

cas tp 0 900 9 2
memcached
NOT_FOUND         <− 键 tp 不存在

set tp 0 900 9
memcached
STORED

gets tp
VALUE tp 0 9 1
memcached
END

cas tp 0 900 5 1
redis
STORED

get tp
VALUE tp 0 5
redis
END
```
### 查找命令-get
Memcached get 命令获取存储在 key(键) 中的 value(数据值) ,如果 key 不存在,则返回空。
get 命令的基本语法格式如下：
```bash
    get key
    # 
    多个 key 使用空格隔开,如下:
    get key1 key2 key3
```
参数说明如下：
```bash
    key：键值 key-value 结构中的 key,用于查找缓存值
```
实例：
```bash
在以下实例中,我们使用 runoob 作为 key,过期时间设置为 900 秒。
[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
set runoob 0 900 9
memcached
STORED

get runoob
VALUE runoob 0 9
memcached
END
```
### 查找命令-gets
Memcached gets 命令获取带有 CAS 令牌存 的 value(数据值) ,如果 key 不存在,则返回空。
gets 命令的基本语法格式如下：
```bash
    gets key
    ##
    多个 key 使用空格隔开,如下:
    gets key1 key2 key3
```
参数说明如下：
```bash
    key：键值 key-value 结构中的 key,用于查找缓存值
```
实例：
```bash
在以下实例中,我们使用 runoob 作为 key,过期时间设置为 900 秒。
[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
set runoob 0 900 9
memcached
STORED

gets runoob
VALUE runoob 0 9 1
memcached
END
```
在 使用 gets 命令的输出结果中,在最后一列的数字 1 代表了 key 为 runoob 的 CAS 令牌。
### 删除命令-delete
Memcached delete 命令用于删除已存在的 key(键)
delete 命令的基本语法格式如下：
```bash
    delete key [noreply]
```
参数说明如下：
```bash
    key：键值 key-value 结构中的 key,用于查找缓存值。
    noreply（可选）： 该参数告知服务器不需要返回数据
```
实例：
```bash
在以下实例中,我们使用 runoob 作为 key,过期时间设置为 900 秒。之后我们使用 delete 命令删除该 key
[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
set runoob 0 900 9    # 添加
memcached
STORED

get runoob        # 查看
VALUE runoob 0 9
memcached
END

delete runoob     # 删除
DELETED

get runoob      # 再次查看 返回空
END

delete runoob   # 再次删除返回key 不存在
NOT_FOUND
```
输出信息说明：
```bash
    DELETED：删除成功。
    ERROR：语法错误或删除失败。
    NOT_FOUND：key 不存在。
```
### 统计命令-stats 
Memcached stats 命令用于返回统计信息例如 PID(进程号)、版本号、连接数等
stats 命令的基本语法格式如下：
```bash
    stats
```
实例：
```bash
在以下实例中,我们使用了 stats 命令来输出 Memcached 服务信息。
[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
stats

STAT pid 1453
STAT uptime 3561
STAT time 1595492923
STAT version 1.4.15
STAT libevent 2.0.21-stable
STAT pointer_size 64
STAT rusage_user 0.073713
STAT rusage_system 0.085999
STAT curr_connections 10
STAT total_connections 23
STAT connection_structures 11
STAT reserved_fds 20
STAT cmd_get 14
STAT cmd_set 16
STAT cmd_flush 0
STAT cmd_touch 0
STAT get_hits 12
STAT get_misses 2
STAT delete_misses 1
STAT delete_hits 1
STAT incr_misses 0
STAT incr_hits 0
STAT decr_misses 0
STAT decr_hits 0
STAT cas_misses 1
STAT cas_hits 0
STAT cas_badval 2
STAT touch_hits 0
STAT touch_misses 0
STAT auth_cmds 0
STAT auth_errors 0
STAT bytes_read 871
STAT bytes_written 773
STAT limit_maxbytes 16777216
STAT accepting_conns 1
STAT listen_disabled_num 0
STAT threads 4
STAT conn_yields 0
STAT hash_power_level 16
STAT hash_bytes 524288
STAT hash_is_expanding 0
STAT bytes 153
STAT curr_items 2
STAT total_items 11
STAT expired_unfetched 0
STAT evicted_unfetched 0
STAT evictions 0
STAT reclaimed 4
END
这里显示了很多状态信息,下边详细解释每个状态项：

    pid： memcache服务器进程ID
    uptime：服务器已运行秒数
    time：服务器当前Unix时间戳
    version：memcache版本
    pointer_size：操作系统指针大小
    rusage_user：进程累计用户时间
    rusage_system：进程累计系统时间
    curr_connections：当前连接数量
    total_connections：Memcached运行以来连接总数
    connection_structures：Memcached分配的连接结构数量
    cmd_get：get命令请求次数
    cmd_set：set命令请求次数
    cmd_flush：flush命令请求次数
    get_hits：get命令命中次数
    get_misses：get命令未命中次数
    delete_misses：delete命令未命中次数
    delete_hits：delete命令命中次数
    incr_misses：incr命令未命中次数
    incr_hits：incr命令命中次数
    decr_misses：decr命令未命中次数
    decr_hits：decr命令命中次数
    cas_misses：cas命令未命中次数
    cas_hits：cas命令命中次数
    cas_badval：使用擦拭次数
    auth_cmds：认证命令处理的次数
    auth_errors：认证失败数目
    bytes_read：读取总字节数
    bytes_written：发送总字节数
    limit_maxbytes：分配的内存总大小（字节）
    accepting_conns：服务器是否达到过最大连接（0/1）
    listen_disabled_num：失效的监听数
    threads：当前线程数
    conn_yields：连接操作主动放弃数目
    bytes：当前存储占用的字节数
    curr_items：当前存储的数据总数
    total_items：启动以来存储的数据总数
    evictions：LRU释放的对象数目
    reclaimed：已过期的数据条目来存储新数据的数目
```
### 统计命令-stats items
Memcached stats items 命令用于显示各个 slab 中 item 的数目和存储时长(最后一次访问距离现在的秒数)
stats items 命令的基本语法格式如下：
```bash
    stats items
```
实例：
```bash
[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
stats items 

STAT items:1:number 2
STAT items:1:age 1090
STAT items:1:evicted 0
STAT items:1:evicted_nonzero 0
STAT items:1:evicted_time 0
STAT items:1:outofmemory 0
STAT items:1:tailrepairs 0
STAT items:1:reclaimed 4
STAT items:1:expired_unfetched 0
STAT items:1:evicted_unfetched 0
END
```
### 统计命令-stats slabs
Memcached stats slabs 命令用于显示各个slab的信息,包括chunk的大小、数目、使用情况等
stats slabs 命令的基本语法格式如下：
```bash
    stats slabs
```
实例：
```bash
[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
stats slabs

STAT 1:chunk_size 96
STAT 1:chunks_per_page 10922
STAT 1:total_pages 1
STAT 1:total_chunks 10922
STAT 1:used_chunks 2
STAT 1:free_chunks 10920
STAT 1:free_chunks_end 0
STAT 1:mem_requested 153
STAT 1:get_hits 12
STAT 1:cmd_set 16
STAT 1:delete_hits 1
STAT 1:incr_hits 0
STAT 1:decr_hits 0
STAT 1:cas_hits 0
STAT 1:cas_badval 2
STAT 1:touch_hits 0
STAT active_slabs 1
STAT total_malloced 1048512
END
```
### 统计命令-stats sizes
Memcached stats sizes 命令用于显示所有item的大小和个数。
该信息返回两列,第一列是 item 的大小,第二列是 item 的个数。
stats sizes 命令的基本语法格式如下：
```bash
    stats sizes
```
实例：
```bash
[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
stats sizes

STAT 96 2
END
```
### 清除所有-flush_all
Memcached flush_all 命令用于清理缓存中的所有 key=>value(键=>值) 对。
该命令提供了一个可选参数 time,用于在制定的时间后执行清理缓存操作
flush_all 命令的基本语法格式如下：
```bash
    flush_all [time] [noreply]
```
实例：
```bash
# 清除所有缓存
[root@localhost ~]# telnet  localhost  11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.
set runoob 0 900 9
memcached
STORED

get runoob
VALUE runoob 0 9
memcached
END

flush_all
OK

get runoob
END
```

## Memcached 客户端安装
安装软件包
```bash
yum install php-devel  php-mysql  php  mysql  mysql-server   -y   
# 如果是centos7  执行下面的命令
yum install gcc gcc-c++ zlib-devel -y
yum install php-devel   php-mariadb   php  mariadb   mariadb-server   -y
yum install   wget   lsof telnet  -y
# 安装memcache      地址：http://pecl.php.net/package/memcache
wget   http://pecl.php.net/get/memcache-3.0.7.tgz

tar zxf    memcache-3.0.7.tgz  -C  /usr/src/

cd  /usr/src/memcache-3.0.7/
# 执行phpize生成configure文件
phpize 

#编译安装
./configure && make && make install

# 安装后查看模块是否安装成功
ll /usr/lib64/php/modules/memcache.so

# 在 /etc/php.d/  新添加一个文件：
cp mysql.ini memcache.ini
# 修改文件
vi   memcache.ini
    extension=memcache.so
```
验证memcahce是否安装成功,需要拷贝源码包的 example.php文件到apache的网页目录下
```bash
安装 httpd
yum install  http  -y
# 复制文件
cp /usr/src/memcache-3.0.7/example.php   /var/www/html/
# 修改example.php的连接memcached服务器的地址和端口
vi  /var/www/html/example.php

    $memcache = memcache_connect('192.168.1.89', 11211);
```
启动httpd然后通过浏览器访问,关闭防火墙或者放行端口(memcache服务器也需要关闭防火墙或者放行端口)
![](../11.png)
这个界面表示正常
## 管理 Memcached
拷贝源码包下memcache.php到apache网页目录下通过访问来管理memcache
```bash
# 复制文件
cp  /usr/src/memcache-3.0.7/memcache.php       /var/www/html/
# 修改/var/www/html/memcache.php 
vi  /var/www/html/memcache.php
    源：
    define('ADMIN_USERNAME','memcache');    // Admin Username
    define('ADMIN_PASSWORD','password');    // Admin Password
    define('DATE_FORMAT','Y/m/d H:i:s');
    define('GRAPH_SIZE',200);
    define('MAX_ITEM_DUMP',50);

    $MEMCACHE_SERVERS[] = 'mymemcache-server1:11211'; // add more as an array
    $MEMCACHE_SERVERS[] = 'mymemcache-server2:11211'; // add more as an array

    改为：

    define('ADMIN_USERNAME','memcache');    // Admin Username
    define('ADMIN_PASSWORD','password');    // Admin Password
    define('DATE_FORMAT','Y/m/d H:i:s');
    define('GRAPH_SIZE',200);
    define('MAX_ITEM_DUMP',50);

    $MEMCACHE_SERVERS[] = '192.168.1.89:11211'; // add more as an array
    // $MEMCACHE_SERVERS[] = 'mymemcache-server2:11211'; // add more as an array
```
通过网页访问
![](../2.png)
![](../3.png)

缓存session
```bash
修改 /etc/php.ini
		session.save_handler = memcache
		session.save_path = "tcp://192.168.1.89:11211"
```

## Memcache总结
memcached 问题

    缓存雪崩
    缓存雪崩一般是由某个缓存节点失效,导致其他节点的缓存命中率下降, 缓存中缺失的数据
    去数据库查询.短时间内,造成数据库服务器崩溃.
    数据丢失
    memcached 数据丢失,设为永久有效,却莫名其妙的丢失了。
    1:如果 slab 里的很多 chunk,已经过期,但过期后没有被 get 过, 系统不知他们已经过期. 2:永久数据很久没 get 了,不活跃,如果新增 item,则永久数据被踢了. 3: 当然,如果那些非永久数据被 get,也会被标识为 expire,从而不会再踢掉永久数据
    方案：永久数据和非永久数据分开放。


优点
```bash
 一.部分容灾
假设只用一台memcache,如果这台memcache服务器挂掉了,那么请求将不断的冲击数据库,这样有可能搞死数据库,从而引发”雪崩“。如果使用多台memcache服务器,由于memcache使用一致性哈希算法,万一其中一台挂掉了,部分请求还是可以在memcache中命中,为修复系统赢得一些时间。

 二.容量问题
一台memcache服务器的容量毕竟有限,可以使用多台memcache服务器,增加缓存容量。

 三.均衡请求
使用多台memcache服务器,可以均衡请求,避免所有请求都冲进一台memcache服务器,导致服务器挂掉。

四.利用memcache分布式特性
使用一台memcache服务器,并没有利用memcache的数据分布式特性。

    稳定、配置简单
    速度快 (因为Memcache是运行在内存中的,所以它的速度是非常快的)
    可以保存的item数据量是没有限制的,只要内存足够
```
缺点
```bash
   1.不能持久化存储：最大30天的数据过期时间,即使设置成永久,也仅存储30天
   2.存储数据有限制:    最大键长为250字节,超过则无法存储
                       单个item最大数据1M,超过则无法存储
                       最大同时连接数是200
                       最大软连接数是1024
   3.mm存储数据只能key-value
   4.集群数据没有复制和同步机制 【崩溃不会影响程序,会从数据库中取数据】
   5.内存回收不能及时  LRU(算法)：未使用内存》过期内存》最近最少使用内存   这是惰性删除
```