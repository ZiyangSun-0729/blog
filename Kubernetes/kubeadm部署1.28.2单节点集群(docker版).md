# 第一章：kubernetes介绍

​                                                  ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1715757186288-e5ae30be-603f-4a7c-9bc8-fa218a40120c.png)



kubernetes（k8s）是2014年由Google公司基于Go语言编写的一款开源的容器集群编排系统，用于自动化容器的部署、扩缩容和管理；

kubernetes（k8s）是基于Google内部的Borg系统的特征开发的一个版本，集成了Borg系统大部分优势；

官方地址：https://Kubernetes.io

代码托管平台：https://github.com/Kubernetes



**除了k8s还有哪些容器编排系统？**如：`docker swarm`、`Openshift`、`Rancher`、`Mesos`等。

## kubernetes具备的功能

- **弹性伸缩**：k8s可以根据资源的使用情况自动地调整容器的副本数。例如，在高峰时段，k8s可以自动增加容器的副本数以应对更多的流量；而在低峰时段，k8s可以减少应用的副本数，节省资源；
- **资源限额：**k8s允许指定每个容器所需的CPU和内存资源，能够更好的管理容器的资源使用量；
- **滚动升级：**k8s可以在不中断服务的情况下滚动升级应用版本，确保在整个过程中仍有足够的实例在提供服务；
- **负载均衡：**k8s可以根据应用的负载情况自动分配流量，确保各个实例之间的负载均衡，避免某些实例过载导致的性能下降；
- **服务发现：**k8s可以自动发现应用的实例，并为它们分配一个统一的访问地址。这样，用户只需要知道这个统一的地址，就可以访问到应用的任意实例，而无需关心具体的实例信息；
- **存储管理：**k8s可以自动管理应用的存储资源，为应用提供持久化的数据存储。这样，在应用实例发生变化时，用户数据仍能保持一致，确保数据的持久性；
- **密钥与配置管理：**Kubernetes 允许你存储和管理敏感信息，例如：密码、令牌、证书、ssh密钥等信息进行统一管理，并共享给多个容器复用；

## kubernetes集群角色

k8s集群需要建⽴在多个节点上，将多个节点组建成一个集群，然后进⾏统⼀管理，但是在k8s集群内部，这些节点⼜被划分成了两类⻆⾊：

- **Master管理节点：**负责集群的所有管理工作，和协调集群中运行的容器应用； 
- **Node工作节点：**负责运行集群中所有用户的容器应用， 执行实际的工作负载 ； 

**Master管理节点组件：**

- **API Server：**作为集群的控制中心，处理外部和内部通信，接收用户请求并处理集群内部组件之间的通信；
- **Scheduler：**负责将待部署的 Pods 分配到合适的 Node 节点上，根据资源需求、策略和约束等因素进行调度；
- **Controller Manager：**管理集群中的各种控制器，例如： Deployment、ReplicaSet、StatefulSet控制器等，来管理集群中的各种资源；
- **etcd：**作为集群的数据存储，保存集群的配置信息和状态信息；

**Node工作节点组件：**

- **Kubelet：**负责与 Master 节点通信，并根据 Master 节点的调度决策来创建、更新和删除 Pod，同时维护 Node 节点上的容器状态；
- **容器运行时**（如 Docker、containerd 等）：负责运行和管理容器，提供容器生命周期管理功能。例如：创建、更新、删除容器等；
- **Kube-proxy：**负责为集群内的服务实现网络代理和负载均衡，确保服务的访问性；

**非必须的集群组件：**

- **DNS服务：**严格意义上的必须插件，在k8s中，很多功能都需要用到DNS服务，例如：服务发现、有状态应用的访问等；
- **Dashboard：** 是k8s集群WEB管理界面，如：Rancher、Kuboard等
- **资源监控**：例如metrics-server监视器，用于监控集群中资源利用率；

## kubernetes集群类型

- **一主多从集群：**由一台Master管理节点和多台Node工作节点组成，生产环境下Master节点存在单点故障的风险，适合学习和测试环境使用；
- **多主多从集群：**由多台Master管理节点和多Node工作节点组成，安全性高，适合生产环境使用；

# 第二章：kubernetes集群部署

## kubernetes集群规划

提示：系统尽量别带图形界面，图形比较吃内存。

| **主机名** | **IP地址**    | **角色** | **操作系统** | **硬件配置** |
| ---------- | ------------- | -------- | ------------ | ------------ |
| master01   | 192.168.0.111 | 管理节点 | Rocky 9.0    | 2C/4G/50G    |
| node01     | 192.168.0.112 | 工作节点 | Rocky 9.0    | 2C/4G/50G    |
| node02     | 192.168.0.113 | 工作节点 | Rocky 9.0    | 2C/4G/50G    |

