---
title: 使用kubeadm安装kubernetes
tags:
  - kubernetes
  - liunx
categories: 运维
date: 2020-05-22 14:39:17
---


Kubernetes技术已经成为了原生云技术的事实标准，它是目前基础软件领域最为热门的分布式调度和管理平台。于是，Kubernetes也几乎成了时下开发工程师和运维工程师必备的技能之一。

## 一、主机环境预设
1、测试环境说明

  测试使用的Kubernetes集群可由一个master主机及一个以上（建议至少两个）node主机组成，这些主机可以是物理服务器，也可以运行于vmware、virtualbox或kvm等虚拟化平台上的虚拟机，甚至是公有云上的VPS主机。

  本测试环境将由master01、node01和node02三个独立的主机组成，它们分别拥有4核心的CPU及4G的内存资源，操作系统环境均为CentOS 7.5 1804，域名为ilinux.io。此外，需要预设的系统环境如下：

   （1）借助于NTP服务设定各节点时间精确同步；

  （2）通过DNS完成各节点的主机名称解析，测试环境主机数量较少时也可以使用hosts文件进行；

  （3）关闭各节点的iptables或firewalld服务，并确保它们被禁止随系统引导过程启动；

  （4）各节点禁用SELinux；

  （5）各节点禁用所有的Swap设备；

  （6）若要使用ipvs模型的proxy，各节点还需要载入ipvs相关的各模块；


2、设定时钟同步

  若节点可直接访问互联网，直接启动chronyd系统服务，并设定其随系统引导而启动。

    systemctl start chronyd.service

    systemctl enable chronyd.service


  不过，建议用户配置使用本地的的时间服务器，在节点数量众多时尤其如此。存在可用的本地时间服务器时，修改节点的/etc/crhony.conf配置文件，并将时间服务器指向相应的主机即可，配置格式如下：

    server CHRONY-SERVER-NAME-OR-IP iburst

  或者使用ntpdate

      yum -y install wget vim net-tools ntpdate
      ntpdate time.pool.aliyun.com

3、主机名称解析

  出于简化配置步骤的目的，本测试环境使用hosts文件进行各节点名称解析，文件内容如下所示：

    [root@master01 ~]# cat /etc/hosts
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
    192.168.1.100 master01

    192.168.1.101 node1

    192.168.1.102 node2

 

4、关闭iptables或firewalld服务

  在CentOS7上，iptables或firewalld服务通常只会安装并启动一种，在不确认具体启动状态的前提下，这里通过同时关闭并禁用二者即可简单达到设定目标。

    systemctl stop firewalld.service

    systemctl stop iptables.service

    systemctl disable firewalld.service

    systemctl disable iptables.service

 

5、关闭并禁用SELinux

  若当前启用了SELinux，则需要编辑/etc/sysconfig/selinux文件,禁用SELinux，并临时设置其当前状态为permissive：

    sed -i ‘s@^\(SELINUX=\).*@\1disabled@‘  /etc/sysconfig/selinux

    setenforce 0

6、禁用Swap设备

  部署集群时，kubeadm默认会预先检查当前主机是否禁用了Swap设备，并在未禁用时强制终止部署过程。因此，在主机内存资源充裕的条件下，需要禁用所有的Swap设备，否则，就需要在后文的kubeadm init及kubeadm join命令执行时额外使用相关的选项忽略检查错误。

  关闭Swap设备，需要分两步完成。首先是关闭当前已启用的所有Swap设备：

    swapoff -a

  而后编辑/etc/fstab配置文件，注释用于挂载Swap设备的所有行。

7、启用ipvs内核模块

  创建内核模块载入相关的脚本文件/etc/sysconfig/modules/ipvs.modules，设定自动载入的内核模块。文件内容如下：


      #!/bin/bash

      ipvs_modules_dir="/usr/lib/modules/$(uname -r)/kernel/net/netfilter/ipvs"

      for i in $(ls $ipvs_modules_dir | sed -r ‘s@(.*).ko.xz@\1@‘); do

      /sbin/modinfo -F filename $i &> /dev/null

      if [ $? -eq 0 ]; then

      /sbin/modprobe $i

      fi

      done

 

  修改文件权限，并手动为当前系统加载内核模块：

    chmod +x /etc/sysconfig/modules/ipvs.modules

    bash /etc/sysconfig/modules/ipvs.modules

