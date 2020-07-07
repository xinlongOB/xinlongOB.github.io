---
title: kubernetes-1.18.2常见报错--持续更新
tags:
  - kubernetes
  linux
categories: 运维
date: 2020-05-24 14:40:27
---
## 初始化时端口已经启动

    [root@k8s-master01 ~]# kubeadm init --config config.yaml

    [init] Using Kubernetes version: v1.10.0
    [init] Using Authorization modes: [Node RBAC]
    [preflight] Running pre-flight checks.
    [preflight] Some fatal errors occurred:
            [ERROR Port-6443]: Port 6443 is in use
            [ERROR Port-10250]: Port 10250 is in use
            [ERROR Port-10251]: Port 10251 is in use
            [ERROR Port-10252]: Port 10252 is in use
            [ERROR FileAvailable--etc-kubernetes-manifests-kube-apiserver.yaml]: /etc/kubernetes/manifests/kube-apiserver.yaml already exists
            [ERROR FileAvailable--etc-kubernetes-manifests-kube-controller-manager.yaml]: /etc/kubernetes/manifests/kube-controller-manager.yaml already exists
            [ERROR FileAvailable--etc-kubernetes-manifests-kube-scheduler.yaml]: /etc/kubernetes/manifests/kube-scheduler.yaml already exists
    [preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`

     

    解决方案：发现杀死进程都没有用，最终重启一下kubeadm就可以了，如下：

    [root@k8s-master01 ~]# kubeadm reset

## 无法拉取镜像

使用kubeadm配置文件，通过在配置文件中指定docker仓库地址，便于内网快速部署。

生成配置文件

    kubeadm config print init-defaults ClusterConfiguration >kubeadm.conf

修改kubeadm.conf

    vi kubeadm.conf
    修改 imageRepository: k8s.gcr.io
    改为 registry.aliyuncs.com/google_containers
    imageRepository: registry.aliyuncs.com/google_containers
    修改kubernetes版本kubernetesVersion: v1.13.0
    改为kubernetesVersion: v1.18.2
    kubernetesVersion: v1.18.2

  
  再次查看kubeadm config所需的镜像

    [root@master01 ~]# kubeadm config images list --config kubeadm.conf
    W0524 14:25:08.505708   14715 configset.go:202] WARNING: kubeadm cannot validate component configs for API groups [kubelet.config.k8s.io kubeproxy.config.k8s.io]
    registry.aliyuncs.com/google_containers/kube-apiserver:v1.18.2
    registry.aliyuncs.com/google_containers/kube-controller-manager:v1.18.2
    registry.aliyuncs.com/google_containers/kube-scheduler:v1.18.2
    registry.aliyuncs.com/google_containers/kube-proxy:v1.18.2
    registry.aliyuncs.com/google_containers/pause:3.2
    registry.aliyuncs.com/google_containers/etcd:3.4.3-0
    registry.aliyuncs.com/google_containers/coredns:1.6.7

拉取镜像并初始化

    kubeadm config images pull --config kubeadm.conf

    kubeadm init --kubernetes-version=1.18.2 --apiserver-advertise-address=192.168.1.100  --image-repository registry.aliyuncs.com/google_containers  --service-cidr=10.10.0.0/16 --pod-network-cidr=10.122.0.0/16  


## 初始化报错

    W0523 10:11:56.806416  112153 configset.go:202] WARNING: kubeadm cannot validate component configs for API groups [kubelet.config.k8s.io kubeproxy.config.k8s.io]
    [init] Using Kubernetes version: v1.18.3
    [preflight] Running pre-flight checks
    error execution phase preflight: [preflight] Some fatal errors occurred:
            [ERROR DirAvailable--var-lib-etcd]: /var/lib/etcd is not empty
    [preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`


删除文件

    rm -rf /var/lib/etcd

就可以继续初始化了

## 无法下载flannel网络插件

    [root@master01 ~]# wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
    --2020-05-24 14:32:30--  https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
    正在解析主机 raw.githubusercontent.com (raw.githubusercontent.com)... 0.0.0.0, ::
    正在连接 raw.githubusercontent.com (raw.githubusercontent.com)|0.0.0.0|:443... 失败：拒绝连接。
    正在连接 raw.githubusercontent.com (raw.githubusercontent.com)|::|:443... 失败：拒绝连接。

    [root@master01 ~]# kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
    The connection to the server raw.githubusercontent.com was refused - did you specify the right host or port?

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

  验正master节点已经就绪

    kubectl get nodes

  上述命令应该会得到类似如下输出：

    NAME STATUS ROLES AGE VERSION

    master01.ilinux.io Ready master 4m9s v1.12.1

## 添加node节点报错

node节点报错
    [root@node01 yum.repos.d]# kubectl get node  
    The connection to the server localhost:8080 was refused - did you specify the right host or port?	

    kubeadm join 192.168.1.100:6443 --token 946w2y.xhj1wukp35zu6ppb     --discovery-token-ca-cert-hash sha256:93253b79ac5f2a3f32ee7d76e4d7d75cb2bbcd9190132a931c7ea5d5985521a1 		

在node上执行kubeadm join后 在master服务器查询状态为 NotReady 在node上查询报错为上述日志
<br/>解决办法：<br/>

在node服务器上执行scp  把master上的admin.conf文件拉取到/etc/kubernetes/admin.conf 			

    scp root@192.168.1.100:/etc/kubernetes/admin.conf /etc/kubernetes/admin.conf 			

设置环境变量

    export KUBECONFIG=/etc/kubernetes/admin.conf 

再次执行 kubectl get node 

    [root@node01 yum.repos.d]# kubectl get node 
    NAME       STATUS   ROLES    AGE     VERSION
    master01   Ready    master   7h58m   v1.18.3
    master02   Ready    <none>   14m     v1.18.3

master执行 kubectl get node 

    [root@master01 ~]# kubectl get nodes
    NAME       STATUS   ROLES    AGE     VERSION
    master01   Ready    master   7h58m   v1.18.3
    master02   Ready    <none>   14m     v1.18.3