## 集群前期环境准备

按照集群规划修改每个节点主机名

```shell
hostnamectl set-hostname xxx
```



**提示：以下前期环境准备需要在所有节点都执行**

配置集群之间本地解析，集群在初始化时需要能够解析主机名

```shell
192.168.0.111 master01 
192.168.0.112 node01
192.168.0.113 node02
```

## 开启bridge网桥过滤

`etc/sysctl.d/`目录用于存放配置内核参数的文件，文件以`.conf`结尾，这些文件会在系统启动时自动加载。

```shell
cat > /etc/sysctl.d/k8s.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
```

<details class="lake-collapse"><summary id="u2eee2b91"><strong><span class="ne-text" style="font-size: 16px">参数解释：</span></strong></summary><p id="u3ac54d94" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="font-size: 16px">net.bridge.bridge-nf-call-ip6tables = 1 </span><span class="ne-text" style="background-color: #FBE4E7; font-size: 16px"> //对网桥上的IPv6数据包通过iptables处理</span></p><p id="uef5ab82b" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="font-size: 16px">net.bridge.bridge-nf-call-iptables = 1    </span><span class="ne-text" style="background-color: #FBE4E7; font-size: 16px">//对网桥上的IPv4数据包通过iptables处理</span></p><p id="uce132d78" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="font-size: 16px">net.ipv4.ip_forward = 1                          </span><span class="ne-text" style="background-color: #FBE4E7; font-size: 16px">//开启IPv4路由转发，来实现集群中的容器跨网络通信</span></p></details>

由于开启bridge功能，需要加载br_netfilter模块使参数生效

```shell
modprobe br_netfilter && lsmod | grep br_netfilter
```

加载配置文件，使上述配置生效

```shell
sysctl -p /etc/sysctl.d/k8s.conf
```

## 安装IPvs代理模块

在k8s中Service有两种代理模式，一种是基于iptables的，一种是基于ipvs，两者对比ipvs模式性能更高效，如果想要使用ipvs模式，需要手动载入ipvs模块。

`ipset`和`ipvsadm`是网络管理和负载均衡相关的软件包，提供`ip_vs`模块

```shell
dnf -y install ipset ipvsadm
```

将需要加载的ipvs相关模块写入到文件中

`/etc/modules-load.d/`目录主要用于在系统启动时加载用户自定义的内核模块，这个目录中的文件以 `.conf` 结尾，文件中指定需要加载的内核模块。

```shell
cat > /etc/modules-load.d/ip_vs.conf <<EOF
ip_vs
ip_vs_rr
ip_vs_wrr
ip_vs_sh
nf_conntrack
EOF
```

<details class="lake-collapse"><summary id="udd560b81"><strong><span class="ne-text">模块介绍：</span></strong></summary><p id="u732ce1a1" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">ip_vs         	</span><span class="ne-text" style="background-color: #FBE4E7"> //提供负载均衡的模块,支持多种负载均衡算法,如轮询、最小连接、加权最小连接等</span></p><p id="u0a833c6d" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">ip_vs_rr      	 </span><span class="ne-text" style="background-color: #FBE4E7">//轮询算法的模块（默认算法）</span></p><p id="u3a18e515" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">ip_vs_wrr     	</span><span class="ne-text" style="background-color: #FBE4E7"> //加权轮询算法的模块,根据后端服务器的权重值转发请求</span></p><p id="u082f596e" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">ip_vs_sh     	</span><span class="ne-text" style="background-color: #FBE4E7"> //哈希算法的模块,同一客户端的请求始终被分发到相同的后端服务器,保证会话一致性</span></p><p id="u69a2d241" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">nf_conntrack  	</span><span class="ne-text" style="color: #DF2A3F"> </span><span class="ne-text" style="background-color: #FBE4E7">//链接跟踪的模块,用于跟踪一个连接的状态,例如 TCP 握手、数据传输和连接关闭等</span></p></details>

 加载模块生效（这个文件会在下次系统启动时自动生效）

```shell
systemctl restart systemd-modules-load.service
```

过滤模块，验证是否成功加载  

```shell
lsmod | grep ip_vs
===========================================================
ip_vs_sh               16384  0
ip_vs_wrr              16384  0
ip_vs_rr               16384  0
ip_vs                 188416  6 ip_vs_rr,ip_vs_sh,ip_vs_wrr
nf_conntrack          176128  1 ip_vs
nf_defrag_ipv6         24576  2 nf_conntrack,ip_vs
libcrc32c              16384  3 nf_conntrack,xfs,ip_vs
```

