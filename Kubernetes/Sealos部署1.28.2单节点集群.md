# kubernetes集群部署

## kubernetes集群规划

提示：系统尽量别带图形界面，图形比较吃内存。

| **主机名** | **IP地址**    | **角色** | **操作系统** |
| ---------- | ------------- | -------- | ------------ |
| master     | 192.168.0.151 | 管理节点 | Rocky 9.0    |
| node01     | 192.168.0.152 | 工作节点 | Rocky 9.0    |

## 集群前期环境准备

按照集群规划修改每个节点主机名

```shell
nmcli g hostname xxx
```



**提示：以下前期环境准备需要在所有节点都执行**

配置集群之间本地解析，集群在初始化时需要能够解析主机名

```shell
192.168.0.151 master
192.168.0.152 node01
```

## 开启bridge网桥过滤

bridge(桥接) 是 Linux 系统中的一种虚拟网络设备，它充当一个虚拟的交换机，为集群内的容器提供网络通信功能，容器就可以通过这个 bridge 与其他容器或外部网络通信了。

`/etc/sysctl.d/`目录用于存放配置内核参数的文件，文件以`.conf`结尾，这些文件会在系统启动时自动加载。

```shell
cat > /etc/sysctl.d/k8s.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
```

<details class="lake-collapse"><summary id="uafb55be2"><strong><span class="ne-text">参数解释：</span></strong></summary><p id="u78a472b0" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">net.bridge.bridge-nf-call-ip6tables = 1 </span><span class="ne-text" style="background-color: #FBE4E7"> //对网桥上的IPv6数据包通过iptables处理</span></p><p id="u0898c15e" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">net.bridge.bridge-nf-call-iptables = 1  </span><span class="ne-text" style="background-color: #FBE4E7"> //对网桥上的IPv4数据包通过iptables处理</span></p><p id="u46991a0c" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">net.ipv4.ip_forward = 1                        </span><span class="ne-text" style="background-color: #FBE4E7"> //开启IPv4路由转发,来实现集群中的容器跨网络通信</span></p></details>

由于开启bridge功能，需要加载br_netfilter模块来允许在bridge设备上的数据包经过iptables防火墙处理

```shell
modprobe br_netfilter && lsmod | grep br_netfilter
```

<details class="lake-collapse"><summary id="u4beabc91"><strong><span class="ne-text">命令解释：</span></strong></summary><p id="ub740f718" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">modprobe       	</span><span class="ne-text" style="background-color: #FBE4E7"> //命令可以加载内核模块</span></p><p id="ud9a00fd4" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">br_netfilter    		</span><span class="ne-text" style="background-color: #FBE4E7">//模块模块允许在bridge设备上的数据包经过iptables防火墙处理</span></p></details>

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

## 安装Sealos

~~~~powershell
[root@master ~]# mkdir sealos
[root@master ~]# tar xf sealos_5.0.1_linux_amd64.tar.gz -C sealos
[root@master ~]# mv sealos /usr/local/vim
[root@master ~]# vim /etc/profile
	export SEALOS_HOME=/usr/local/sealos/
	export PATH=$PATH:$SEALOS_HOME
[root@master ~]# source !$
[root@master ~]# sealos version
    CriVersionInfo:
      RuntimeApiVersion: v1
      RuntimeName: containerd
      RuntimeVersion: v1.7.15
      Version: 0.1.0
    KubernetesVersionInfo:
      clientVersion:
        buildDate: "2023-09-13T09:35:49Z"
        compiler: gc
        gitCommit: 89a4ea3e1e4ddd7f7572286090359983e0387b2f
        gitTreeState: clean
        gitVersion: v1.28.2
        goVersion: go1.20.8
        major: "1"
        minor: "28"
        platform: linux/amd64
      kustomizeVersion: v5.0.4-0.20230601165947-6ce0bf390ce3
      serverVersion:
        buildDate: "2023-09-13T09:29:07Z"
        compiler: gc
        gitCommit: 89a4ea3e1e4ddd7f7572286090359983e0387b2f
        gitTreeState: clean
        gitVersion: v1.28.2
        goVersion: go1.20.8
        major: "1"
        minor: "28"
        platform: linux/amd64
    SealosVersion:
      buildDate: "2024-10-09T02:18:27Z"
      compiler: gc
      gitCommit: 2b74a1281
      gitVersion: 5.0.1
      goVersion: go1.20.14
      platform: linux/amd64
~~~~

