# 第一章：K8S介绍及集群部署

kubernetes（k8s）是2014年由Google公司基于Go语言编写的一款开源的容器集群编排系统，用于自动化容器的部署、扩缩容和管理；

kubernetes（k8s）是基于Google内部的Borg系统的特征开发的一个版本，集成了Borg系统大部分优势；

官方地址：https://Kubernetes.io

代码托管平台：https://github.com/Kubernetes



除了k8s还有哪些容器编排系统？如：docker swarm、Openshift、Rancher、Mesos等。

## k8s具备的功能

- 自我修复：k8s监控容器的运行状况，并在容器出现异常时自动对其重启；
- 弹性伸缩：k8s可根据资源使用情况自动地调整容器的副本数。例如，在高峰时段，可自动增加容器的副本数以应对更多的流量；在低峰时段，减少容器的副本数节省资源；
- 资源限额：通过对容器所需的CPU和内存资源设置上限，能够更好的管理容器的资源使用量；
- 滚动升级：k8s可在不中断服务的情况下滚动升级应用版本，确保在整个过程中服务中断；
- 负载均衡：k8s可根据应用的负载情况自动分配流量，确保各实例之间的负载均衡，避免某些实例过载导致性能下降；
- 服务发现：通过为实例分配一个统一的访问地址，这样，用户只需要知道这个统一的地址，就可以访问到应用的任意实例，而无需关心具体的实例信息；
- 存储管理：k8s可以自动管理应用的存储资源，为应用提供持久化的数据存储。这样，在应用实例发生变化时，用户数据仍能保持一致，确保数据的持久性；
- 密钥与配置管理：Kubernetes 允许你存储和管理敏感信息，例如：密码、令牌、证书、ssh密钥等信息进行统一管理，并共享给多个容器复用；

## k8s集群角色

k8s将多个节点组建成一个集群进⾏统⼀管理，但是在集群内部，这些节点⼜被划分成了两类⻆⾊：

- `Master管理节点`：负责集群的所有管理工作； 
- `Node工作节点`：负责运行集群中的容器应用；

## Master管理节点组件：

- `API Server`：作为集群的管理入口，处理外部和内部通信，接收用户请求并处理集群内部组件之间的通信；
- `Scheduler`：作为集群资源调度计算，根据调度策略，负责将待部署的 Pods 分配到合适的 Node 节点上；
- `Controller Manager`：管理集群中的各种控制器，例如 Deployment、ReplicaSet、DaemonSet等，管理集群中的各种资源；
- `etcd`：作为集群的数据存储，保存集群的配置信息和状态信息；

## Node工作节点组件：

- `Kubelet`：负责与 Master 节点通信，并根据 Master 节点的调度决策来创建、更新和删除 Pod，同时维护 Node 节点上的容器状态；
- `容器运行时`（如 Docker、containerd 等）：负责运行和管理容器，提供容器生命周期管理功能。例如：创建、更新、删除容器等；
- `Kube-proxy`：负责为集群内的服务实现网络代理和负载均衡，确保服务的访问性；

## 非必须的集群插件：

- `DNS服务`：严格意义上的必须插件，在k8s中，很多功能都需要用到DNS服务，例如：服务发现、负载均衡、有状态应用的访问等；
- `Dashboard`： 是k8s集群的Web管理界面；
- `资源监控`：例如metrics-server监视器，用于监控集群中资源利用率；

## k8s集群类型

- 一主多从集群：由一台Master管理节点和多台Node工作节点组成，生产环境下Master节点存在单点故障的风险，适合学习和测试环境使用；
- 多主多从集群：由多台Master管理节点和多Node工作节点组成，安全性高，适合生产环境使用；

## k8s集群规划

| **主机名** | **IP地址**    | **操作系统** | **硬件最低配置** |
| ---------- | ------------- | ------------ | ---------------- |
| master01   | 192.168.0.130 | Ubuntu18.04  | 2Core/4G内存/50G |
| node01     | 192.168.0.131 | Ubuntu18.04  | 1Core/2G内存/50G |
| node02     | 192.168.0.132 | Ubuntu18.04  | 1Core/2G内存/50G |

## 集群环境部署

按照集群规划修改每个节点主机名

```shell
hostnamectl set-hostname 主机名
```



**提示：以下前期环境准备需要在所有节点都执行** 

配置集群之间本地解析，集群在初始化时需要能够解析到每个节点的主机名

```shell
#...
192.168.0.130 master01
192.168.0.131 node01
192.168.0.132 node02
```



为了保证 `kubelet` 正常工作要求禁用SWAP，否则集群初始化失败