## 关闭SWAP交换分区

为了保证 kubelet 正常工作，k8s强制要求禁用，否则集群初始化失败

```shell
swapoff -a && \
sed -ri 's/.*swap.*/#&/' /etc/fstab
```

## 准备docker环境

添加阿里云 docker-ce 仓库

```shell
yum install -y yum-utils && \
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

安装 docker 软件包

```shell
dnf install docker-ce-20.10.24-3.el9.x86_64 -y
```

启用Docker Cgroup控制组，用于限制容器资源使用量，如CPU、内存

```shell
mkdir /etc/docker
cat > /etc/docker/daemon.json <<EOF
{
        "exec-opts": ["native.cgroupdriver=systemd"],
        "registry-mirrors": ["https://docker.1ms.run"]
}
EOF
```

启动Docker服务并设置随机自启

```shell
systemctl enable docker --now
```

## 安装cri-dockerd

下载cri-dockerd

```shell
wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.13/cri-dockerd-0.3.13.amd64.tgz
```

解压压缩包

```shell
tar -xf cri-dockerd-0.3.13.amd64.tgz
```

拷贝程序文件到`/usr/bin/`目录

```shell
cp cri-dockerd/cri-dockerd /usr/bin
```

准备`cri-docker.service`服务管理文件，文件中`pause`镜像下载地址为阿里云

```shell
[Unit]
Description=CRI Interface for Docker Application Container Engine
Documentation=https://docs.mirantis.com
After=network-online.target firewalld.service docker.service
Wants=network-online.target
Requires=cri-docker.socket

[Service]
Type=notify

ExecStart=/usr/bin/cri-dockerd --network-plugin=cni --pod-infra-container-image=registry.aliyuncs.com/google_containers/pause:3.9

ExecReload=/bin/kill -s HUP $MAINPID
TimeoutSec=0
RestartSec=2
Restart=always

StartLimitBurst=3

StartLimitInterval=60s

LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity

TasksMax=infinity
Delegate=yes
KillMode=process

[Install]
WantedBy=multi-user.target
```

创建cri-dockerd的socker文件用于kubelet与cri-dockerd通信

```shell
[Unit]
Description=CRI Docker Socket for the API
PartOf=cri-docker.service

[Socket]
ListenStream=%t/cri-dockerd.sock
SocketMode=0660
SocketUser=root
SocketGroup=docker

[Install]
WantedBy=sockets.target
```

启动cri-docker

```shell
systemctl daemon-reload && \
systemctl enable  cri-docker --now 
```

## 配置kubernetes仓库

本实验使用阿里云YUM源

```shell
cat > /etc/yum.repos.d/k8s.repo <<EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```

## 安装集群相关软件

安装以下软件包：

- kubeadm：用于初始化集群，并配置集群所需的组件并生成对应的安全证书和令牌；
- kubelet：负责与 Master 节点通信，并根据 Master 节点的调度决策来创建、更新和删除 Pod，同时维护 Node 节点上的容器状态；
- kubectl：用于管理k8集群的一个命令行工具；

```shell
dnf -y install kubeadm-1.28.2 kubelet-1.28.2 kubectl-1.28.2
```

kubelet启用Cgroup控制组，用于限制进程的资源使用量，如CPU、内存

```shell
tee > /etc/sysconfig/kubelet <<EOF
KUBELET_EXTRA_ARGS="--cgroup-driver=systemd"
EOF
```

设置kubelet开机自启动即可，集群初始化后自动启动

```shell
systemctl enable kubelet
```

## 查看集群所需镜像

在master01节点查看集群所需镜像文件

```shell
kubeadm config images list
```

## k8s集群初始化

在master01节点生成初始化集群的配置文件

```shell
kubeadm config print init-defaults > kube-init.yml
```

配置文件需要修改如下内容

```shell
#管理节点的IP地址
advertiseAddress: 192.168.0.111

#容器运行时cri-dockerd.sock文件位置
criSocket: unix:///var/run/cri-dockerd.sock

#当前主机注册到集群后的集群节点名称
name: master01

