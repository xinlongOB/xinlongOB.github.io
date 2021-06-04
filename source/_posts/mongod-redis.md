---
title: mongod-redis
tags:
  - python
  - linux
date: 2021-06-04 15:27:21
---
## mongo
mongodb占用内存非常高，这是因为官方为了提升存储的效率，设计就这么设计的。

但是大部分的个人开发者所购买的服务器内存并没有那么大，所以，我们需要配置下MongoDB的内存缓存大小，不然mongodb会占用非常多。


以mongodb 3.2为例，WiredTiger内部缓存，默认会用掉

    60% * 内存 - 1GB
    1GB

当你的内存大于1GB，mongodb会用掉 内存的60% - 1GB 的内存作为缓存；

当你的内存小于1GB，mongodb会直接用掉1GB。




```bash
下面是修改后的配置：/etc/mongod.conf

# Where and how to store data.
storage:
  dbPath: /var/lib/mongo
  #dbPath: /mongodata 
  journal:
    enabled: true
#  engine:
  mmapv1:
    smallFiles: true
  wiredTiger:
    engineConfig:
      configString : cache_size=512M

其实重点就是下面一项，配置之后，重启mongodb生效：

	
wiredTiger:
    engineConfig:
      configString : cache_size=512M
```


## redis
maxmemory：设置Redis的最大内存，如果设置为0 。表示不作限制。通常是配合下面介绍的maxmemory-policy参数一起使
maxmemory-policy ：当内存使用达到maxmemory设置的最大值时，redis使用的内存清除策略。有以下几种可以选择：


设置了maxmemory的选项，redis内存使用达到上限。可以通过设置LRU算法来删除部分key，释放空间。默认是按照过期时间的,如果set时候没有加上过期时间就会导致数据写满maxmemory

LRU是Least Recently Used 近期最少使用算法。

volatile-lru -> 根据LRU算法生成的过期时间来删除。

allkeys-lru -> 根据LRU算法删除任何key。

volatile-random -> 根据过期设置来随机删除key。

allkeys-random -> 无差别随机删。

volatile-ttl -> 根据最近过期时间来删除(辅以TTL)

noeviction -> 谁也不删，直接在写操作时返回错误。

如果设置了maxmemory，一般都要设置过期策略。打开Redis的配置文件有如下描述，Redis有六种过期策略：