## 安装配置初始化

~~~~shell
[root@master ~]# sealos gen labring/kubernetes:v1.28.2 \
    labring/helm:v3.14.0 \
    labring/calico:v3.27.4 \
    labring/cert-manager:v1.16.1 \
    labring/openebs:v3.10.0 \
    --masters 192.168.0.151 \
    --nodes 192.168.0.152 > Clusterfile.yaml
[root@master ~]# sealos apply -f Clusterfile.yaml
~~~~

~~~~shell
[root@master ~]# kubectl get pod -A -o wide
NAMESPACE          NAME                                           READY   STATUS    RESTARTS       AGE   IP                NODE     NOMINATED NODE   READINESS GATES
calico-apiserver   calico-apiserver-7bcb77ccf8-cphg9              1/1     Running   0  54m   100.89.161.141    master   <none>           <none>
calico-apiserver   calico-apiserver-7bcb77ccf8-klhcd              1/1     Running   0              54m   100.117.144.135   node01   <none>           <none>
calico-system      calico-kube-controllers-5c77db9885-czqp8       1/1     Running   0  55m   100.89.161.142    master   <none>           <none>
calico-system      calico-node-fqqjq                              1/1     Running   0              55m   192.168.0.152     node01   <none>           <none>
calico-system      calico-node-kp2gn                              0/1     Running   0  55m   192.168.0.151     master   <none>           <none>
calico-system      calico-typha-8789b49d6-xfx2n                   1/1     Running   0              55m   192.168.0.152     node01   <none>           <none>
calico-system      csi-node-driver-ft6d4                          2/2     Running   0  55m   100.89.161.143    master   <none>           <none>
calico-system      csi-node-driver-slrvq                          2/2     Running   0              55m   100.117.144.131   node01   <none>           <none>
cert-manager       cert-manager-859bc755b6-n7ljs                  1/1     Running   0              55m   100.117.144.129   node01   <none>           <none>
cert-manager       cert-manager-cainjector-dc59548c5-wmfnk        1/1     Running   0              55m   100.117.144.132   node01   <none>           <none>
cert-manager       cert-manager-webhook-d45c9fbd6-w5ntf           1/1     Running   0              55m   100.117.144.130   node01   <none>           <none>
kube-system        coredns-5dd5756b68-4lfd5                       1/1     Running   0  55m   100.89.161.139    master   <none>           <none>
kube-system        coredns-5dd5756b68-h8phx                       1/1     Running   0  55m   100.89.161.140    master   <none>           <none>
kube-system        etcd-master                                    1/1     Running   0  55m   192.168.0.151     master   <none>           <none>
kube-system        kube-apiserver-master                          1/1     Running   0  55m   192.168.0.151     master   <none>           <none>
kube-system        kube-controller-manager-master                 1/1     Running   0  55m   192.168.0.151     master   <none>           <none>
kube-system        kube-proxy-2wxrp                               1/1     Running   0  55m   192.168.0.151     master   <none>           <none>
kube-system        kube-proxy-7vxd6                               1/1     Running   0              55m   192.168.0.152     node01   <none>           <none>
kube-system        kube-scheduler-master                          1/1     Running   0  55m   192.168.0.151     master   <none>           <none>
kube-system        kube-sealos-lvscare-node01                     1/1     Running   0              55m   192.168.0.152     node01   <none>           <none>
openebs            openebs-localpv-provisioner-56d6489bbc-8xhhp   1/1     Running   0              54m   100.117.144.134   node01   <none>           <none>
tigera-operator    tigera-operator-7fdd699b8c-s2hns               1/1     Running   0              55m   192.168.0.152     node01   <none>           <none>
~~~~



# Helm配置Ingress

## 使用 Helm 安装 NGINX Ingress 控制器

首先，你需要安装 NGINX Ingress 控制器。如果你的 Helm 仓库还没有添加 `ingress-nginx`，可以执行以下命令添加仓库：

```shell
[root@master ~]# helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
[root@master ~]# helm repo update
```

然后使用 Helm 安装 `ingress-nginx`：

```shell
[root@master ~]# helm search repo nginx
NAME                       	CHART VERSION	APP VERSION	DESCRIPTION                                       
ingress-nginx/ingress-nginx	4.11.3       	1.11.3     	Ingress controller for Kubernetes using NGINX a...

[root@master ~]# helm install ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace
```

上面这条命令会做以下几件事：