#集群镜像下载地址，修改为阿里云
imageRepository: registry.cn-hangzhou.aliyuncs.com/google_containers
```

通过配置文件初始化集群

```shell
kubeadm init --config kube-init.yml 
```

<details class="lake-collapse"><summary id="ued9fd1a5"><span class="ne-text">选项说明：</span></summary><p id="ufe9b678b" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">init 				</span><span class="ne-text" style="background-color: #FBE4E7">//初始化集群</span></p><p id="u7bfa030c" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">--config			</span><span class="ne-text" style="background-color: #FBE4E7">//通过配置文件初始化</span></p></details>

根据集群初始化后的提示，执行如下命令

```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```



**提示：**如果哪个节点出现问题，可以使用下列命令重置当前节点

```shell
kubeadm  reset
```



**提示**：如果需要在Node节点也可以管理集群，将Master节点`/root/.kube`目录拷贝到Node节点上

```shell
scp -r /root/.kube 节点IP:/root
```



根据提示将node节点加入集群，要在`kubeadm join`最后增加`--cri-socket=/var/run/cri-dockerd.sock`，指定容器运行时cri-dockerd.sock文件位置

```shell
kubeadm join 192.168.0.111:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:50df8066df9da74e09618bb5d9560b208450a9a89881aef0000a00cd83abcd37 --cri-socket=/var/run/cri-dockerd.sock
```

加入成功后在master节点验证

```shell
kubectl get nodes
===========================================================
NAME     STATUS     ROLES                  AGE     VERSION
master01 NotReady   control-plane,master   3m31s   v1.28.2
node01   NotReady   <none>                 12s     v1.28.2
node02   NotReady   <none>                 89s     v1.28.2
```



**提示：**如果哪个节点出现问题，可以使用下列命令重置当前节点

```shell
kubeadm  reset
```



## 部署Pod网络Calico

Calico 和 Flannel 是两种流行的 k8s 网络插件，它们都为集群中的 Pod 提供网络功能。然而，它们在实现方式和功能上有一些重要区别：

**网络模型的区别：**

**Calico** 使用 BGP（边界网关协议）作为其底层网络模型。它利用 BGP 为每个 Pod 分配一个唯一的 IP 地址，并在集群内部进行路由。Calico 支持网络策略，可以对流量进行精细控制，允许或拒绝特定的通信。

**Flannel** 则采用了一个简化的覆盖网络模型。它为每个节点分配一个 IP 地址子网，然后在这些子网之间建立覆盖网络。Flannel 将 Pod 的数据包封装到一个更大的网络数据包中，并在节点之间进行转发。Flannel 更注重简单和易用性，不提供与 Calico 类似的网络策略功能。

**性能的区别：**

**Calico** 使用 BGP 进行路由，其性能通常优于 Flannel。Calico 可以实现直接的 Pod 到 Pod 通信，而无需在节点之间进行额外的封装和解封装操作。这使得 Calico 在大型或高度动态的集群中具有更好的性能。

**Flannel** 的覆盖网络模型会导致额外的封装和解封装开销，从而影响网络性能。对于较小的集群或对性能要求不高的场景，这可能并不是一个严重的问题。



master01节点下载Calico文件（版本 3.24）

```shell
wget https://raw.githubusercontent.com/projectcalico/calico/v3.24.1/manifests/calico.yaml
```

文件备用地址（版本 3.25）

```shell
wget https://docs.projectcalico.org/manifests/calico.yaml
```

创建calico网络

```shell
kubectl apply -f calico.yaml 
```

查看Calico Pod状态是否为Running

```shell
kubectl get pod -n kube-system
===========================================================
NAME                                       READY   STATUS    RESTARTS   AGE
calico-kube-controllers-66966888c4-whdkj   1/1     Running   0          101s
calico-node-f4ghp                          1/1     Running   0          101s
calico-node-sj88q                          1/1     Running   0          101s
calico-node-vnj7f                          1/1     Running   0          101s
calico-node-vwnw4                          1/1     Running   0          101s
```

## 验证集群节点状态

在master01节点查看集群信息

```shell
kubectl get nodes
==========================================================
NAME       STATUS   ROLES                  AGE   VERSION
master01   Ready    control-plane,master   25m   v1.28.2
node01     Ready    <none>   							 25m   v1.28.2
node02     Ready    <none>   							 24m   v1.28.2
```

## 部署Nginx测试集群

在master01节点部署nginx程序测试

```shell
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.20.2
    ports:
    - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30002
```

查看Pod状态是否为Running

```shell
kubectl get pod
===========================================================
NAME                     READY   STATUS    RESTARTS   AGE
nginx-696649f6f9-j8zbj   1/1     Running   0          2m1s
```

查看Service代理信息

```shell
kubectl get service
===========================================================
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        44m
nginx-svc    NodePort    10.103.195.31   <none>        80:30002/TCP   96s
```



浏览器访问测试：[http://IP:3](http://192.168.0.30:32554/)0002