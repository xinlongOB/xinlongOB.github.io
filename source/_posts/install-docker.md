---
title: docker安装部署以及Dockerfile
tags:
  - docker
  linux
categories: 运维
date: 2020-06-11 15:36:06
---
## docker安装准备
关闭防火墙和selinux
<br/>centos6<br/>

```bash
sudo  service  iptables  stop
sudo  service  ip6tables  stop
```
centos7
```bash
sudo  systemctl stop  firewalld
```
关闭开启自启动
```bash
sudo  chkconfig  iptables  off   
sudo  chkconfig  ip6tables  off   
```
临时关闭selinux
```bash
sudo  setenforce 0
```
永久关闭selinux
```bash
sudo  vim  /etc/selinux/config
        SELINUX=disabled
```
重启生效


Docker 要求 CentOS 系统的内核版本高于 3.10，查看本页面的前提条件来验证你的CentOS 版本是否支持 Docker
```bash
[sgsm@localhost ~]$ uname -r
3.10.0-514.21.2.el7.x86_64
[sgsm@localhost ~]$ 
```
如果版本较低需要升级内容
```bash
# 更新yum库
yum update   
# 升级内核版本（包含aufs）
cd /etc/yum.repos.d
wget http://www.hop5.in/yum/el6/hop5.repo
yum install kernel-ml-aufs kernel-ml-aufs-devel -y
```
更新后需要修改配置文件
```bash
sudo  vi /etc/grub.conf
    # 把默认的引导文件设置为0。因为升级内核之后，新的内核在第一个（0）位置
    default=0   
```
最后重启系统,再次查看就是3.10版本了

## docker安装部署
因为epel源中就有docker所以直接用epel源安装
```bash
sudo  yum install  epel-release   -y
sudo  yum install  docker-io   -y
sudo  yum install docker-ce -y
```
启动docker
```bash
sudo  systemctl start  docker
```

## docker 镜像
搜寻镜像 $docker search 关键字
```bash
[sgsm@localhost ~]$ sudo docker  search  centos7.2  
INDEX       NAME                                             DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/kecikeci/centos7.2-tools               centos7.2+阿里云yum源+ssh密码登录+常用软件 基于官方centos7...   3                    
docker.io   docker.io/13652604711/centos7.2-ssh                                                              2                    
docker.io   docker.io/hasonl/centos7.2                       Centos7.2   System                              1                    
docker.io   docker.io/sssllc/centos7.2-jdk1.8                centos-release-7-2.1511.el7.centos.2.10.x8...   1                    
docker.io   docker.io/yasanbee/centos7.2-systemd             CentOS 7.2 Base Image Dockerfile with syst...   1                    [OK]
docker.io   docker.io/10sr/centos7.2-python2.7               centos7.2-python2.7                             0                    [OK]
docker.io   docker.io/692383247/centos7.2                    centos7.2                                       0                    
docker.io   docker.io/92docker/centos7.2                                                                     0                    
docker.io   docker.io/ailyfeng/centos7.2.1511                centos7.2.1511基础配置                              0                    
docker.io   docker.io/chaoduoli/centos7.2-ssh                账号和密码都为root                                     0                    
docker.io   docker.io/coxy/centos7.2-vagrant                 CentOS 7.2 for Vagrant use. Contains SSH, ...   0                    
docker.io   docker.io/daisuke310vvv/centos7.2-java1.7.0                                                      0                    
docker.io   docker.io/dock2box/centos7.2.1511                                                                0                    
docker.io   docker.io/ekzm/centos7.2.1511                                                                    0                    
docker.io   docker.io/elain/centos7.2                        centos7.2 基础镜像                                  0                    
docker.io   docker.io/gengyanping/centos7.2                                                                  0                    
docker.io   docker.io/hizhangsir/centos7.2-jdk1.8            base                                            0                    
docker.io   docker.io/jiezhiz/centos7.2                      base image from centos7.2                       0                    
docker.io   docker.io/pangyu/centos7.2_java                                                                  0                    
docker.io   docker.io/plsicloud/centos7.2-iperf3                                                             0                    
docker.io   docker.io/plsicloud/centos7.2-openmpi2.0.1                                                       0                    
docker.io   docker.io/plsicloud/centos7.2-openmpi2.0.2-hpl                                                   0                    
docker.io   docker.io/qxp2181/centos7.2_mono_jexus           centos7.2_mono_jexus                            0                    
docker.io   docker.io/sibylai/centos7.2-ssh                                                                  0                    
docker.io   docker.io/wodrow/centos7.2                                                                       0                    
[sgsm@localhost ~]$ 
```
下载镜像 $ docker pull 镜像名
```bash
[sgsm@localhost ~]$ sudo docker  pull alpine         
Using default tag: latest
Trying to pull repository docker.io/library/alpine ... 
latest: Pulling from docker.io/library/alpine
df20fa9351a1: Pull complete 
Digest: sha256:185518070891758909c9f839cf4ca393ee977ac378609f700f60a771a2dfe321
Status: Downloaded newer image for docker.io/alpine:latest
[sgsm@localhost ~]$ 
```
dokcer run hello-world
<br/>pull镜像测试  如果pull失败   需要更改镜像源<br/>