- 安装 NGINX Ingress 控制器。
- 在 Kubernetes 集群中创建 `ingress-nginx` 命名空间（如果尚未创建）。
- 部署 Ingress 控制器及相关资源。

你也可以选择一些常用的自定义配置，使用 `--set` 来配置安装选项。例如，启用外部负载均衡器：

```shell
[root@master ~]# helm install ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace \
  --set controller.service.externalTrafficPolicy=Local
```

## 验证 Ingress 控制器是否安装成功

安装完成后，可以使用以下命令验证 `ingress-nginx` 控制器是否安装成功：

```shell
[root@master ~]# kubectl get pods -n ingress-nginx
NAME                                       READY   STATUS    RESTARTS        AGE
ingress-nginx-controller-ddf5756b8-z99tq   1/1     Running   1 (4m26s ago)   6m47s
```

此命令将列出 `ingress-nginx` 命名空间中的所有 Pod，确认 NGINX Ingress 控制器是否已正常运行。









# Clusterfile.yaml

~~~~yaml
[root@master ~]# cat Clusterfile.yaml 
apiVersion: apps.sealos.io/v1beta1
kind: Cluster
metadata:
  creationTimestamp: null
  name: default
spec:
  hosts:
  - ips:
    - 192.168.0.151:22
    roles:
    - master
    - amd64
  - ips:
    - 192.168.0.152:22
    roles:
    - node
    - amd64
  image:
  - labring/kubernetes:v1.28.2
  - labring/helm:v3.14.0
  - labring/calico:v3.27.4
  - labring/cert-manager:v1.16.1
  - labring/openebs:v3.10.0
  ssh: {}
status: {}

---
apiVersion: kubeadm.k8s.io/v1beta3
kind: InitConfiguration
localAPIEndpoint:
  advertiseAddress: 192.168.0.151
  bindPort: 6443
nodeRegistration:
  criSocket: /run/containerd/containerd.sock
  taints: null

---
apiServer:
  certSANs:
  - 127.0.0.1
  - apiserver.cluster.local
  - 10.103.97.2
  - 192.168.0.151
  extraArgs:
    audit-log-format: json
    audit-log-maxage: "7"
    audit-log-maxbackup: "10"
    audit-log-maxsize: "100"
    audit-log-path: /var/log/kubernetes/audit.log
    audit-policy-file: /etc/kubernetes/audit-policy.yml
    enable-aggregator-routing: "true"
    feature-gates: ""
  extraVolumes:
  - hostPath: /etc/kubernetes
    mountPath: /etc/kubernetes
    name: audit
    pathType: DirectoryOrCreate
  - hostPath: /var/log/kubernetes
    mountPath: /var/log/kubernetes
    name: audit-log
    pathType: DirectoryOrCreate
  - hostPath: /etc/localtime
    mountPath: /etc/localtime
    name: localtime
    pathType: File
    readOnly: true
apiVersion: kubeadm.k8s.io/v1beta3
controlPlaneEndpoint: apiserver.cluster.local:6443
controllerManager:
  extraArgs:
    bind-address: 0.0.0.0
    cluster-signing-duration: 876000h
    feature-gates: ""
  extraVolumes:
  - hostPath: /etc/localtime
    mountPath: /etc/localtime
    name: localtime
    pathType: File
    readOnly: true
dns: {}
etcd:
  local:
    dataDir: ""
    extraArgs:
      listen-metrics-urls: http://0.0.0.0:2381
kind: ClusterConfiguration
kubernetesVersion: v1.28.2
networking:
  podSubnet: 100.64.0.0/10
  serviceSubnet: 10.96.0.0/22
scheduler:
  extraArgs:
    bind-address: 0.0.0.0
    feature-gates: ""
  extraVolumes:
  - hostPath: /etc/localtime
    mountPath: /etc/localtime
    name: localtime
    pathType: File
    readOnly: true

---
apiVersion: kubeadm.k8s.io/v1beta3
caCertPath: /etc/kubernetes/pki/ca.crt
controlPlane:
  localAPIEndpoint:
    bindPort: 6443
discovery:
  timeout: 5m0s
kind: JoinConfiguration
nodeRegistration:
  criSocket: /run/containerd/containerd.sock
  taints: null

---
apiVersion: kubeproxy.config.k8s.io/v1alpha1
bindAddress: 0.0.0.0
bindAddressHardFail: false
clientConnection:
  acceptContentTypes: ""
  burst: 10
  contentType: application/vnd.kubernetes.protobuf
  kubeconfig: ""
  qps: 5