```shell
swapoff -a && \
sed -ri 's/.*swap.*/#&/' /etc/fstab
```

检查swap

```shell
free -h
#...
Swap:            0B          0B          0B
```

## 开启bridge网桥过滤

bridge(桥接) 是 Linux 系统中的一种虚拟网络设备，它充当一个虚拟的交换机，为集群内的容器提供网络通信功能，容器就可以通过这个 bridge 与其他容器或外部网络通信了。

```shell
cat > /etc/sysctl.d/k8s.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
```

<details class="lake-collapse"><summary id="u624dee65"><strong><span class="ne-text">参数解释：</span></strong></summary><p id="ubedf7fc8" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">net.bridge.bridge-nf-call-ip6tables = 1  //对网桥上的IPv6数据包通过iptables处理</span></p><p id="u6be1b54b" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">net.bridge.bridge-nf-call-iptables = 1   //对网桥上的IPv4数据包通过iptables处理</span></p><p id="u8563908a" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">net.ipv4.ip_forward = 1                         //开启IPv4路由转发，来实现集群中的容器与外部网络的通信</span></p></details>

由于开启bridge功能，需要加载br_netfilter模块来允许在bridge设备上的数据包经过iptables防火墙处理

```shell
modprobe br_netfilter && lsmod | grep br_netfilter
```

<details class="lake-collapse"><summary id="ub7783183"><strong><span class="ne-text">命令解释：</span></strong></summary><p id="ue6d332fb" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">modprobe       	 //命令可以加载内核模块</span></p><p id="ua66f7fff" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">br_netfilter    		//模块模块允许在bridge设备上的数据包经过iptables防火墙处理</span></p></details>

加载配置文件，使上述配置生效

```shell
sysctl -p /etc/sysctl.d/k8s.conf
```

## 准备 Containerd 环境

## 准备Docker环境

安装Docker依赖包

```shell
apt install -y apt-transport-https ca-certificates curl software-properties-common
```

 添加软件包GPG密钥文件

```shell
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

添加阿里云 `docker-ce` 仓库

```shell
add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

更新软件仓库

```shell
apt update
```

查看可以安装的版本

```shell
apt-cache madison docker-ce
```

安装 `docker` 软件包

```shell
apt install docker-ce -y
```

查看以安装的软件包信息

```shell
dpkg -l | grep docker-ce
```

启用 `Cgroup` 控制组，用于限制进程的资源使用量，如CPU、内存资源

```shell
cat > /etc/docker/daemon.json <<EOF
{
        "exec-opts": ["native.cgroupdriver=systemd"],
        "registry-mirrors": ["https://docker.rainbond.cc"]
}
EOF
```

启动 `docker` 

```shell
systemctl daemon-reload
systemctl restart docker
systemctl enable docker
```

## 集群部署方式

k8s集群有多种部署方式，目前常用的部署方式有如下两种：

- `kubeadm` 部署方式：kubeadm是一个快速搭建kubernetes的集群工具；
- 二进制包部署方式：从官网下载每个组件的二进制包，依次去安装，部署麻烦；
- 其他方式：通过一些开源的工具搭建，例如：sealos；



通过 `Kubeadm` 方式部署k8s集群，需要配置k8s软件仓库来安装集群所需软件，本实验使用阿里云YUM源



安装依赖包，使得 apt 支持 ssl 传输 

```shell
apt install apt-transport-https
```

 添加软件包GPG密钥文件

```shell
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
```

添加阿里云 `kubernetes` 仓库

```shell
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF
```

更新软件仓库

```shell
apt update
```



安装集群软件，本实验安装k8s 1.23.0版本软件

- `kubeadm`：用于初始化集群，并配置集群所需的组件并生成对应的安全证书和令牌；
- `kubelet`：负责与 Master 节点通信，并根据 Master 节点的调度决策来创建、更新和删除 Pod，同时维护 Node 节点上的容器状态；
- `kubectl`：用于管理k8集群的一个命令行工具；



查看可安装的软件包信息

```shell
apt-cache madison kubeadm
```

安装1.23.0版本软件包

```shell
apt install -y kubeadm=1.23.0-00  kubelet=1.23.0-00 kubectl=1.23.0-00
```

设置 `kubelet` 开机自启动即可，集群初始化后自动启动

```shell
systemctl enable kubelet 
```

## 集群初始化

**在master01节点初始化集群**：查看集群所需镜像文件