```bash
sudo  vim /etc/docker/daemon.json
	{
	"registry-mirrors": ["https://alzgoonw.mirror.aliyuncs.com"]
	}
```
重启docker
```bash
sudo systemctl restart  docker
```
默认docker pull的镜像都是最新版本   指定版本需要 docker pull centos:centos7    更精确为 docker pull centos:centos7.2.1511
<br/>例如：<br/>

```bash
sudo  docker pull centos:centos7.2.1511
```
查看docker 镜像
```bash
[root@localhost ~]# docker images
REPOSITORY                                                           TAG                 IMAGE ID            CREATED             SIZE
web                                                                  v1.1                84f2139ad784        6 weeks ago         285 MB
docker.io/alpine                                                     latest              f70734b6a266        2 months ago        5.61 MB
docker.io/nginx                                                      latest              602e111c06b6        2 months ago        127 MB
docker.io/centos                                                     latest              470671670cac        5 months ago        237 MB
docker.io/centos                                                     centos7             5e35e350aded        7 months ago        203 MB
docker.io/centos                                                     centos7.2.1511      9aec5c5fe4ba        15 months ago       195 MB
k8s.gcr.io/kube-apiserver                                            v1.13.0             f1ff9b7e3d6e        18 months ago       181 MB
registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver   v1.13.0             f1ff9b7e3d6e        18 months ago       181 MB
[root@localhost ~]# 
```
参数解释：

	REPOSITORY：仓库名称
	TAG：标签名，一个仓库可以有若干个标签对应不同的镜像，默认都是latest
	IMAGE ID：镜像ID
	CREATED：创建时间，注意不是本地的pull时间
	SIZE：镜像大小

删除镜像
```bash
# 在删除docker镜像前需先删除所有使用此镜像的所有容器才可删除镜像
docker rmi <image id>
```

## 容器操作
创建容器
```bash
docker run -it  --privileged=true  -v /bak:/soft  --name web   -d  centos:centos7.2.1511 /usr/sbin/init 
    # 选项
     --privileged=true  赋予root权限,此root不是系统用户root,而是docker容器内的root权限, 不指定在容器内无法启动服务
     -v /bak:/soft    挂载   -v 宿主机目录:容器内目录   
     --name    容器名字
     -d   后台运行
     centos:centos7.2.1511    指定创建容器所使用的的镜像
     /usr/sbin/init        不指定在容器内无法启动服务 报错：Fai led to get D-BuS connect ion: operation not permitted

```
查看所有容器
```bash
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE                   COMMAND             CREATED             STATUS                                PORTS                            NAMES
56da5506c5a1        centos:centos7.2.1511   "/usr/sbin/init"    6 weeks ago         Exited (137) Less than a second ago                                    web
```
查看正在运行的容器
```bash
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                            NAMES
ccc0f0ca5334        84f2139ad784        "/usr/sbin/init"    6 weeks ago         Up 6 weeks          9001/tcp, 0.0.0.0:9090->80/tcp   test
[root@localhost ~]# 
```
选项

    -a：查看所有容器，含停止运行的
    -l：查看刚启动的容器
    -q：只显示容器ID
    -s:显示容器大小
    -n=4: 列出最近创建的4个容器