clusterCIDR: ""
configSyncPeriod: 15m0s
conntrack:
  maxPerCore: 32768
  min: 131072
  tcpCloseWaitTimeout: 1h0m0s
  tcpEstablishedTimeout: 24h0m0s
detectLocal:
  bridgeInterface: ""
  interfaceNamePrefix: ""
detectLocalMode: ""
enableProfiling: false
healthzBindAddress: 0.0.0.0:10256
hostnameOverride: ""
iptables:
  localhostNodePorts: true
  masqueradeAll: false
  masqueradeBit: 14
  minSyncPeriod: 1s
  syncPeriod: 30s
ipvs:
  excludeCIDRs:
  - 10.103.97.2/32
  minSyncPeriod: 0s
  scheduler: ""
  strictARP: false
  syncPeriod: 30s
  tcpFinTimeout: 0s
  tcpTimeout: 0s
  udpTimeout: 0s
kind: KubeProxyConfiguration
metricsBindAddress: 0.0.0.0:10249
mode: ipvs
nodePortAddresses: null
oomScoreAdj: -999
portRange: ""
showHiddenMetricsForVersion: ""
winkernel:
  enableDSR: false
  forwardHealthCheckVip: false
  networkName: ""
  rootHnsEndpointName: ""
  sourceVip: ""

---
address: 0.0.0.0
apiVersion: kubelet.config.k8s.io/v1beta1
authentication:
  anonymous:
    enabled: false
  webhook:
    cacheTTL: 2m0s
    enabled: true
  x509:
    clientCAFile: /etc/kubernetes/pki/ca.crt
authorization:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: 5m0s
    cacheUnauthorizedTTL: 30s
cgroupDriver: cgroupfs
cgroupsPerQOS: true
configMapAndSecretChangeDetectionStrategy: Watch
containerLogMaxFiles: 5
containerLogMaxSize: 10Mi
containerRuntimeEndpoint: unix:///run/containerd/containerd.sock
contentType: application/vnd.kubernetes.protobuf
cpuCFSQuota: true
cpuCFSQuotaPeriod: 100ms
cpuManagerPolicy: none
cpuManagerReconcilePeriod: 10s
enableControllerAttachDetach: true
enableDebugFlagsHandler: true
enableDebuggingHandlers: true
enableProfilingHandler: true
enableServer: true
enableSystemLogHandler: true
enforceNodeAllocatable:
- pods
eventBurst: 10
eventRecordQPS: 5
evictionHard:
  imagefs.available: 10%
  memory.available: 100Mi
  nodefs.available: 10%
  nodefs.inodesFree: 5%
evictionPressureTransitionPeriod: 5m0s
failSwapOn: true
fileCheckFrequency: 20s
hairpinMode: promiscuous-bridge
healthzBindAddress: 0.0.0.0
healthzPort: 10248
httpCheckFrequency: 20s
imageGCHighThresholdPercent: 85
imageGCLowThresholdPercent: 75
imageMinimumGCAge: 2m0s
iptablesDropBit: 15
iptablesMasqueradeBit: 14
kind: KubeletConfiguration
kubeAPIBurst: 10
kubeAPIQPS: 5
localStorageCapacityIsolation: true
logging:
  flushFrequency: 5000000000
  format: text
  options:
    json:
      infoBufferSize: "0"
  verbosity: 0
makeIPTablesUtilChains: true
maxOpenFiles: 1000000
maxPods: 110
memoryManagerPolicy: None
memorySwap: {}
memoryThrottlingFactor: 0.9
nodeLeaseDurationSeconds: 40
nodeStatusMaxImages: 50
nodeStatusReportFrequency: 10s
nodeStatusUpdateFrequency: 10s
oomScoreAdj: -999
podPidsLimit: -1
port: 10250
registerNode: true
registryBurst: 10
registryPullQPS: 5
rotateCertificates: true
runtimeRequestTimeout: 2m0s
seccompDefault: false
serializeImagePulls: true
shutdownGracePeriod: 0s
shutdownGracePeriodCriticalPods: 0s
staticPodPath: /etc/kubernetes/manifests
streamingConnectionIdleTimeout: 4h0m0s
syncFrequency: 1m0s
topologyManagerPolicy: none
topologyManagerScope: container
volumePluginDir: /usr/libexec/kubernetes/kubelet-plugins/volume/exec/
volumeStatsAggPeriod: 1m0s
~~~~