8、master对node节点ssh互信

     ssh-keygen
     ssh-copy-id node01
     ssh-copy-id node02

## 二、安装程序包（在各主机上完成如下设定）

1、生成yum仓库配置

  首先获取docker-ce的配置仓库配置文件：

      yum install wget  -y
     wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker.repo

  而后手动生成kubernetes的yum仓库配置文件/etc/yum.repos.d/kubernetes.repo，内容如下：

    [kubernetes]
    name=Kubernetes
    baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
    gpgcheck=1
    gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
    enabled=1


2、安装相关的程序包

  Kubernetes会对经过充分验正的Docker程序版本进行认证，目前认证完成的最高版本是17.03，但docker-ce的最新版本已经高出了几个版本号。管理员可忽略此认证而直接使用最新版本的docker-ce程序，不过，建议根据后面的说明，将安装命令替换为安装17.03版。

    yum install docker-ce

    yum install kubelet kubeadm kubectl

  如果要安装目前经过Kubernetes认证的docker-17版本，可以将上面第一条安装命令替换为如下命令：

    yum install -y --setopt=obsoletes=0 docker-ce-17.03.2.ce docker-ce-selinux-17.03.2.ce

## 三、配置并启动docker服务（在各节点执行）

安装组件的几种方法：

1、从阿里云镜像仓库拉取镜像
    
    docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver:v1.13.0      

  修改镜像tag
    
    docker tag  registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver:v1.13.0  k8s.gcr.io/kube-apiserver:v1.13.0
    docker images

  使用docker pull镜像后  就不用修改代理配置了
 <br/> 批量修改脚本： <br/> 

      #!/bin/bash
      KUBE_VERSION=v1.13.0
      KUBE_PAUSE_VERSION=3.1
      ETCD_VERSION=3.1.12
      DNS_VERSION=1.14.8
      GCR_URL=k8s.gcr.io
      ALIYUN_URL=registry.cn-shenzhen.aliyuncs.com/cookcodeblog
      images=(kube-proxy:${KUBE_VERSION}
      kube-scheduler:${KUBE_VERSION}
      kube-controller-manager:${KUBE_VERSION}
      kube-apiserver:${KUBE_VERSION}
      pause:${KUBE_PAUSE_VERSION}
      etcd:${ETCD_VERSION}
      k8s-dns-sidecar:${DNS_VERSION}
      k8s-dns-kube-dns:${DNS_VERSION}
      k8s-dns-dnsmasq-nanny:${DNS_VERSION})


      for imageName in ${images[@]} ; do
        docker pull $ALIYUN_URL/$imageName
        docker tag  $ALIYUN_URL/$imageName $GCR_URL/$imageName
        docker rmi $ALIYUN_URL/$imageName
      done

      docker images

 
2、若要通过默认的k8s.gcr.io镜像仓库获取Kubernetes系统组件的相关镜像，需要配置docker Unit  #File（/usr/lib/systemd/system/docker.service文件）中的Environment变量，为其定义合用的HTTPS_PROXY，格式如下：

    Environment="HTTPS_PROXY=PROTOCOL://HOST:PORT"

    Environment="NO_PROXY=172.20.0.0/16,127.0.0.0/8"

  如果没有国外的服务器最好还是先使用docker pull下镜像 然后修改