查看在容器里做过的操作
```bash
docker  diff  容器id
```
查看容器运行信息docker stats
   <br/> docker stats 可以查看到运行状态容器的CPU，内存及网络使用率<br/>

```bash
[root@localhost ~]# docker stats  ccc0f0ca5334
CONTAINER           CPU %               MEM USAGE / LIMIT      MEM %               NET I/O             BLOCK I/O           PIDS
ccc0f0ca5334        0.01%               1000 KiB / 7.605 GiB   0.01%               7.3 kB / 4.76 kB    531 MB / 179 MB     12
CONTAINER           CPU %               MEM USAGE / LIMIT      MEM %               NET I/O             BLOCK I/O           PIDS
ccc0f0ca5334        0.01%               1000 KiB / 7.605 GiB   0.01%               7.3 kB / 4.76 kB    531 MB / 179 MB     12
CONTAINER           CPU %               MEM USAGE / LIMIT      MEM %               NET I/O             BLOCK I/O           PIDS
ccc0f0ca5334        0.08%               1000 KiB / 7.605 GiB   0.01%               7.3 kB / 4.76 kB    531 MB / 179 MB     12
CONTAINER           CPU %               MEM USAGE / LIMIT      MEM %               NET I/O             BLOCK I/O           PIDS
ccc0f0ca5334        0.08%               1000 KiB / 7.605 GiB   0.01%               7.3 kB / 4.76 kB    531 MB / 179 MB     12
CONTAINER           CPU %               MEM USAGE / LIMIT      MEM %               NET I/O             BLOCK I/O           PIDS
ccc0f0ca5334        0.00%               1000 KiB / 7.605 GiB   0.01%               7.3 kB / 4.76 kB    531 MB / 179 MB     12
CONTAINER           CPU %               MEM USAGE / LIMIT      MEM %               NET I/O             BLOCK I/O           PIDS
ccc0f0ca5334        0.00%               1000 KiB / 7.605 GiB   0.01%               7.3 kB / 4.76 kB    531 MB / 179 MB     12
^C
[root@localhost ~]# 
```
查看 Docker 容器或镜像的一些内部信息：
```bash
 docker inspect  ccc0f0ca5334
```
进入容器
```bash 
 docker exec -it  web  /bin/bash
```
退出容器不结束容器
```bash
使用 ctrl+p + ctrl+q    或者   ctrl+a  + ctrl+d
```
容器的删除
```bash
# 删除已停止中的容器：
docker rm  容器id
# 删除正常运行的容器：
docker rm -f  容器id
```
关闭容器
```bash
docker  stop  容器id或者容器name
```

## 容器内操作
```bash
yum install epel-release -y
yum install net-tools   httpd  vim  elinks    passwd openssl openssh-server     openssl-client  -y
 /usr/sbin/sshd -D &  # 启动sshd  方便之后的ansible
然后配置yum源为宿主机的ip     # 宿主机上需要开服httpd    并且在访问站点目录解压sgsm.zip包

配置好yum源安装nodejs-4.4-*
 yum install  subversion    nodejs-4.4*  -y   # 因为测试环境需要svn检出代码 所以需要安装subversion

 svn  co  代码路径

##  如果有报错 字符集有问题需要调整  
  export LC_CTYPE="zh_CN.UTF-8"
  export LANG="zh_CN.UTF-8"
  export LC_CTYPE="zh_CN.GB2312"
  然后在svn co
  如果还不行    使用下面的方法
	vim   /etc/profile 
		在最后插入
			export LC_ALL=en_US.UTF-8
			export LANG=en_US.UTF-8
			export LANGUAGE=en_US.UTF-8
		保存退出
	source  /etc/profile    #  source 立即生效
```

  
  
  
  
  
## 容器启动后端口映射
首先停止容器
```bash
sudo docker stop test
```
然后停止服务
```bash
sudo systemctl stop docker
```	

修改配置文件&emsp;&emsp; ( [hash_of_the_container] 为容器id)
```
vim /var/lib/docker/containers/[hash_of_the_container]/hostconfig.json
在 hostconfig.json 里有 "PortBindings":{} 这个配置项，可以改成 "PortBindings":{"9001/tcp":[{"HostIp":"","HostPort":"900"}]}
																				前者为容器端口，后者为宿主机端口
```