```shell
root@master01:~# kubeadm config images list
#...以下是集群初始化所需的集群组件镜像
v1.27.1; falling back to: stable-1.23
k8s.gcr.io/kube-apiserver:v1.23.17
k8s.gcr.io/kube-controller-manager:v1.23.17
k8s.gcr.io/kube-scheduler:v1.23.17
k8s.gcr.io/kube-proxy:v1.23.17
k8s.gcr.io/pause:3.6
k8s.gcr.io/etcd:3.5.1-0
k8s.gcr.io/coredns/coredns:v1.8.6
```

生成集群初始化配置文件

```shell
root@master01:~# kubeadm config print init-defaults > kubeadm-config.yml
```

配置文件需要修改如下内容

```shell
root@master01:~# cat kubeadm-config.yml
#本机IP地址
advertiseAddress: 192.168.0.130

#本机名称
name: master01

#集群镜像下载地址，修改为阿里云
imageRepository: registry.cn-hangzhou.aliyuncs.com/google_containers
```

集群初始化

```shell
root@master01:~# kubeadm init --config kubeadm-config.yml
```



**如果出现以下报错：**

[kubelet-check] The HTTP call equal to 'curl -sSL http://localhost:10248/healthz' failed with error: Get "http://localhost:10248/healthz": dial tcp 127.0.0.1:10248: connect: connection refused.



重置节点

```shell
root@master01:~# kubeadm reset
```

重启docker程序

```shell
root@master01:~# systemctl daemon-reload
root@master01:~# systemctl restart docker
```

在重新初始化集群

```shell
root@master01:~# kubeadm init --config kubeadm-config.yml --upload-certs
```

根据集群初始化后的提示，执行以下命令生成集群管理员配置文件

```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```



**根据提示将** `node` **节点加入集群，需要注意的是复制kubeadm join 命令时会在 .--discovery 前边多个点，要把这个点去掉**

```shell
kubeadm join 192.168.0.131:6443 --token abcdef.0123456789abcdef \
> .--discovery-token-ca-cert-hash sha256:79adaa16cc7724e5e0afcd1ab82fa15cda282f9604607ec63a71c813992e58dd
```

加入进群

```shell
kubeadm join 192.168.0.131:6443 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:79adaa16cc7724e5e0afcd1ab82fa15cda282f9604607ec63a71c813992e58dd
```

加入集群后验证节点信息

```shell
root@master01:~# kubectl get nodes
NAME       STATUS     ROLES                  
master01   NotReady   control-plane,master   
node01     NotReady   <none>               
node02     NotReady   <none>               
```

提示：如果哪个节点出现问题，可以使用下列命令重置当前节点

```shell
kubeadm  reset
```

## 部署Calico网络

`Calico` 和 `Flannel` 是两种流行的 k8s 网络插件，它们都为集群中的 Pod 提供网络功能。然而，它们在实现方式和功能上有一些重要区别： 



**网络模型的区别：**

- Calico 使用 BGP（边界网关协议）作为其底层网络模型。它利用 BGP 为每个 Pod 分配一个唯一的 IP 地址，并在集群内部进行路由。Calico 支持网络策略，可以对流量进行精细控制，允许或拒绝特定的通信。
- Flannel 则采用了一个简化的覆盖网络模型。它为每个节点分配一个 IP 地址子网，然后在这些子网之间建立覆盖网络。Flannel 将 Pod 的数据包封装到一个更大的网络数据包中，并在节点之间进行转发。Flannel 更注重简单和易用性，不提供与 Calico 类似的网络策略功能。

**性能的区别：**

- 由于 Calico 使用 BGP 进行路由，其性能通常优于 Flannel。Calico 可以实现直接的 Pod 到 Pod 通信，而无需在节点之间进行额外的封装和解封装操作。这使得 Calico 在大型或高度动态的集群中具有更好的性能。
- Flannel 的覆盖网络模型会导致额外的封装和解封装开销，从而影响网络性能。对于较小的集群或对性能要求不高的场景，这可能并不是一个严重的问题。



在 `master01` 节点安装下载 `Calico` 的yaml文件

```shell
root@master01:~# wget https://raw.githubusercontent.com/projectcalico/calico/v3.24.1/manifests/calico.yaml 
```

创建calico网络

```shell
root@master01:~# kubectl apply -f calico.yaml 
```

查看calico的Pod状态

```shell
root@master01:~# kubectl get pod -n kube-system
NAME                                      READY   
#...
calico-kube-controllers-66966888c4-whdkj   1/1    
calico-node-f4ghp                          1/1     
calico-node-sj88q                          1/1     
calico-node-vnj7f                          1/1     
calico-node-vwnw4                          1/1  
```

## 检查集群状态

```shell
root@master01:~# kubectl get nodes
NAME     STATUS   ROLES                  
master   Ready    control-plane,master   
node01   Ready    <none>                 
node02   Ready    <none>   
```