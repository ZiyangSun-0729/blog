# calicoctl

## 概述

`calicoctl`是 Calico 客户端的管理工具。可以更加方便的**管理 calico 网络，配置和安全策略，**calicoctl 命令行提供了许多的**资源管理命令，允许创建，修改，删除和查看不同的 Calico 资源，**网络资源包含了：node，bgpPeer，hostEndpoint，workloadEndpoint，ipPool，policy，profile等。

## 资源

### Node

**管理节点的网络信息和配置**

管理节点的网络信息和配置是 Calico 的核心功能之一，主要是通过**Node 资源**来实现。

**可以把他理解为 Kubernetes 集群中的每一个节点，**在 Calico 中，每个节点（Node）都代表着一个运行 Calico 的 Kubernetes 的工作节点、物理、虚机。

### BGPPeer（BGP对等体）自治系统

**配置 BGP 对等关系**

用于配置 Calico 节点与其他 BGP 对等体（节点/路由器）之间的对等关系。

* 建立节点之间 / 与外部网络设备之间的 BGP 会话。
* 配置外部网络边界、跨自治系统（ASN）的路由等。

### HostEndpoint

**管理主机接口流量**

表示一个主机接口（物理、虚拟网络接口），用于定义和管理主机层面的网络策略。

* 控制进入、离开主机网络接口的流量。
* 应用防火墙规则、安全策略。

### WorkloadEndpoint

**管理工作负载接口流量**

表示一个工作负载（如 Pod 、 虚拟机）的网络接口。

* 管理和控制工作负载之间的网络流量。
* 应用策略来限制、允许特定工作负载的通信。

### IPPool

**定义 IP 地址池**

定义 Calico 用于分配 Pod 和其他资源的 IP 地址范围。

* 配置和管理 Pod 、工作负载的 IP 地址分配。
* 支持 CIDR 范围配置、NAT规则等。

### Policy

**配置网络访问控制策略**

定义网络安全规则，用于允许、拒绝特定的流量。

* 控制网络流量的入站和出站行为。
* 配置细粒度的访问控制规则。

### Profile

**分组和复用网络策略**

表示一组网络策略的集合，通常会与特定的网络段、负载均衡关联。

* 为工作负载、 IP 地址分配默认策略。
* 提供一种方式来对网络策略进行分组和复用。

## calicoctl 使用

`calicoctl` 通过读写 calico 的数据存储系统（datastore）来进行查看或者其他各类的管理操作，通常，他需要提供认证信息经过响应的存储系统完成认证。

在使用 Kubernetes API 存储数据是，需要使用类似 kubectl 的认证信息完成认证。**他可以通过环境变量声明的 `DATASTORE_TYPE`  和 `KUBECONFIG` 来介入集群，如：**

### 认证信息配置

~~~~
export KUBECONFIG=/path/to/your/kubeconfig
export DATASTORE_TYPE=kubernetes
~~~~

查看帮助

~~~~
calicoctl -h
~~~~

查看calico节点

~~~~
calicoctl get node -o wide
~~~~

### 查看 IP 资源池

~~~~
calicoctl get ippool -o wide
calicoctl get ippool -o yaml
~~~~

![](E:\kubernetes\Calico\calicoctl\images\微信截图_20250111165323.png)

### 配置 IP 池

IP 池是 Calico 分配 Pod 网络中的一个范围地址

在集群中定义新的 IP 池

~~~~
cat > pool-1.yaml <<EOF
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: pool-1
spec:
  cidr: 172.17.1.0/24
  ipipMode: Never
  natOutgoing: true
  disabled: false
  nodeSelector: all()
EOF

cat > pool-2.yaml <<EOF
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: pool-2
spec:
  cidr: 172.17.2.0/24
  ipipMode: Never
  natOutgoing: true
  disabled: false
  nodeSelector: all()
EOF
~~~~

创建并查看新定义的 ippool

~~~~
calicoctl apply -f pool-1.yaml
calicoctl apply -f pool-2.yaml
calicoctl get ippool -o wide
~~~~

![](E:\kubernetes\Calico\calicoctl\images\微信截图_20250111170703.png)

#### kubectl 创建 ippool

如果要使用 kubectl 来创建 ippool ，那么需要先进行查询他的 apiVersion 和 kind

先删除上面新创建的两个 ippool

~~~~
calicoctl delete -f pool-1.yaml
calicoctl delete -f pool-2.yaml
calicoctl get ippool
~~~~

在未修改前执行 kubectl ，发现提示 kind 报错