3、生成配置文件
  
      kubeadm config print init-defaults ClusterConfiguration >kubeadm.conf  

  修改kubeadm.conf

      vi kubeadm.conf
    # 修改 imageRepository: k8s.gcr.io
    # 改为 registry.aliyuncs.com/google_containers
    imageRepository: registry.aliyuncs.com/google_containers
    # 修改kubernetes版本kubernetesVersion: v1.13.0
    # 改为kubernetesVersion: v1.18.2
    kubernetesVersion: v1.18.2

  查看所以下载的镜像

    kubeadm config images list --config kubeadm.conf

      W0524 14:25:08.505708   14715 configset.go:202] WARNING: kubeadm cannot validate component configs for API groups [kubelet.config.k8s.io kubeproxy.config.k8s.io]
      registry.aliyuncs.com/google_containers/kube-apiserver:v1.18.2
      registry.aliyuncs.com/google_containers/kube-controller-manager:v1.18.2
      registry.aliyuncs.com/google_containers/kube-scheduler:v1.18.2
      registry.aliyuncs.com/google_containers/kube-proxy:v1.18.2
      registry.aliyuncs.com/google_containers/pause:3.2
      registry.aliyuncs.com/google_containers/etcd:3.4.3-0
      registry.aliyuncs.com/google_containers/coredns:1.6.7

  拉取镜像

      kubeadm config images pull --config kubeadm.conf

4、初始化的时候指定镜像库 -- 个人推荐这种方式比较简单
<br/>例如：<br/>

    kubeadm init --kubernetes-version=1.18.2 --apiserver-advertise-address=192.168.1.100  --image-repository registry.aliyuncs.com/google_containers  --service-cidr=10.10.0.0/16 --pod-network-cidr=10.122.0.0/16  



  另外，docker自1.13版起会自动设置iptables的FORWARD默认策略为DROP，这可能会影响Kubernetes集群依赖的报文转发功能，因此，需要在docker服务启动后，重新将FORWARD链的默认策略设备为ACCEPT，方式是修改/usr/lib/systemd/system/docker.service文件，在“ExecStart=/usr/bin/dockerd”一行之后新增一行如下内容：

    ExecStartPost=/usr/sbin/iptables -P FORWARD ACCEPT

 
  重载完成后即可启动docker服务：

    systemctl daemon-reload

    systemctl start docker.service

  而后设定docker和kubelet随系统引导自动启动：

    systemctl enable docker kubelet

 

## 四、初始化主节点（在master01上完成如下操作）

