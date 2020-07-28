---
title: Lvs虚拟服务器
tags:
  - lvs
  - linux
categories: 运维
date: 2020-07-24 14:52:25
---
## lvs简介
LVS是Linux Virtual Server 的简写,意即linux虚拟服务器,是一个虚拟服务器集群系统
负载均衡集群是：load balance 集群的简写,翻译成中文就是负载均衡集群,常用的负载均衡开源软件有nginx、lvs、horxy。硬件有F5 netscale

## lvs的基本工作原理

1、当用户向复杂均衡调用器(Director Server)发起请求,调用器将请求发往内核空间
2、PREROUTING链首先会接收到用户请求,判断目标IP确定是本机IP,将数据包发往INPUT链
3、IPVS是工作在INPUT链上的,当用户的请求到达INPUT时,IPVS会将用户请求和自己已定义好的集群服务进行比对,如果用户请求的就是定义的集群服务,那么此时IPVS会强行修改数据包里面的目标IP地址和端口,并将新的数据发往POSTROUTING链
4、POSTROUTING链收到数据包后发现目标IP地址是自己的后端服务器地址,那么此时通过选路,将数据最终发送到后端服务器

## lvs相关术语
```bash
1、DS：Director Server：指的是其阿奴单负载均衡节点
2、RS：Real Sever：后端真实工作的服务器
3、VIP：对外服务直接接受用户请求,作为用户请求的目标IP地址
4、DIP：Director Server IP ：主要用于和内部主机通讯的IP地址
5、RIP：Real Server IP ：后端服务器的IP地址
6、CIP：Client IP ：客户端的IP地址
```
## lvs三种工作模式
NAT：网络地址转换
DR：直连路由
TUN：隧道模式

### NAT工作原理
1、当用户请求达到Director Server,此时请求的数据报文会先到内核空间的PREROUTING链,此时的报文的源IP为CIP,目标IP为VIP
2、PREROUTING检查发现数据包的目标IP是本机,将数据包发送至INPUT链
3、IPVS比对数据包请求的服务是否为集群服务,若是则修改数据包的目标IP地址为后端服务器IP,然后将数据包发至POSTROUTING链,此时报文的源IP为CIP目标IP为RIP
4、Real Server比对发现目标为自己的地址,开始构建响应报文发回给Director Server。此时的报文的源IP为RIP,目标IP为CIP
5、Director Server 在响应客户端前,此时会将源IP地址修改为自己的VIP地址,然后响应给客户端,此时的报文源IP为VIP,目标IP为CIP

```bash
NAT模型的特性
   RS应该使用私有地址,RS的网关必须指向DIP
   DIP和RIP必须在同一个网段内
   请求和响应报文都需要经过Director Server ,高负载场景中,Director Server易成为性能瓶颈
   支持端口映射
   RS可以使用任意操作系统

缺陷：对Director Server 压力会比较大,请求和响应都需要经过director server
```
### DR模式原理

1、当用户请求到达Director Server,此时请求的数据报文会先到内核空间的PREROUTING链,此时报文的源IP为CIP,目标IP为VIP
2、PREROURING检查发现数据包的目标IP是本机,将数据包发送至INPUT链
3、IPVS比对数据包请求的服务是否为集群服务,若是,将请求报文中源MAC地址修改为DIP的MAC地址,将目标MAC地址修改为RIP的MAC地址,然后将数据包发送至POSTROUING链
4、由于DS和RS在同一个网络中,所以是通过二层传输,POSTROUTING链检查目标MAC地址为RIP的MAC地址,会将数据包发送至Real Server
5、RS发送请求报文的MAC地址是自己的MAC地址,就接受次报文,处理完成后,将响应报文通过lo接口传送给eht0网卡然后向外发出,此时的源地址IP为VIP目标IP为CIP
6、响应报文最终送达至客户端