~~~~
kubectl apply -f pool-1.yaml
~~~~

![](E:\kubernetes\Calico\calicoctl\images\微信截图_20250111204051.png)

查看 apiVersion 和 kind

~~~~
kubectl api-versions|grep calico
kubectl api-resources -o wide|grep calico|grep -i ippool
~~~~

![](E:\kubernetes\Calico\calicoctl\images\微信截图_20250111204605.png)

这里需要做的是把 apiVersion 换掉

~~~~
cat > pool-3.yaml <<EOF
apiVersion: crd.projectcalico.org/v1
kind: IPPool
metadata:
  name: pool-3
spec:
  cidr: 172.17.3.0/24
  ipipMode: Never
  natOutgoing: true
  disabled: false
  nodeSelector: all()
EOF

cat > pool-4.yaml <<EOF
apiVersion: crd.projectcalico.org/v1
kind: IPPool
metadata:
  name: pool-4
spec:
  cidr: 172.17.4.0/24
  ipipMode: Never
  natOutgoing: true
  disabled: false
  nodeSelector: all()
EOF
~~~~

查看 ippool 并创建

~~~~
calico get ippools
kubectl apply -f pool-3.yaml
kubectl apply -f pool-4.yaml
~~~~

![](E:\kubernetes\Calico\calicoctl\images\微信截图_20250111205052.png)

### 使用指定 ippool 创建 Pod

利用 `cni.projectcalico.org/ipv4pools` 进行注解，从而指定 ippool ，进行分配

~~~~bash
cat > ./nginx.yaml << EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      annotations:		# 此处需要格外注意引号
        "cni.projectcalico.org/ipv4pools": '["pool-3","pool-4"]'
    spec:
      containers:
      - name: nginx
        image: nginx
        resources:
          limits:
            memory: "128Mi"
            cpu: "500m"
        ports:
        - containerPort: 80
EOF
~~~~

查看 Pod IP

![](E:\kubernetes\Calico\calicoctl\images\微信截图_20250112154822.png)

### 固定 IP 创建 Pod

利用 `cni.projectcalico.org/ipAddrs` ，从而固定 Pod IP

~~~~bash
vim nginx.yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      annotations:		# 注意引号
        cni.projectcalico.org/ipAddrs: '["172.16.0.2“]'
    spec:
      containers:
      - name: nginx
        image: nginx
        resources:
          limits:
            memory: "128Mi"
            cpu: "500m"
        ports:
        - containerPort: 80
~~~~

![](E:\kubernetes\Calico\calicoctl\images\微信截图_20250112163221.png)

### 网络策略（NetworkPolicy）

https://docs.tigera.io/calico/latest/reference/resources/networkpolicy

`网络策略资源（NerworkPolicy）` **表示应用的一组有序规则到与标签选择器。**NetworkPolicy 是命名空间资源。NerworkPolicy 在特定的 namespace 中仅适用于工作负载终端节点资源，在该 namespace 中。两个资源位于同一个 namespace 中，如果 namespace 两个的 value 设置相同。

**示例：**此实例策略允许来自 TCP frontend 6379  端口的流量访问 database

~~~~bash
apiVersion: networking.k8s.io/v1  # 定义使用 Kubernetes 的网络策略 API 版本
kind: NetworkPolicy               # 声明资源类型为 NetworkPolicy
metadata:
  name: allow-tcp-6379            # 定义 NetworkPolicy 的名称
  namespace: production           # 指定该策略适用的命名空间
spec:
  podSelector:                    # 定义目标 Pod 的选择器
    matchLabels:
      role: database              # 选择标签为 role=database 的 Pod
  policyTypes:                    # 定义策略类型
  - Ingress                       # 定义入站规则
  - Egress                        # 定义出站规则
  ingress:                        # 定义允许的入站流量规则
  - from:                         # 定义流量来源
    - podSelector:                # 匹配来源 Pod 的选择器
        matchLabels:
          role: frontend          # 来源 Pod 需带有标签 role=frontend
    ports:                        # 定义允许的目标端口
    - protocol: TCP               # 协议为 TCP
      port: 6379                  # 目标端口为 6379
  egress:                         # 定义允许的出站流量规则
  - to:                           # 定义流量目标
    - podSelector: {}             # 对目标 Pod 不设置选择器（允许所有目标）
    ports:                        # 定义允许的目标端口
    - protocol: TCP               # 协议为 TCP
      port: 6379                  # 目标端口为 6379
~~~~

![](E:\kubernetes\Calico\calicoctl\images\微信截图_20250112175421.png)