1、初始化init

    kubeadm init --kubernetes-version=1.18.2 --apiserver-advertise-address=192.168.1.100  --image-repository registry.aliyuncs.com/google_containers  --service-cidr=10.10.0.0/16 --pod-network-cidr=10.122.0.0/16  

  master初始化日志

		[mark-control-plane] Marking the node master01 as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
		[bootstrap-token] Using token: 946w2y.xhj1wukp35zu6ppb
		[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
		[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to get nodes
		[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
		[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
		[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
		[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
		[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
		[addons] Applied essential addon: CoreDNS
		[addons] Applied essential addon: kube-proxy

		Your Kubernetes control-plane has initialized successfully!

		To start using your cluster, you need to run the following as a regular user:

		  mkdir -p $HOME/.kube
		  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
		  sudo chown $(id -u):$(id -g) $HOME/.kube/config

		You should now deploy a pod network to the cluster.
		Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
		  https://kubernetes.io/docs/concepts/cluster-administration/addons/

		Then you can join any number of worker nodes by running the following on each as root:

		kubeadm join 192.168.1.100:6443 --token 946w2y.xhj1wukp35zu6ppb \
			--discovery-token-ca-cert-hash sha256:93253b79ac5f2a3f32ee7d76e4d7d75cb2bbcd9190132a931c7ea5d5985521a1 

  上面master初始化的日志需要保留   token值创建node节点的时候需要用到

2、初始化kubectl

  kubectl是kube-apiserver的命令行客户端程序，实现了除系统部署之外的几乎全部的管理操作，是kubernetes管理员使用最多的命令之一。kubectl需经由API server认证及授权后方能执行相应的管理操作，kubeadm部署的集群为其生成了一个具有管理员权限的认证配置文件/etc/kubernetes/admin.conf，它可由kubectl通过默认的“$HOME/.kube/config”的路径进行加载。当然，用户也可在kubectl命令上使用--kubeconfig选项指定一个别的位置。

 

  下面复制认证为Kubernetes系统管理员的配置文件至目标用户（例如当前用户root）的家目录下：

     mkdir -p $HOME/.kube
     cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
     chown $(id -u):$(id -g) $HOME/.kube/config

  而后，即可通过kubectl进行客户端命令测试，并借此了解集群组件的当前状态：

    kubectl get componentstatus

一个正常的输出应该类似如下输出结果所示：

    NAME STATUS MESSAGE ERROR

    controller-manager Healthy ok

    scheduler Healthy ok

    etcd-0 Healthy {"health": "true"}


3、添加flannel网络附件  

  Kubernetes系统上Pod网络的实现依赖于第三方插件进行，这类插件有近数十种之多，较为著名的有flannel、calico、canal和kube-router等，简单易用的实现是为CoreOS提供的flannel项目。下面的命令用于在线部署flannel至Kubernetes系统之上：

    kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

  如果无法访问网站需要手动创建文件    kube-flannel.yaml
  <br/>由于内容太长保存在下面网站中：<br/>
    https://xinlong.youare.ink/2020/05/24/kube-flannel/
  <br/>然后执行<br/>

       kubectl apply -f kube-flannel.yaml

  稍等几秒后使用如下命令确认其输出结果中Pod的状态为“Running”，

    kubectl get pods -n kube-system -l app=flannel

  类似如下所示：

    NAME READY STATUS RESTARTS AGE

    kube-flannel-ds-amd64-wscnz 1/1 Running 0 14m

4、验正master节点已经就绪

    kubectl get nodes

  上述命令应该会得到类似如下输出：

    NAME STATUS ROLES AGE VERSION

    master01.ilinux.io Ready master 4m9s v1.12.1

## 五、添加节点到集群中（在node01和node02上分别完成如下操作）

1、忽略Swap相关的预检错误

  若未禁用Swap设备，编辑kubelet的配置文件/etc/sysconfig/kubelet，设置其忽略Swap启用的状态错误，内容如下：

    KUBELET_EXTRA_ARGS="--fail-swap-on=false"


  提示：若节点禁用了所有的Swap设备，并无须执行此步骤。
 

2、添加节点

 将节点加入第二步中创建的master的集群中，要使用主节点初始化过程中记录的kubeadm join命令，并且在未禁用Swap设备的情况下，额外附加“--ignore-preflight-errors=Swap”选项；下面的命令来自于前面初始master时运行的kubeadm init命令的输出结果。

   kubeadm join 192.168.1.100:6443 --token 946w2y.xhj1wukp35zu6ppb     --discovery-token-ca-cert-hash sha256:93253b79ac5f2a3f32ee7d76e4d7d75cb2bbcd9190132a931c7ea5d5985521a1 	--ignore-preflight-errors=Swap

在node服务器上执行scp  把master上的admin.conf文件拉取到/etc/kubernetes/admin.conf 			

    scp root@192.168.1.100:/etc/kubernetes/admin.conf /etc/kubernetes/admin.conf 			

设置环境变量

    export KUBECONFIG=/etc/kubernetes/admin.conf 

  在每个节点添加完成后，即可通过kubectl验正添加结果。下面的命令及其输出是在node01和node02均添加完成后运行的，其输出结果表明两个Node已经准备就绪。

    kubectl get nodes

  输出：

    NAME STATUS ROLES AGE VERSION

    master01.magedu.com Ready master 31m v1.12.1

    node01.magedu.com Ready <none> 3m8s v1.12.1

    node02.magedu.com Ready <none> 2m25s v1.12.1

  到此为止，一个master，并附带有两个node的kubernetes集群基础设施已经部署完成，用户随后即可测试其核心功能。例如，下面的命令可将myapp以Pod的形式编排运行于集群之上，并通过在集群外部进行访问：

    kubectl create deployment myapp --image=ikubernetes/myapp:v1

    kubectl create service nodeport myapp --tcp=80:80

  而后，使用如下命令了解Service对象myapp使用的NodePort，以便于在集群外部进行访问：

    kubectl get svc -l app=myapp
    
  输出：

    NAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGE

    myapp NodePort 10.102.254.75 <none> 80:31257/TCP 2m32s


myapp是一个web应用，因此，用户可以于集群外部通过“http://NodeIP:31257”这个URL访问myapp上的应用，例如于集群外通过浏览器访问“http://172.20.0.61:31257”。