修改config.v2.json	&emsp;	&emsp;	(## 如果容器内端口从没有暴露 需要修改这个文件)
```bash
vim /var/lib/docker/containers/[hash_of_the_container]/config.v2.json
在 config.v2.json 里面添加一个配置项 "ExposedPorts":{"80/tcp":{}} , ## 必须将这个配置项添加到 "Tty": true, 前面
```
最后重启 docker的守护进程 
```bash
systemctl restart  docker
```
然后需要启动容器id 
```bash
docker start  test
```
查看配置项已经修改成功
```bash
[root@localhost ~]# docker ps 
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                            NAMES
ccc0f0ca5334        84f2139ad784        "/usr/sbin/init"    6 weeks ago         Up 6 weeks          9001/tcp, 0.0.0.0:9090->80/tcp   test
```

##  Dockerfile
docker指令
<br/>指令：FROM <br/>

```bash
    功能描述：设置基础镜像 
    语法：FROM < image>[:< tag> | @< digest>] 
    提示：镜像都是从一个基础镜像（操作系统或其他镜像）生成，可以在一个Dockerfile中添加多条FROM指令，一次生成多个镜像 
    注意：如果忽略tag选项，会使用latest镜像
```
指令：MAINTAINER 
```bash
    功能描述：设置镜像作者 
    语法：MAINTAINER < name>
```
指令：RUN 
```bash
    功能描述： 
    语法：RUN < command> 
              RUN [“executable”,”param1”,”param2”] 
    提示：RUN指令会生成容器，在容器中执行脚本，容器使用当前镜像，脚本指令完成后，Docker Daemon会将该容器提交为一个中间镜像，供后面的指令使用 
    补充：RUN指令第一种方式为shell方式，使用/bin/sh -c < command>运行脚本，可以在其中使用\将脚本分为多行 
              RUN指令第二种方式为exec方式，镜像中没有/bin/sh或者要使用其他shell时使用该方式，其不会调用shell命令 
    例子：RUN source $HOME/.bashrc;\ 
              echo $HOME

              RUN [“/bin/bash”,”-c”,”echo hello”]

              RUN [“sh”,”-c”,”echo”,”$HOME”] 使用第二种方式调用shell读取环境变量
```
指令：CMD 
```bash
    功能描述：设置容器的启动命令 
    语法：CMD [“executable”,”param1”,”param2”] 
              CMD [“param1”,”param2”] 
              CMD < command> 
    提示：CMD第一种、第三种方式和RUN类似，第二种方式为ENTRYPOINT参数方式，为entrypoint提供参数列表 
    注意：Dockerfile中只能有一条CMD命令，如果写了多条则最后一条生效
```
指令：LABEL 
```bash
    功能描述：设置镜像的标签 
    延伸：镜像标签可以通过docker inspect查看 
    格式：LABEL < key>=< value> < key>=< value> … 
    提示：不同标签之间通过空格隔开 
    注意：每条指令都会生成一个镜像层，Docker中镜像最多只能有127层，如果超出Docker Daemon就会报错，如LABEL ..=.. <假装这里有个换行> LABEL ..=..合在一起用空格分隔就可以减少镜像层数量，同样，可以使用连接符\将脚本分为多行 
              镜像会继承基础镜像中的标签，如果存在同名标签则会覆盖
```
指令：EXPOSE 
```bash
    功能描述：设置镜像暴露端口，记录容器启动时监听哪些端口 
    语法：EXPOSE < port> < port> … 
    延伸：镜像暴露端口可以通过docker inspect查看 
    提示：容器启动时，Docker Daemon会扫描镜像中暴露的端口，如果加入-P参数，Docker Daemon会把镜像中所有暴露端口导出，并为每个暴露端口分配一个随机的主机端口（暴露端口是容器监听端口，主机端口为外部访问容器的端口） 
    注意：EXPOSE只设置暴露端口并不导出端口，只有启动容器时使用-P/-p才导出端口，这个时候才能通过外部访问容器提供的服务
```
指令：ENV 
```bash
    功能描述：设置镜像中的环境变量 
    语法：ENV < key>=< value>…|< key> < value> 
    注意：环境变量在整个编译周期都有效，第一种方式可设置多个环境变量，第二种方式只设置一个环境变量 
    提示：通过${变量名}或者 $变量名使用变量，使用方式${变量名}时可以用${变量名:-default} ${变量名:+cover}设定默认值或者覆盖值 
              ENV设置的变量值在整个编译过程中总是保持不变的
```
指令：ADD 
```bash
    功能描述：复制文件到镜像中 
    语法：ADD < src>… < dest>|[“< src>”,… “< dest>”] 
    注意：当路径中有空格时，需要使用第二种方式 
              当src为文件或目录时，Docker Daemon会从编译目录寻找这些文件或目录，而dest为镜像中的绝对路径或者相对于WORKDIR的路径 
    提示：src为目录时，复制目录中所有内容，包括文件系统的元数据，但不包括目录本身 
              src为压缩文件，并且压缩方式为gzip,bzip2或xz时，指令会将其解压为目录 
              如果src为文件，则复制文件和元数据 
              如果dest不存在，指令会自动创建dest和缺失的上级目录
```
指令：COPY 
```bash
    功能描述：复制文件到镜像中 
    语法：COPY < src>… < dest>|[“< src>”,… “< dest>”] 
    提示：指令逻辑和ADD十分相似，同样Docker Daemon会从编译目录寻找文件或目录，dest为镜像中的绝对路径或者相对于WORKDIR的路径
```
指令：ENTRYPOINT
```bash 
    功能描述：设置容器的入口程序 
    语法：ENTRYPOINT [“executable”,”param1”,”param2”] 
              ENTRYPOINT command param1 param2（shell方式） 
    提示：入口程序是容器启动时执行的程序，docker run中最后的命令将作为参数传递给入口程序 
              入口程序有两种格式：exec、shell，其中shell使用/bin/sh -c运行入口程序，此时入口程序不能接收信号量 
              当Dockerfile有多条ENTRYPOINT时只有最后的ENTRYPOINT指令生效 
              如果使用脚本作为入口程序，需要保证脚本的最后一个程序能够接收信号量，可以在脚本最后使用exec或gosu启动传入脚本的命令 
    注意：通过shell方式启动入口程序时，会忽略CMD指令和docker run中的参数 
              为了保证容器能够接受docker stop发送的信号量，需要通过exec启动程序；如果没有加入exec命令，则在启动容器时容器会出现两个进程，并且使用docker stop命令容器无法正常退出（无法接受SIGTERM信号），超时后docker stop发送SIGKILL，强制停止容器 
    例子：FROM ubuntu <换行> ENTRYPOINT exec top -b
```
指令：VOLUME 
```bash
    功能描述：设置容器的挂载点 
    语法：VOLUME [“/data”] 
              VOLUME /data1 /data2 
    提示：启动容器时，Docker Daemon会新建挂载点，并用镜像中的数据初始化挂载点，可以将主机目录或数据卷容器挂载到这些挂载点
```
指令：USER 
```bash
    功能描述：设置RUN CMD ENTRYPOINT的用户名或UID 
    语法：USER < name>
```
指令：WORKDIR 
```bash
    功能描述：设置RUN CMD ENTRYPOINT ADD COPY指令的工作目录 
    语法：WORKDIR < Path> 
    提示：如果工作目录不存在，则Docker Daemon会自动创建 
              Dockerfile中多个地方都可以调用WORKDIR，如果后面跟的是相对位置，则会跟在上条WORKDIR指定路径后（如WORKDIR /A   WORKDIR B   WORKDIR C，最终路径为/A/B/C）
```
指令：ARG 
```bash
    功能描述：设置编译变量 
    语法：ARG < name>[=< defaultValue>] 
    注意：ARG从定义它的地方开始生效而不是调用的地方，在ARG之前调用编译变量总为空，在编译镜像时，可以通过docker build –build-arg < var>=< value>设置变量，如果var没有通过ARG定义则Daemon会报错 
              可以使用ENV或ARG设置RUN使用的变量，如果同名则ENV定义的值会覆盖ARG定义的值，与ENV不同，ARG的变量值在编译过程中是可变的，会对比使用编译缓存造成影响（ARG值不同则编译过程也不同） 
    例子：ARG CONT_IMAG_VER <换行> RUN echo $CONT_IMG_VER 
              ARG CONT_IMAG_VER <换行> RUN echo hello 
              当编译时给ARG变量赋值hello，则两个Dockerfile可以使用相同的中间镜像，如果不为hello，则不能使用同一个中间镜像
```
指令：ONBUILD 
```bash
    功能描述：设置自径想的编译钩子指令 
    语法：ONBUILD [INSTRUCTION] 
    提示：从该镜像生成子镜像，在子镜像的编译过程中，首先会执行父镜像中的ONBUILD指令，所有编译指令都可以成为钩子指令
```
指令：STOPSIGNAL 
```bash
    功能描述：设置容器退出时，Docker Daemon向容器发送的信号量 
    语法：STOPSIGNAL signal 
    提示：信号量可以是数字或者信号量的名字，如9或者SIGKILL，信号量的数字说明在Linux系统管理中有简单介绍
```

写好Dockerfile后放到一个目录中  	&emsp;	&emsp; --Dockerfile里所需要copy 或者add的文件要在同目录下
```bash
docker build   -t  web:v1.1 .      --  -t指定名称 和 版本    . 代表上下级目录
```
启动docker
```bash
docker run -it  --privileged=true  --name test   -p 9090:80  -d   84f2139ad784   /usr/sbin/init    
#  --privileged=true 可以在容器中有权限启动服务     -p 端口映射 9090宿主机端口  80 容器端口
``` 
```bash
##多个端口映射
docker run -it  --privileged=true  --name lin   -p 90:80  -p 1111:9001 -p 2222:20001  -d   9aec5c5fe4ba   /usr/sbin/init  
```

dockerfile案例
```bash
# Base images 基础镜像
FROM centos:centos7.2.1511

#MAINTAINER 维护者信息
MAINTAINER xinlong 

#ADD  文件放在当前目录下，拷过去会自动解压
ADD node.repo.bak   /etc/yum.repos.d/

#RUN 执行以下命令 
RUN yum clean all \
        && yum install    httpd    -y 

#WORKDIR 相当于cd
WORKDIR /var/www/html

RUN echo "dockerfile test"  >  index.html

#EXPOSE 映射端口
EXPOSE 9001
EXPOSE 20001

CMD systemctl start httpd   
```
```bash

# Base images 基础镜像
FROM centos7.2.1511

#MAINTAINER 维护者信息
MAINTAINER xinlong 

#ADD  文件放在当前目录下，如果是压缩文件拷过去会自动解压
ADD node.repo   /etc/yum.repos.d/

#RUN 执行以下命令 
RUN yum clean all   \
	&& yum install epel-release -y	\
	&& yum install net-tools   httpd  vim  elinks    passwd openssl openssh-server     openssl-client  -y	\
	&& /usr/sbin/sshd -D &
	

RUN /data/sgsm/1/web/trunk
RUN mkdir /data/sgsm/   -p \
	mkdir /data/sgsm/1/	\
	mkdir         /data/sgsm/1/{boss_pvp,chart_pvp,chat_pvp,city_pvp,consume_pvp,convey_pvp,five_pvp,login,ore_pvp,pay,pvp,recharge_pvp,secret_pvp,server,servers_pvp,sky,team_pvp,video,web,wusheng_pvp}   \
	mkdir /data/sgsm/1/web/{logs,trunk} 

	
#WORKDIR 相当于cd
WORKDIR /data/sgsm/1/web/trunk
	svn co http://192.168.1.161/program/program/server/

#EXPOSE 映射端口  创建容器的时候还需要指定
EXPOSE 9001

CMD systemctl start httpd

```

## 导入导出容器
导出容器--docker export 容器id > /指定目录/名字.tar
```bash
docker  export  ID  > file.tar
```
scp发送至有docker环境的服务器

导入容器（可以先docker images查看一下）--cat 名字.tar | docker import - 镜像名
```bash
cat  file.tar  | docker  import  - NAME 
```

## 镜像导入导出和更新
镜像导出：
```bash
docker save 镜像 xxx.tar
```  
镜像导入：
```bash
docker load -i xxx.tar
```
镜像的更新：
```bash
docker commit -m 提交的描述 -a 提交作者 -p 更新时暂停此容器 新镜像名
```