```bash
DR模型的特性：
  保证前端路由将目标地址为VIP报文统统发给Director Server,而不是RS
  RS可以使用私有地址：也可以是公网地址,如果使用公网地址,此时可以通过互联网对RIP进行直接访问
  RS跟Director Server 必须在同一个物理网络中
  所有的请求报文经由Director Server,但相应报文必须不能经过Director Server
  不支持地址转换,也不支持端口映射
  RS可以是大多数常见的操作系统
  RS的网关决不允许指向DIP（因为我们不允许他经过director）
  RS上的lo接口配置VIP的IP地址

缺陷：RS和DS必须在同一网络
 修改RS上内核参数（arp_ignore和arp_announce）将RS上的VIP配置在lo接口的别名上,并限制其不能响应对VIP地址解析请求
```
### TUN模式原理
1、当用户请求达到Director Server,此时请求的数据报文会先到内核空间的PREROUTING链,此时报文的源IP为CIP,目标IP为VIP
2、PREROUTING检查发现数据包的目标IP是本机,将数据包发送至INPUT链
3、IPVS比对数据包请求的服务是否为集群服务,若是,在请求报文的首部再次封装一层IP报文,封装源IP为DIP,目标IP为RIP,然后发至POSTROUTING链,此时源IP为DIP,目标IP为RIP
4、POSTROUTING链根据最新的封装的IP报文,将数据包发送至RS(因为在外层多封装了一层IP首部,所以可以理解为此时通过隧道传输),此时源IP为DIP,目标IP为DIP
5、RS接收到报文后发现是自己的IP地址,就将报文接收下来。拆除掉最外层的IP后,会发现里面还有一层IP首部,而且目标是自己的lo接口VIP。那么此时RS开始处理此请求,处理完成之后,通过lo接口送给eth0网卡。然后向外传递。此时的源IP地址为VIP,目标IP为CIP
6、响应报文最终送达客户端
```bash
TUN模型特性：
  RIP、VIP、DIP全是公网地址
  RS的网关不会也不可能指向DIP
  所有的请求报文经由Director Server,但相应报文必须不能进过Director Server
  不支持端口映射
  RS的系统必须支持隧道
```
## lvs的八种调度算法
1、轮询调度  rr
```bash
    这种算法是最简单的,就是按照依次循环的方式将请求调度到不同的服务器上,该算法最大的特点就是简单,轮询算法就是假设所有的服务器处理请求的能力都是一样的,调度器会将所有请求平均分配给每个RS,不管后端RS的配置和处理能力,非常均衡的分发下去
```
2、加权轮询  wrr
```bash
    这种算法比rr多了一个权重的概念,可以给RS设置权重,权重越高,那么分发的请求数越多,权重的取值范围是0-100,主要是对 rr 算法的一种优化和补充,lvs会考虑每台服务器的性能,并给每台服务器添加要给的权值,如果服务器A的权值是1,服务器B的权值是2,则调度到服务器B的请求会是服务器A的2倍,权值越高的服务器,处理的请求越多
```
3、最少连接 lc
```bash
    这个算法会根据后端RS的连接数来决定把请求分发给谁,比如RS1连接数比RS2连接数少,那么请求会优先给到RS1
```
4、加权最少连接数 wlc
```bash
    这个算法比lc 多了一个权重的概念类似于wrr
```
5、基于局部性最少连接调度算法 lblc
```bash
    这个算法是请求数据包目标IP地址的一种算法,该算法先根据请求的目标IP地址寻找最近的该目标IP
```
6、复杂的基于局部性最少连接的算法 lblcr
```bash
    记录的不是要给目标IP与一台服务器之前的连接记录,它会维护一个目标IP到一组服务器之间的映射关系,防止单点服务器负载过高
```
7、目标地址散列调度算法 dh
```bash
    该算法是根据目标IP地址通过散列函数将目标IP与服务器建立映射关系,出现服务器不可用或负载过高的情况下,发往该目标IP的请求会固定发给服务器
```
8、原地址散列调度算法 sh
```bash
    与目标地址散列调度算法类似,但它是根据原地址散列算法进行静态分配固定的服务器资源
```
9、最短预期延时调度 sed
```bash
    希望在请求少的时候将请求尽可能转发到性能高的服务器上,sed这种调度算法为了解决WLC的缺点而生,它不再考虑非活动连接。
    sed这种算法也有一定缺陷,在请求量比较少的时候,某个权重下的节点可能一个请求都没有轮到,而权重大的节点却轮到了比较多的请求。
    (活动连接数+1)*256/权重
```
10、不排队调度 nq
```bash
    当有空闲服务器可用时,作业将被发送到空闲服务器,而不是等待快速的服务器。当没有可用的空闲服务器时,作业将被发送到服务器,以最小化其预期延迟（最短预期延迟调度算法）
```
应用场景
```bash
 网络服务
      wrr
      wlc
  web cache
      lblc
      lblcr
  会话保持
      sh
```

NAT模式
