# 第一章：Docker快速入门

## Docker介绍

Docker 是一个开源的容器运行时软件（容器运行时是负责运行容器的软件），基于 Go 语言编写，并遵从 Apache2.0 协议开源。 

Docker可以让开发者打包自己的应用以及依赖到一个轻量的容器中，然后发布到任何流行的Linux系统上（docker主要理念：一次封装随处运行）。

Docker的思想来源于集装箱，让容器与容器之间相互隔离，与系统相互隔离提高程序之间的安全，更重要的是容器性能开销极低。

Docker官网：[www.docker.com](http://www.docker.com)

## Docker组成部分

镜像（images）：用来创建容器的模板文件，一个镜像可以创建多个容器。

容器（container）：程序的载体，程序运行在容器中，每个容器相互隔离，互不影响，但可以相互通讯。

仓库（Repository）：集中存放镜像的场所，仓库分为公开仓库（public）和私有仓库（private）两种。

最大的公开仓库为docker hub：https://hub.docker.com

国内的公开仓库包括：阿里、网易、中科大等

## Docker版本介绍

Docker 从 1.17.0 版本之后分为

Docker CE（Community Edition）社区免费版，功能有限，不提供官方技术支持。

Docker EE（Enterprise Edition）企业收费版，功能全面，提供官方技术支持。

## 安装docker软件包

| **主机名** | **IP地址**    | **操作系统** | **硬件配置** |
| ---------- | ------------- | ------------ | ------------ |
| docker     | 192.168.0.110 | Rocky 9.0    | 2C/4G/50G    |

<details class="lake-collapse"><summary id="u6db864be"><strong><span class="ne-text" style="font-size: 22px">官方安装文档：</span></strong></summary><p id="ue292ee41" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><a href="https://docs.docker.com/engine/installation/linux/centos/" data-href="https://docs.docker.com/engine/installation/linux/centos/" target="_blank" class="ne-link"><span class="ne-text" style="font-size: 16px">https://docs.docker.com/engine/installation/linux/centos/</span></a></p></details>



下载阿里云docker-ce仓库

```shell
dnf install -y yum-utils && \
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

可查看仓库中所有版本，按照需求安装

```shell
dnf list --showduplicates docker-ce
dnf install -y docker-ce-3:20.10.24-3.el9
```

启动docker并设置随机自启

```shell
systemctl enable docker --now
```

卸载Docker方式：删除安装包， 删除镜像、容器、配置文件内容

```shell
dnf remove docker-ce
rm -rf /var/lib/docker
```

## 镜像常用管理命令

以下是Docker镜像常用管理命令 

| **命令**                       | **描述**                   |
| ------------------------------ | -------------------------- |
| `docker images`                | 列出本地所有镜像           |
| `docker search <image>`        | 从Docker Hub搜索镜像仓库   |
| `docker pull <image>`          | 从Docker Hub拉取镜像       |
| `docker rmi <image>`           | 删除一个镜像               |
| `docker image inspect <image>` | 查看镜像的详细信息和元数据 |



`docker images` 命令用于列出本地所有的镜，命令格式如下：

```shell
docker images
```

<details class="lake-collapse"><summary id="ue253f52f"><strong><span class="ne-text">字段解释：</span></strong></summary><p id="u3787eb2d" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">REPOSITORY		</span><span class="ne-text" style="background-color: #FBE4E7">//镜像名称</span></p><p id="u86737524" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">TAG   			</span><span class="ne-text" style="background-color: #FBE4E7">//镜像的标签</span></p><p id="ue1258f2a" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">IMAGE ID    		</span><span class="ne-text" style="background-color: #FBE4E7">//镜像的ID</span></p><p id="u3cce555a" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">CREATED     		</span><span class="ne-text" style="background-color: #FBE4E7">//镜像更新时间</span></p><p id="ua5d1522d" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">SIZE       			</span><span class="ne-text" style="background-color: #FBE4E7">//镜像大小</span></p></details>

`docker search` 命令用于在Docker Hub中搜索镜像，命令格式如下：

```shell
docker search nginx
```

<details class="lake-collapse"><summary id="uadd8f739"><strong><span class="ne-text">字段解释</span></strong></summary><p id="u4b12aa7d" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">NAME		 	</span><span class="ne-text" style="background-color: #FBE4E7">//镜像仓库名称 </span></p><p id="u13c11040" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">DESCRIPTION	 	</span><span class="ne-text" style="background-color: #FBE4E7">//镜像仓库描述 </span></p><p id="ue6788a8e" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">STARS        		</span><span class="ne-text" style="background-color: #FBE4E7">//评分（类似于点赞）</span></p><p id="ua5695985" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">OFFICIAL     		</span><span class="ne-text" style="background-color: #FBE4E7">//是否为官方镜像，[OK] 表示官方镜像</span></p><p id="u8cfc7030" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">AUTOMATED    	</span><span class="ne-text" style="background-color: #FBE4E7">//是否为自动构建的镜像，[OK]表示是，自动化构建是Docker Hub的一项功能，当代码仓库中的源代码发生变更时，Docker Hub会自动触发构建过程，从而创建一个新的镜像。</span></p></details>

`docker pull` 命令用于在Docker Hub中下载镜像，下载时如果不指定镜像版本则是仓库中的`latest` 版本，命令格式如下：

```shell
docker pull nginx:1.20.2
```

注：如果无法拉取镜像，可以配置镜像加速器

```shell
cat > /etc/docker/daemon.json <<EOF
{
		"registry-mirrors": ["https://docker.1ms.run"],
  		"dns": ["8.8.8.8", "8.8.4.4"]
}
EOF

cat > /etc/docker/daemon.json <<EOF
{
		"registry-mirrors": ["https://docker.1ms.run"]
}
EOF

cat > /etc/docker/daemon.json <<EOF
{
         "registry-mirrors": ["https://docker.rainbond.cc"]
}
EOF

cat > /etc/docker/daemon.json <<EOF
{
        "registry-mirrors": [ "https://abde64ba3c6d4242b9d12854789018c6.mirror.swr.myhuaweicloud.com" ]
}
EOF

cat > /etc/docker/daemon.json <<EOF
{
         "registry-mirrors": ["https://docker.rainbond.cc"]
}
EOF

cat > /etc/docker/daemon.json <<EOF
{
"data-root": "/data/docker",
"registry-mirrors": [
   "http://hub-mirror.c.163.com",
   "https://docker.1panelproxy.com",
   "https://docker-proxy.741001.xyz",
   "https://registry.docker-cn.com",
   "https://docker.anyhub.us.kg",
   "https://dockerhub.jobcher.com",
   "https://dockerhub.icu",
   "https://docker.awsl9527.cn" ]
}
```

重启docker生效

```shell
systemctl restart docker
```



## 容器常用管理命令

| **命令**                         | **作用**                                     |
| -------------------------------- | -------------------------------------------- |
| `docker run 选项...`             | 创建容器                                     |
| `docker ps`                      | 查看正在运行容器                             |
| `docker ps -a`                   | 查看所有容器（包含关闭的容器）               |
| `docker exec -it 容器名/容器ID`  | 进入容器（正在运行的状态）                   |
| `docker stop 容器名/容器ID`      | 停止容器                                     |
| `docker start 容器名/容器ID`     | 启动被停止的容器                             |
| `docker restart 容器名/容器ID`   | 重启容器                                     |
| `--restart=always`               | 创建容器时设置容器随机自启                   |
| `docker update`                  | 更新容器配置                                 |
| `docker logs 容器名/容器ID`      | 查看容器日志信息                             |
| `docker cp 容器名:路径 本机目录` | 拷贝容器中的数据到宿主机                     |
| `docker cp 本机目录 容器名:路径` | 拷贝宿主机中的数据到容器中                   |
| `docker rm 容器名/容器ID`        | 删除容器（-f 用于强制删除，无需关闭容器）    |
| `docker kill 容器名/容器ID`      | 强制停止正在运行的容器（一般不用，除非卡了） |
| `docker inspect 容器名称`        | 查看容器的元数据信息                         |
| `docker info `                   | 查看docker信息                               |



创建容器：`docker run 选项...`

<details class="lake-collapse"><summary id="u07ea5330"><strong><span class="ne-text" style="font-size: 22px">常用选项：</span></strong></summary><ul class="ne-ul" style="margin: 0; padding-left: 23px"><li id="u735a1402" data-lake-index-type="0"><span class="ne-text" style="font-size: 16px">-d                              //创建容器并指定容器在后台运行</span></li><li id="u658963f0" data-lake-index-type="0"><span class="ne-text" style="font-size: 16px">-it                               //创建容器，并直接进入到容器内部，一旦退出容器，容器关闭</span></li><li id="u760793a7" data-lake-index-type="0"><span class="ne-text" style="font-size: 16px">--name="名称"          //为容器指定一个名称 </span></li><li id="udf943e9c" data-lake-index-type="0"><span class="ne-text" style="font-size: 16px">-p                              //指定端口映射，格式为：宿主机端口:容器端口</span></li></ul></details>

```shell
docker run -d --name=nginx nginx:1.20.2
```

查看正在运行的容器信息

```shell
docker ps        
```

查看所有容器,包括未运行的

```shell
docker ps -a     
```

查看容器元数据信息

```shell
docker inspect nginx
===========================================================
[
    {
        //容器的ID
        "Id": "4d065f6f5f4ea5c324faf7c574b665251c0d4af47a319fc491e6bc4e96beebb6",
        //容器创建时间（UTC时间）
        "Created": "2024-09-16T09:57:38.57677014Z",
        //容器启动时执行的命令
        "Path": "/docker-entrypoint.sh",
        //容器启动时传递的参数
        "Args": [
            "nginx",
            "-g",
            "daemon off;"
        ],
        //容器的状态
        "State": {
            //当前状态是运行中
            "Status": "running",
            //true表示容器正在运行
            "Running": true,
            //false表示容器没有被暂停
            "Paused": false,
            //false表示容器没有重启中
            "Restarting": false,
            //false表示容器没有因为内存不足被杀死
            "OOMKilled": false,
            //false表示容器没有死亡
            "Dead": false,
            //容器的进程ID
            "Pid": 343962,
            //上一次容器退出的状态码。0 表示成功退出
            "ExitCode": 0,
            //容器运行中没有错误
            "Error": "",
            //容器启动时间（UTC 时间）
            "StartedAt": "2024-09-16T09:57:38.921817969Z",
            //容器尚未结束，所以这个时间是一个默认的占位符
            "FinishedAt": "0001-01-01T00:00:00Z"
        },

        //容器镜像的SHA256哈希值
        "Image": "sha256:0584b370e957bf9d09e10f424859a02ab0fda255103f75b3f8c7d410a4e96ed5",
        //这些路径指向容器内部用于 DNS 配置、主机名、主机文件和日志文件的位置
        "ResolvConfPath": "/var/lib/docker/containers/4d065f6f5f4ea5c324faf7c574b665251c0d4af47a319fc491e6bc4e96beebb6/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/4d065f6f5f4ea5c324faf7c574b665251c0d4af47a319fc491e6bc4e96beebb6/hostname",
        "HostsPath": "/var/lib/docker/containers/4d065f6f5f4ea5c324faf7c574b665251c0d4af47a319fc491e6bc4e96beebb6/hosts",
        "LogPath": "/var/lib/docker/containers/4d065f6f5f4ea5c324faf7c574b665251c0d4af47a319fc491e6bc4e96beebb6/4d065f6f5f4ea5c324faf7c574b665251c0d4af47a319fc491e6bc4e96beebb6-json.log",
        //容器的名称
        "Name": "/nginx",
        //容器的重启次数
        "RestartCount": 0,
        //Docker存储驱动，overlay2是默认的存储驱动
        "Driver": "overlay2",
        //容器的运行平台是 Linux
        "Platform": "linux",
        //MountLabel, ProcessLabel, AppArmorProfile安全相关的标签，通常为空，表示没有设置特定的安全配置
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        //docker exec 命令的执行 ID，null表示当前没有任何 docker exec 命令在容器中运行
        "ExecIDs": null,
        //关于容器的主机配置
        "HostConfig": {
            //容器的挂载点（即主机文件或目录挂载到容器中的位置）null表示当前没有任何挂载点设置
            "Binds": null,
            //这是一个文件路径，用于存储容器ID文件。此文件可以用于进程间通信或者在主机和容器之间传递信息。为空表示没有设置这个文件
            "ContainerIDFile": "",
            //关于日志记录配置
            "LogConfig": {
                //指定了日志记录的驱动类型json-file，表示容器的日志会被存储为JSON文件
                "Type": "json-file",
                //用于指定日志驱动的额外配置，为空则表示没有为json-file日志驱动提供额外的配置选项
                "Config": {}
            },
            //使用默认网络模式（通常是 bridge）
            "NetworkMode": "default",
            //容器没有公开端口到主机
            "PortBindings": {},
            //容器重启策略，Name: no表示容器在停止后不会自动重启
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            
            //容器停止后是否会被自动删除，为true时容器在停止后会被Docker自动删除；false表示容器不会在停止后自动删除
            "AutoRemove": false,
            //指定用于管理容器卷的驱动，为空表示没有使用特定的卷驱动
            "VolumeDriver": "",
            //列出容器是否从其他容器挂载的数据卷，null表示没有挂载其他容器的卷
            "VolumesFrom": null,
            //列出容器中是否添加的额外Linux功能。null表示没有添加额外的功能
            "CapAdd": null,
            //列出从容器中移除的Linux功能。null表示没有移除任何功能
            "CapDrop": null,
            //容器是否使用私有的cgroup，"private" 表示容器有自己的cgroup，不与主机或其他容器共享
            "CgroupnsMode": "private",
            //容器的DNS服务器列表，空数组表示容器使用主机的默认DNS配置
            "Dns": [],
            //DNS 配置的选项，空数组表示没有设置特定的DNS选项
            "DnsOptions": [],
            //DNS搜索域，空数组表示没有设置DNS搜索域
            "DnsSearch": [],
            //用于指定额外的主机名映射，null表示没有额外的主机名映射
            "ExtraHosts": null,
            //列出容器中添加的用户组，null表示没有添加额外的用户组
            "GroupAdd": null,
            //容器的IPC模式，"private"表示容器有自己的IPC，不与主机或其他容器共享
            "IpcMode": "private",
            //Cgroup的父级路径，这个字段为空，表示没有设置特定的cgroup路径
            "Cgroup": "",
            //列出容器间的链接信息，null表示没有设置容器间的链接
            "Links": null,
            //调整容器的OOM (Out Of Memory) 评分，0表示默认的OOM评分
            "OomScoreAdj": 0,
            //容器的PID模式，为空字符串表示容器使用默认的PID模式
            "PidMode": "",
            //容器是否以特权模式运行，false表示容器没有以特权模式运行
            "Privileged": false,
            //为true表示容器的所有暴露的端口都会被映射到主机上；false表示只有显式映射的端口会被公开
            "PublishAllPorts": false,
            //指示容器的根文件系统是否为只读，false表示根文件系统是可读写
            "ReadonlyRootfs": false,
            //用于指定安全选项，null表示没有设置额外的安全选项
            "SecurityOpt": null,
            //定义了容器的 UTS (UNIX Time-sharing System) 模式，为空表示使用默认的UTS模式
            "UTSMode": "",
            //定义了容器的用户模式，为空表示使用默认的用户模式
            "UsernsMode": "",
            //指定容器的共享内存大小，单位是字节
            "ShmSize": 67108864,
            //指定容器的运行时，"runc" 是Docker的默认运行时
            "Runtime": "runc",
            //指定容器控制台的大小（行数和列数）这里表示控制台的大小是30行和117列
            "ConsoleSize": [
                30,
                117
            ],
            //指定了容器的隔离模式，为空表示使用默认的隔离模式，Docker支持不同的隔离技术，如process和hyperv，但这里没有特别指定
            "Isolation": "",
            //容器的 CPU 共享权重，用于CPU资源的分配和调度，0表示使用默认值通常为1024
            "CpuShares": 0,
            //容器的内存限制，单位为字节
            "Memory": 209715200,
            //容器的CPU配额，以纳秒为单位，0表示没有特别设置CPU配额
            "NanoCpus": 0,
            //容器的cgroup父级路径，为空表示使用默认
            "CgroupParent": "",
            //容器的块I/O权重，0表示使用默认值，通常为500，这个值用于控制容器在块设备上的I/O优先级
            "BlkioWeight": 0,
            //容器的块I/O权重设备的详细配置，空表示没有指定特定的设备或权重
            "BlkioWeightDevice": [],
            //容器的块设备读速率限制，单位为字节每秒，空表示没有设置读速率限制
            "BlkioDeviceReadBps": [],
            //容器的块设备写速率限制，单位为字节每秒，空表示没有设置写速率限制
            "BlkioDeviceWriteBps": [],
            //容器的块设备读IOPS限制（每秒输入/输出操作次数）空表示没有设置读IOPS限制
            "BlkioDeviceReadIOps": [],
            //容器的块设备写IOPS限制，空表示没有设置写IOPS限制
            "BlkioDeviceWriteIOps": [],
            //定义CPU时间片的周期（单位为微秒）0表示使用默认值，通常为100000微秒（100 毫秒）这个设置与CPU配额一起使用，以限制容器的CPU使用时间
            "CpuPeriod": 0,
            //容器的CPU配额（单位为微秒）0 表示没有特别设置CPU配额。结合CpuPeriod使用，可以限制容器的CPU使用时间
            "CpuQuota": 0,
            //实时CPU调度的周期（单位为微秒）0表示没有设置实时CPU调度
            "CpuRealtimePeriod": 0,
            //实时CPU调度的运行时间（单位为微秒）0表示没有设置实时CPU运行时间
            "CpuRealtimeRuntime": 0,
            //容器可以使用的CPU核心，为空表示容器可以使用主机上所有的CPU核心
            "CpusetCpus": "",
            //容器可以使用的内存节点，为空表示容器可以使用主机上所有的内存节点
            "CpusetMems": "",
            //容器可以访问的设备，空表示没有特别设置设备访问
            "Devices": [],
            //用于设置设备的cgroup规则，null表示没有设置特定的设备cgroup规则
            "DeviceCgroupRules": null,
            //用于请求特定的设备资源，null表示没有设备请求
            "DeviceRequests": null,
            //容器的内核内存限制（单位为字节）0表示没有设置内核内存限制
            "KernelMemory": 0,
            //容器的内核TCP内存限制（单位为字节）0表示没有设置内核TCP内存限制
            "KernelMemoryTCP": 0,
            //内存保留量，用于设置在内存不足时保证的内存量（单位为字节）0表示没有设置内存保留量
            "MemoryReservation": 0,
            //容器的总内存限制，包括交换空间（单位为字节）在这个例子中，限制为 419430400 字节（400 MB）
            "MemorySwap": 419430400,
            //设置内存的可交换性（swappiness）null表示没有设置特定的交换性值
            "MemorySwappiness": null,
            //是否禁用OOM (Out Of Memory) 杀手，null表示没有禁用OOM杀手
            "OomKillDisable": null,
            //容器的PID限制，null表示没有设置PID限制
            "PidsLimit": null,
            //容器的资源限制（如文件描述符的最大数量）空表示没有设置特定的资源限制
            "Ulimits": [],
            //容器的CPU数量限制，0表示没有特别设置
            "CpuCount": 0,
            //容器的CPU使用百分比限制，0表示没有特别设置
            "CpuPercent": 0,
            //器的最大I/O操作次数限制，0表示没有设置限制
            "IOMaximumIOps": 0,
            //容器的最大I/O带宽限制，0表示没有设置限制
            "IOMaximumBandwidth": 0,
            //MaskedPaths字段列出了在容器内被屏蔽的路径，即容器内的进程无法访问宿主机这些路径，屏蔽这些路径是容器安全性和隔离机制的一部分，确保容器只访问其被授权的资源，避免对主机系统造成潜在的风险
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            //ReadonlyPaths字段列出了在容器中被设置为只读的路径
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        //GraphDriver字段提供了有关Docker使用的存储驱动的信息，存储驱动用于管理容器文件系统，这个字段下的信息告诉我们当前容器使用的存储驱动及其配置
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/1e82dab886c5c22af0def4e709cebaeacdcb8d24fa9dd37ccc38360bcc61cef4-init/diff:/var/lib/docker/overlay2/59f97564e18a083052bfec0fa190e672e3d56d5006d9e6324caac32af54d6ce2/diff:/var/lib/docker/overlay2/968c759c58842bca95ea9bbc5c9a5e953ee11d8a2637bb2d8ecd972b4858dd42/diff:/var/lib/docker/overlay2/f5c017b075d80b5d4e8fd0d243e4cc00bcf45d8939a0fffdc482346bb1c41eca/diff:/var/lib/docker/overlay2/b341e0f92dcba6c3c0bef2dd548f5bfd96da9fd0421b15c1174d3e8232fe3373/diff:/var/lib/docker/overlay2/4055b28195f2f3485f2c3b40b2a495803bbad9653bc9433560644e535af4e9d9/diff:/var/lib/docker/overlay2/9c882e697021be8407a25684bd24bde09137af7d677e4340502958be53fafd00/diff",
                "MergedDir": "/var/lib/docker/overlay2/1e82dab886c5c22af0def4e709cebaeacdcb8d24fa9dd37ccc38360bcc61cef4/merged",
                "UpperDir": "/var/lib/docker/overlay2/1e82dab886c5c22af0def4e709cebaeacdcb8d24fa9dd37ccc38360bcc61cef4/diff",
                "WorkDir": "/var/lib/docker/overlay2/1e82dab886c5c22af0def4e709cebaeacdcb8d24fa9dd37ccc38360bcc61cef4/work"
            },
            "Name": "overlay2"
        },
        //容器挂载的卷或目录，空表示容器没有挂载任何外部卷或绑定挂载
        "Mounts": [],
        //Config包含容器的配置详细信息，包括网络设置、环境变量、命令、端口暴露等
        "Config": {
            //容器的主机名（既容器前11位ID）
            "Hostname": "4d065f6f5f4e",
            //容器的域名，默认情况下Docker容器没有指定的域名
            "Domainname": "",
            //容器内进程以哪个用户身份运行，此处为空，进程将以默认用户身份运行（通常是 root 用户）
            "User": "",
            //容器是否连接到标准输入，false表示没有，容器启动时不会附加到终端的标准输入流
            "AttachStdin": false,
            //容器是否连接到标准输出，false表示没有，容器的输出不会显示在终端上
            "AttachStdout": false,
            //容器是否连接到标准错误输出，false表示没有，容器的错误输出不会显示在终端上
            "AttachStderr": false,
            //容器暴露的端口
            "ExposedPorts": {
                "80/tcp": {}
            },
            //容器是否分配了伪终端，false表示没有
            "Tty": false,
            //容器的标准输入是否打开，true表示打开
            "OpenStdin": true,
            //false表示标准输入不会在第一次使用后自动关闭，为true则会
            "StdinOnce": false,
            //容器内的环境变量配置信息
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.20.2",
                "NJS_VERSION=0.7.3",
                "PKG_RELEASE=1~bullseye"
            ],
            //容器启动时的默认命令
            "Cmd": [
                "nginx",
                "-g",
                "daemon off;"
            ],
            //容器使用的镜像及其版本
            "Image": "nginx:1.20.2",
            //null表示容器没有定义任何卷
            "Volumes": null,
            //容器的工作目录配置为空，容器将使用默认工作目录，通常是 / 根目录
            "WorkingDir": "",
            //容器启动时执行的脚本，通常用于初始化容器环境或启动应用程序
            "Entrypoint": [
                "/docker-entrypoint.sh"
            ],
            //OnBuild主要用于在构建新的镜像时自动执行额外的指令，null表示没有设置
            "OnBuild": null,
            //容器或镜像指定的标签或元数据
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
            //停止容器时发送给主进程的信号，SIGQUIT信号用于优雅地终止进程并生成核心转储文件以供调试使用
            "StopSignal": "SIGQUIT"
        },
        //容器的网络设置信息
        "NetworkSettings": {
            //容器使用的桥接网络接口，为空表示容器没有配置特定的桥接网络，则使用默认的bridge网络
            "Bridge": "",
            //容器的网络沙箱ID，网络沙箱是Docker为每个容器创建的隔离网络环境，SandboxID是该沙箱的唯一标识符，它用于区分不同容器的网络配置和隔离环境
            "SandboxID": "596c05c53bee060ad9a0606edca89db9ec2a6879bb66f220c3d51d9d3c1e3fea",
            //是否启用了头发夹模式（hairpin mode）false表示没有启用头发夹模式，头发夹模式允许容器通过其自己的IP地址访问自身，但在大多数情况下，默认设置即可
            "HairpinMode": false,
            //容器的链路本地IPv6地址，空表示容器没有配置链路本地IPv6地址
            "LinkLocalIPv6Address": "",
            //指定链路本地 IPv6 地址的前缀长度，0表示没有配置链路本地IPv6 地址，因此没有前缀长度
            "LinkLocalIPv6PrefixLen": 0,
            //容器的端口映射，null表示容器的80端口（TCP）没有进行端口映射到宿主机的端口
            "Ports": {
                "80/tcp": null
            },
            //容器网络的的文件路径
            "SandboxKey": "/var/run/docker/netns/596c05c53bee",
            //容器的附加IP地址，null表示没有，通常情况下，容器只会有一个主要的IP地址，但也可以配置附加的IP地址用于不同的网络需求
            "SecondaryIPAddresses": null,
            //容器的附加IPv6地址，null表示没有
            "SecondaryIPv6Addresses": null,
            //容器网络接口的唯一标识符
            "EndpointID": "cb83d59559d90b6728557ca544bc2b9d01dba0278d6f662fd5348f588f2aa023",
            //容器网络的网关地址，容器通过这个网关与其他网络进行通信
            "Gateway": "172.17.0.1",
            //容器的全局IPv6地址，为空表示没有
            "GlobalIPv6Address": "",
            //全局IPv6地址的前缀长度，0表示没有配置全局IPv6地址，因此前缀长度为0
            "GlobalIPv6PrefixLen": 0,
            //容器的IPv4地址
            "IPAddress": "172.17.0.3",
            //容器的IP地址的前缀长度，16表示172.17.0.0/16子网
            "IPPrefixLen": 16,
            //容器的IPv6网关地址，字段为空，表示容器没有配置IPv6网关
            "IPv6Gateway": "",
            //容器的网络接口的MAC地址
            "MacAddress": "02:42:ac:11:00:03",
            //容器连接到的网络的详细信息
            "Networks": {
                //这是Docker默认的桥接网络（bridge network）名称
                "bridge": {
                    //指定网络的IP地址管理配置，为空表示使用网络的默认配置
                    "IPAMConfig": null,
                    //容器之间的链接，为空表示没有配置容器间的链接
                    "Links": null,
                    //容器在网络上的别名，为空表示没有为容器配置网络别名
                    "Aliases": null,
                    //网络的唯一标识符，用于区分不同的网络
                    "NetworkID": "9712c6851fcfbe85dd6532c485706484c0af96a8ecc534c14e1a77f996f192ec",
                    //容器的网络端点的唯一标识符，标识容器在网络中的连接点
                    "EndpointID": "cb83d59559d90b6728557ca544bc2b9d01dba0278d6f662fd5348f588f2aa023",
                    //容器网络的网关地址
                    "Gateway": "172.17.0.1",
                    //容器在网络中的IP地址
                    "IPAddress": "172.17.0.3",
                    //IP地址的前缀长度，表示子网掩码。16表示172.17.0.0/16子网
                    "IPPrefixLen": 16,
                    //容器的IPv6网关地址，为空表示没有配置IPv6网关
                    "IPv6Gateway": "",
                    //容器的全局IPv6地址，为空表示没有配置全局IPv6地址
                    "GlobalIPv6Address": "",
                    //全局IPv6地址的前缀长度，0表示没有配置全局IPv6地址
                    "GlobalIPv6PrefixLen": 0,
                    //容器网络接口的MAC地址
                    "MacAddress": "02:42:ac:11:00:03",
                    //网络驱动的选项，null表示没有指定额外的驱动选项
                    "DriverOpts": null
                }
            }
        }
    }
]
```



进入容器：docker  exec  -it   容器ID/容器名  解释器程序

- -it ：interaction（交互）terminal（终端）进入容器

```shell
docker exec -it nginx /bin/bash
```

停止容器：docker  stop  容器名/容器ID

```shell
docker  stop  nginx
```

启动被停止的容器：docker  start  容器名/容器ID

```shell
docker start nginx
```

启动被停止的所有容器：ps  -aq 获取所有容器ID

```shell
docker start `docker ps -aq`
```

删除容器：docker  rm  容器名/容器ID

- -f ：强制删除（无需停止容器）

```shell
docker  rm  nginx
```

删除所有容器：ps  -aq  #获取所有容器ID

```shell
docker rm `docker ps -aq`
```

删除所有已经停止运行的容器

```shell
docker container prune
```

查看docker信息

```shell
docker info
===========================================================
Client: Docker Engine - Community
 //Docker客户端（docker-ce-cli）的版本
 Version:    27.2.1
 //当前Docker上下文是default，这表示你正在使用默认的Docker环境
 Context:    default
 //处于false，表示调试模式没有启用
 Debug Mode: false
 //显示已安装的Docker插件及其版本
 Plugins:
  //用于构建镜像的插件版本及插件路径
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.16.2
    Path:     /usr/libexec/docker/cli-plugins/docker-buildx
  //Docker Compose插件版本及插件路径，用于定义和运行多容器Docker工具
  compose: Docker Compose (Docker Inc.)
    Version:  v2.29.2
    Path:     /usr/libexec/docker/cli-plugins/docker-compose

//显示容器的信息
Server:
 //容器数量
 Containers: 0
  //当前运行中的容器
  Running: 0
  //当前暂停的容器。
  Paused: 0
  //当前停止的容器
  Stopped: 0
 //当前有几个Docker镜像
 Images: 1
 //Docker引擎的版本是 20.10.24
 Server Version: 20.10.24
 //使用overlay2存储驱动
 Storage Driver: overlay2
  //使用 xfs 文件系统
  Backing Filesystem: xfs
  //Docker运行时的存储驱动支持d_type，这是文件系统的一个特性，允许存储文件的类型信息
  Supports d_type: true
  //Docker存储驱动原生支持Overlay文件系统的差异比较功能，主要用于对文件的差异处理
  Native Overlay Diff: true
  //Docker的存储驱动不支持用户扩展属性
  userxattr: false
 //Docker容器的日志驱动是json-file，容器的日志将被以JSON格式保存到本地文件中
 Logging Driver: json-file
 //Docker使用systemd作为cgroup驱动，cgroup（控制组）是Linux内核的一部分，用于管理系统资源的分配和限制
 Cgroup Driver: systemd
 //使用的是cgroup的第二版
 Cgroup Version: 2
 //Docker支持的插件类型
 Plugins:
  //目前支持的存储插件为local，即本地存储
  Volume: local
  //支持的网络插件
  Network: bridge host ipvlan macvlan null overlay
  //支持的日志插件
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 //Docker Swarm模式未启用，Swarm是Docker的集群管理工具
 Swarm: inactive
 //Docker 支持的容器运行时
 Runtimes: io.containerd.runc.v2 io.containerd.runtime.v1.linux runc
 //默认运行时是runc
 Default Runtime: runc
 //Docker使用的初始化程序是docker-init
 Init Binary: docker-init
 //containerd的版本
 containerd version: 7f7fdf5fed64eb6a7caf99b3e12efcf9d60e311c
 //runc的版本
 runc version: v1.1.14-0-g2c9f560
 //Docker初始化程序的版本
 init version: de40ad0
 //安全选项
 Security Options:
  //使用seccomp安全配置
  seccomp
   //使用默认的seccomp配置文件来限制容器的系统调用
   Profile: default
  //使用cgroup名空间
  cgroupns
 //主机系统的Linux内核版本
 Kernel Version: 5.14.0-70.13.1.el9_0.x86_64
 //主机操作系统版本
 Operating System: Rocky Linux 9.0 (Blue Onyx)
 //主机的操作系统类型
 OSType: linux
 //主机系统的架构
 Architecture: x86_64
 //主机有2个CPU核心
 CPUs: 2
 //主机的总内存
 Total Memory: 3.61GiB
 //主机名
 Name: docker01
 //Docker主机的唯一标识符
 ID: 2VPU:J57Z:E6AN:HWOF:35IO:BL5P:UNVZ:BQS7:A4S3:5H5M:XUSD:EB6R
 //Docker数据的根目录位置
 Docker Root Dir: /var/lib/docker
 //Docker的调试模式未启用
 Debug Mode: false
 //实验性功能未启用
 Experimental: false
 //不安全的注册表地址（镜像仓库）为本机， 这是Docker的一个配置选项，它允在访问某些镜像仓库时，不使用HTTPS，而是使用HTTP
 Insecure Registries:
  127.0.0.0/8
 // Docker拉取镜像的仓库地址
 Registry Mirrors:
  https://docker.rainbond.cc/
 //容器的实时恢复功能未启用
 Live Restore Enabled: false
```



**练习：**通过Docker部署Nginx的web应用并实现浏览器访问

<details class="lake-collapse"><summary id="u55983cc0"><strong><span class="ne-text" style="font-size: 22px">选项说明：</span></strong></summary><ul class="ne-ul" style="margin: 0; padding-left: 23px"><li id="u7781598a" data-lake-index-type="0"><span class="ne-text" style="font-size: 16px">-p                          //指定端口映射，格式为：宿主机端口:容器端口</span></li><li id="u2e29b35c" data-lake-index-type="0"><span class="ne-text" style="font-size: 16px">--restart=always   //设置容器随机自启</span></li></ul></details>

```shell
docker run -d --name=nginx --restart=always -p 80:80  nginx:1.22.0
```

查看iptables规则

```shell
iptables -nL -t nat
#...
Chain DOCKER (2 references)
target     prot opt source        destination         
RETURN     all  --  0.0.0.0/0     0.0.0.0/0           
DNAT       tcp  --  0.0.0.0/0     0.0.0.0/0     tcp dpt:3306 to:172.17.0.2:3306
```



浏览器访问：http://server_ip:port

# 第二章：Docker容器数据卷

## 容器数据卷作用

在 Docker 中，数据卷 (Volumes) 的主要作用如下： 

- 数据持久化：数据卷是通过将宿主机中的目录或文件挂载到容器中，来解决容器删除后，容器中的数据仍然持久化保存的作用。
- 数据共享：一个数据卷可以挂载给多个容器来实现多容器间的数据共享。

- 数据同步：当需要对容器中的文件进行修改时，可以在宿主机上操作数据卷中的文件，容器会自动同步。

## 配置容器数据卷

在容器中部署MySQL数据库，并通过数据卷持久化保存数据库相关的数据。

```shell
docker pull mysql:8.0
```

创建数据卷目录

```shell
mkdir -p /volumes-mysql/
```

创建MySQL容器并挂载数据卷

- -v      //宿主机目录/文件:容器中目录/文件
- -e      //指定环境变量

提示：目录必须指定绝对路径，如果目录不存在，会自动创建。

```shell
docker run -d --name=mysql -p 3306:3306 \
-v /volumes-mysql/data/:/var/lib/mysql/ \
-v /volumes-mysql/logs/:/var/log/ \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:8.0
```

进入数据库验证

```shell
# step 1：进入容器
docker exec -it mysql /bin/bash
# step 2：进入数据库
mysql -uroot -p123456
```

宿主机查看数据卷目录

```shell
ls /volumes-mysql/
```

删除MySQL容器，并验证宿主机数据是否存在

```shell
# step 1：停止容器
docker stop mysql
# step 2：删除容器
docker rm mysql
```



# 第三章：DockerFile定制镜像

当我们从镜像仓库中下载的镜像不能满足我们的需求时，我们可以通过DockerFile对镜像进行更改，或者按照自己的需求制作镜像。

## DockerFile常用指令

| **指令**  | **说明**                                                     |
| --------- | ------------------------------------------------------------ |
| `FROM`    | 定义基础镜像                                                 |
| `LABEL`   | 定义镜像元数据，例如：版本、作者、邮箱等信息。               |
| `RUN`     | 构建镜像时运行的命令。                                       |
| `ADD`     | 添加文件或目录到镜像中，可以是本地文件，也可以是`url`。如果添加.gz格式压缩包，会自动解压。 |
| `COPY`    | 拷贝文件或目录到镜像中。只能拷贝当前路径下的文件，且不支持解压功能。 |
| `EXPOSE`  | 指定容器运行后监听的端口，协议默认TCP。                      |
| `ENV`     | 设置容器内环境变量。                                         |
| `CMD`     | 启动容器时执行的`Shell`命令。在`Dockerfile`中只能有一条`CMD`指令。如果设置了多条`CMD`，只有最后一条会生效。 |
| `WORKDIR` | 为 RUN、CMD、COPY、AND 设置工作目录。                        |
| `VOLUME`  | 定义容器数据卷。                                             |



## 制作MySQL镜像

要求在MySQL镜像的基础上，为root设置密码，并创建一个名为wordpressd的数据库。

```shell
mkdir mysql && cd mysql
```

通过dockerfile制作镜像

```dockerfile
# step 1：使用MySQL 8.0作为基础镜像
FROM mysql:8.0

# step 2：定义镜像作者，仅支持 key=value 方式定义
# 需要通过 docker inspect 镜像名:版本 方式查看元数据信息
#定义镜像元数据
LABEL user="yesir" \
      email="yesir@163.com" \
      version="wp-mysql:8.0"

# step 3：修改镜像时区
COPY ./Shanghai /etc/localtime

# step 4：通过环境变量设置MySQL的root密码，并创建wordpress数据库
# MySQL容器中的root用户默认支持远程访问
ENV MYSQL_ROOT_PASSWORD=Admin123...
ENV MYSQL_DATABASE=wordpress

# step 5：挂载容器目录到宿主机（提示：不能自定义挂载到宿主机的某个目录）
VOLUME /var/lib/mysql
```

拷贝宿主机时区文件到当前dockerfile目录

```shell
cp /usr/share/zoneinfo/Asia/Shanghai .
```

构建镜像

<details class="lake-collapse"><summary id="ub5123e44"><strong><span class="ne-text" style="font-size: 22px">选项说明：</span></strong></summary><p id="u0f5f2c1e" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="font-size: 16px">-f        </span><span class="ne-text" style="background-color: #FBE4E7; font-size: 16px">//用于指定基于那个文件构建镜像，如果文件名就叫dockerfile，可以省略 -f。</span></p><p id="uf2d16d1f" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="font-size: 16px">-t      </span><span class="ne-text" style="background-color: #FBE4E7; font-size: 16px"> //用于指定构建后的镜像名称，不可省略。</span></p></details>

```shell
docker build -f dockerfile -t wp-mysql:8.0 .
```

**提示**：`docker build `命令最后的`.`表示当前目录，在执行`docker build`命令后，会将Dockerfile文件所在路径下的所有文件或目录打包到镜像中，`.`不可省略。所以在Dockerfile目录下不要放其他多余的文件，避免增加镜像体积。



创建 MySQL 容器

```shell
docker run -d --name=wp-mysql --restart=always -p 3306:3306 wp-mysql:8.0
```



查看数据卷信息

```shell
docker volume ls
===========================================================
DRIVER    VOLUME NAME
local     7d08f40c3f0b73de0ff39e0a4617a366b99a80db2acb87334f1787472bbf3700
local     2735b56309659206e35da287655ac2eab78390ee112a3d654f21524f8613494c
```

**提示：**VOLUME NAME 下方显示的ID需要再宿主机`/var/lib/docker/volumes/`目录查看对应ID的目录。

查看数据卷ID对应目录

```shell
ls /var/lib/docker/volumes/
===========================================================
2735b56309659206e35da287655ac2eab78390ee112a3d654f21524f8613494c  
7d08f40c3f0b73de0ff39e0a4617a366b99a80db2acb87334f1787472bbf3700  
```

查看目录内容

```shell
ls /var/lib/docker/volumes/2735b56309659206e35da287655ac2eab78390ee112a3d654f21524f8613494c/
===========================================================
_data
```

查看`_data`目录下的内容（挂载出来的数据在`_data`目录）

```shell
ls /var/lib/docker/volumes/2735b56309659206e35da287655ac2eab78390ee112a3d654f21524f8613494c/_data/
===========================================================
'#ib_16384_0.dblwr'   binlog.000001   client-cert.pem   mysql                public_key.pem    undo_002
'#ib_16384_1.dblwr'   binlog.000002   client-key.pem    mysql.ibd            server-cert.pem   wordpress
'#innodb_redo'        binlog.index    ib_buffer_pool    mysql.sock           server-key.pem
'#innodb_temp'        ca-key.pem      ibdata1           performance_schema   sys
 auto.cnf             ca.pem          ibtmp1            private_key.pem      undo_001
```

不建议在构建镜像时挂载数据卷，应为无法自定义数据卷挂载到宿主机的目录，后期不方便查看和管理。建议在创建容器时通过`-v`来指定数据卷挂载目录。



## 制作WordPress镜像

WordPress是PHP语言开发的博客平台，是运行在典型的LNMP/LAMP环境中，本案例需要将wordpress部署在docker容器中，并实现外部访问。

<details class="lake-collapse"><summary id="u4df43281"><strong><span class="ne-text" style="font-size: 22px">安装文档：</span></strong></summary><p id="u53a75f95" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><a href="https://codex.wordpress.org/zh-cn:%E5%AE%89%E8%A3%85WordPress" data-href="https://codex.wordpress.org/zh-cn:%E5%AE%89%E8%A3%85WordPress" target="_blank" class="ne-link"><span class="ne-text" style="font-size: 16px">https://codex.wordpress.org/zh-cn:%E5%AE%89%E8%A3%85WordPress</span></a></p></details>

下载wordpress项目包

```shell
# step 1：下载项目包
wget https://cn.wordpress.org/wordpress-6.0-zh_CN.tar.gz
# step 2：解压项目包
tar -xf wordpress-6.0-zh_CN.tar.gz
# step 3：进入解压目录
cd wordpress
# step 4：拷贝样本文件，改名为wp-config.php
mv wp-config-sample.php wp-config.php
```

修改文件内容，定义wordpress连接数据库和身份认证信息，以下是需要修改的内容

```shell
//数据库名称
define( 'DB_NAME', 'wordpress' );

//连接数据库用户
define( 'DB_USER', 'root' );

//用户密码
define( 'DB_PASSWORD', 'Admin123...' );

//数据库主机(MySQL容器的宿主机IP)
define( 'DB_HOST', '192.168.0.110' );


//wordpress身份认证的信息通过该地址获取：https://api.wordpress.org/secret-key/1.1/salt/
define('AUTH_KEY',         'YEv=?HU<*71$8LtF/>s7!e9+${T]]+H0T>-v04+eJ P>)|Sb1dFCyWZ#~&#+.KSr');
define('SECURE_AUTH_KEY',  '~{V<n8|z:+o-qJ{M.PccT1^)axR;DBI`Cs./,/O/l,{KW{_7}`i^ZCm`spS4Pmh(');
define('LOGGED_IN_KEY',    '<P(HVgwFIi6*1ket[;!g&PWU5/XDhBnI{diS!LL!+ cg~)Xw!y2`%#^/%7xcq~m~');
define('NONCE_KEY',        'E-.(~xzIh//CNut*w85EQI4Oh5(|10j0!:+|142Jzx-L*cyL{k$macug+aHyH9l@');
define('AUTH_SALT',        'I$8sy|5G#qi}SM,9W/3{tR&%ZpY+#Jr0(au,jSmls+Iyh]4Sw):FsJg%_yuNGG3.');
define('SECURE_AUTH_SALT', '9JA/N7(R`5~F?xbY*!$ghLJ4}`B[^^!9rjrh|2oXKF;ce(Ll4av9ci!fXdQ/b2<s');
define('LOGGED_IN_SALT',   'b`K-=d(8(92}yHcX!R!|DtV^8D+ped&18sIJ{~P#7Uh}F<8gQKj3(ehdCs`^vwkT');
define('NONCE_SALT',       '|N7Miq3Z|HIBb4M_GRI/mtHsIPe,-qPyJLYf12K{8L5w9>l2Tx:YSimI? }0rWN[');
```

构建wordpress镜像

```dockerfile
# step 1：定义基础镜像
FROM rockylinux:9.0

# step 2：修改系统时区
COPY ./Shanghai /etc/localtime

# step 3：拷贝Nginx仓库文件
COPY ./nginx.repo /etc/yum.repos.d/

# step 4：安装Nginx与PHP（PHP版本默认8.0）
RUN dnf install -y nginx-1.20.2 php php-fpm php-mysqlnd php-gd freetype-devel libjpeg-turbo-devel && \
    dnf clean all

# step 5：拷贝wordpress站点配置文件
RUN rm -rf /etc/nginx/conf.d/default.conf
COPY ./wordpress.conf /etc/nginx/conf.d/

# step 6：通过自定义变量指定网页目录
ENV HTML_DIR=/usr/share/nginx/html/

# step 7：拷贝项目文件到Nginx网页目录
RUN rm -rf $HTML_DIR*
COPY . $HTML_DIR

# step 8：通过数据卷挂载容器数据到宿主机
VOLUME $HTML_DIR

# step 9：拷贝启动脚本 
COPY ./entrypoint.sh /

# step 10：暴露端口
EXPOSE 80

# step 11：容器启动命令
CMD /bin/sh /entrypoint.sh
```

<details class="lake-collapse"><summary id="ue759e932"><strong><span class="ne-text">wordpress.conf 文件详解：</span></strong></summary><ul class="ne-ul" style="margin: 0; padding-left: 23px"><li id="u38e6092a" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">location / {</span></code><span class="ne-text" style="color: #DF2A3F">  				#匹配所有以 </span><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text" style="color: #DF2A3F">/</span></code><span class="ne-text" style="color: #DF2A3F"> 开头的请求</span></li><li id="u246ef75c" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">root /usr/share/nginx/html;</span></code><span class="ne-text" style="color: #DF2A3F">	#指定网页根目录为 </span><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text" style="color: #DF2A3F">/usr/share/nginx/html</span></code></li><li id="u4f753711" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">index index.php index.html;</span></code><span class="ne-text" style="color: #DF2A3F">	#默认的首页文件顺序是 </span><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text" style="color: #DF2A3F">index.php</span></code><span class="ne-text" style="color: #DF2A3F">，如果不存在则使用 </span><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text" style="color: #DF2A3F">index.html</span></code></li><li id="u725f549c" data-lake-index-type="0"><span class="ne-text">}</span></li></ul><p id="u744d3ab6" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text"></span></p><ul class="ne-ul" style="margin: 0; padding-left: 23px"><li id="u5341cbf1" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">location ~ [^/]\.php(/|$) {</span></code><span class="ne-text" style="color: #DF2A3F">	#使用正则表达式匹配以 </span><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text" style="color: #DF2A3F">.php</span></code><span class="ne-text" style="color: #DF2A3F"> 结尾的文件，并允许 </span><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text" style="color: #DF2A3F">/</span></code><span class="ne-text" style="color: #DF2A3F"> 或 </span><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text" style="color: #DF2A3F">$</span></code><span class="ne-text" style="color: #DF2A3F"> 路径后缀。确保 PHP 文件的请求被处理。</span></li><li id="u72c2e06e" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_pass unix:/run/php-fpm/www.sock;</span></code><span class="ne-text" style="color: #DF2A3F">	#指定 FastCGI 服务器（PHP-FPM）的 Unix 套接字路径。</span></li><li id="u2f68db92" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_split_path_info ^(.+\.php)(/.+)$;</span></code><span class="ne-text"></span><span class="ne-text" style="color: #DF2A3F">#解析请求的 PHP 文件路径和路径信息。</span></li><li id="u84e70311" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text" style="color: #DF2A3F">fastcgi_index index.php;</span></code><span class="ne-text" style="color: #DF2A3F">	#默认的 PHP 文件为 </span><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text" style="color: #DF2A3F">index.php</span></code><span class="ne-text" style="color: #DF2A3F">。</span></li><li id="u68ac65e8" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_param</span></code><span class="ne-text"> 指令 </span><span class="ne-text" style="color: #DF2A3F">	#设置 FastCGI 相关的参数，指示 PHP-FPM 脚本的执行位置和相关信息。</span></li><li id="uc96b8a7a" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">DOCUMENT_ROOT</span></code><span class="ne-text" style="color: #DF2A3F">	#指定文档根目录。</span></li><li id="u27a1b33e" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">SCRIPT_FILENAME</span></code><span class="ne-text" style="color: #DF2A3F">	#指定 PHP 脚本的完整路径。</span></li><li id="uea781505" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">PATH_TRANSLATED</span></code><span class="ne-text">	</span><span class="ne-text" style="color: #DF2A3F">#指定路径翻译后的路径。</span></li></ul><p id="ud2f310aa" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text"></span></p><ul class="ne-ul" style="margin: 0; padding-left: 23px"><li id="u0b842daa" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">include fastcgi_params;</span></code><span class="ne-text">		</span><span class="ne-text" style="color: #DF2A3F">#包含标准的 FastCGI 参数配置。</span></li><li id="uf14858fe" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_param QUERY_STRING $query_string;</span></code><span class="ne-text">	</span><span class="ne-text" style="color: #DF2A3F">#传递查询字符串。</span></li><li id="u2fa4b0e5" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_param REQUEST_METHOD $request_method;</span></code><span class="ne-text">	</span><span class="ne-text" style="color: #DF2A3F">#传递请求方法（GET、POST 等）。</span></li><li id="u056ffc0a" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_param CONTENT_TYPE $content_type;</span></code><span class="ne-text">	</span><span class="ne-text" style="color: #DF2A3F">#传递内容类型。</span></li><li id="ub728731c" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_param CONTENT_LENGTH $content_length;</span></code><span class="ne-text" style="color: #DF2A3F"> #传递内容长度。</span></li><li id="u78855e19" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_intercept_errors on;</span></code><span class="ne-text">				</span><span class="ne-text" style="color: #DF2A3F">#开启对 FastCGI 错误的拦截。</span></li><li id="u5ec4cb23" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_ignore_client_abort off;</span></code><span class="ne-text">				</span><span class="ne-text" style="color: #DF2A3F">#是否忽略客户端中断。</span></li><li id="uc1fa4767" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_connect_timeout 60;</span></code><span class="ne-text">					</span><span class="ne-text" style="color: #DF2A3F">#FastCGI 连接超时时间。</span></li><li id="u3e76d35e" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_send_timeout 180;</span></code><span class="ne-text">					</span><span class="ne-text" style="color: #DF2A3F">#发送超时时间。</span></li><li id="ubcf8d319" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_read_timeout 180;</span></code><span class="ne-text">					</span><span class="ne-text" style="color: #DF2A3F">#读取超时时间。</span></li><li id="u0335e29b" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_buffer_size 128k;</span></code><span class="ne-text">					</span><span class="ne-text" style="color: #DF2A3F">#设置 FastCGI 缓冲区大小。</span></li><li id="u4d961ea5" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_buffers 4 256k;</span></code><span class="ne-text">						</span><span class="ne-text" style="color: #DF2A3F">#设置 FastCGI 缓冲区的数量和大小。</span></li><li id="u1c17fe11" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_busy_buffers_size 256k;</span></code><span class="ne-text">				</span><span class="ne-text" style="color: #DF2A3F">#设置繁忙缓冲区的大小。</span></li><li id="u6e7d58a3" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_temp_file_write_size 256k;</span></code><span class="ne-text">			</span><span class="ne-text" style="color: #DF2A3F">#设置临时文件的写入大小。</span></li><li id="ud85f0857" data-lake-index-type="0"><span class="ne-text">}</span></li></ul><p id="ub42e4e4d" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text"></span></p><ul class="ne-ul" style="margin: 0; padding-left: 23px"><li id="ud80a60ea" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">location /php_status {</span></code><span class="ne-text">	</span><span class="ne-text" style="color: #DF2A3F">#匹配以 </span><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text" style="color: #DF2A3F">/php_status</span></code><span class="ne-text" style="color: #DF2A3F"> 开头的请求，用于 PHP 状态页面。</span></li><li id="u898b26d6" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">root html;</span></code><span class="ne-text">	</span><span class="ne-text" style="color: #DF2A3F">#指定根目录为 </span><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text" style="color: #DF2A3F">html</span></code><span class="ne-text" style="color: #DF2A3F">（相对于 Nginx 配置文件目录）。</span></li><li id="u43244e61" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_pass unix:/run/php-fpm/www.sock;</span></code><span class="ne-text">	</span><span class="ne-text" style="color: #DF2A3F">#与之前相同，指定 FastCGI 服务器的 Unix 套接字路径。</span></li><li id="u85681457" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_index index.php;</span></code><span class="ne-text">	</span><span class="ne-text" style="color: #DF2A3F">#默认的 PHP 文件为 </span><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text" style="color: #DF2A3F">index.php</span></code><span class="ne-text" style="color: #DF2A3F">。</span></li><li id="u301d7f85" data-lake-index-type="0"><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text">fastcgi_param SCRIPT_FILENAME /scripts$fastcgi_script_name;</span></code><span class="ne-text">	</span><span class="ne-text" style="color: #DF2A3F">#指定 PHP 脚本的路径，这里将其设置为 </span><code class="ne-code" style="font-family: SFMono-Regular, Consolas, Liberation Mono, Menlo, Courier, monospace; background-color: rgba(0, 0, 0, 0.06); border: 1px solid rgba(0, 0, 0, 0.08); border-radius: 2px; padding: 0px 2px"><span class="ne-text" style="color: #DF2A3F">/scripts</span></code><span class="ne-text" style="color: #DF2A3F"> 目录。</span></li><li id="u6dab1015" data-lake-index-type="0"><span class="ne-text">}</span></li></ul></details>

<details class="lake-collapse"><summary id="u95c78b7a"><strong><span class="ne-text" style="font-size: 24px">PHP软件包作用：</span></strong></summary><p id="u270e465b" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">php					</span><span class="ne-text" style="color: #DF2A3F">//是php代码的解释器</span></p><p id="u96418c5d" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="color: rgb(51, 51, 51)">php-fpm				</span><span class="ne-text" style="color: #DF2A3F">//管理php进程接收请求</span></p><p id="uf3ed27f2" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="color: rgb(51, 51, 51)">php-mysqlnd			</span><span class="ne-text" style="color: #DF2A3F">//PHP连接MySQL的扩展，连接MySQL数据库并进行增删改查</span></p><p id="uc23a693e" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="color: rgb(51, 51, 51)">php-gd	 			</span><span class="ne-text" style="color: #DF2A3F">//php处理图片扩展，如生成图片、裁剪图片、缩放图片等</span></p><p id="u349839b8" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="color: rgb(51, 51, 51)">freetype-devel，libjpeg-turbo-devel	</span><span class="ne-text" style="color: #DF2A3F">//处理字体和辅助处理图片的软件包</span></p></details>

准备`entrypoint.sh`脚本

```shell
#!/bin/bash
#修改项目文件身份为Nginx
chown -R nginx.nginx $HTML_DIR && \
#修改PHP运行用户为Nginx（与Nginx保持一致）
sed -i 's#user = apache#user = nginx#' /etc/php-fpm.d/www.conf && \
#修改PHP运行的组为Nginx（与Nginx保持一致）
sed -i 's#group = apache#group = nginx#' /etc/php-fpm.d/www.conf && \
#创建PHP的sock目录
mkdir /run/php-fpm/ && \
chown -R nginx.nginx /run/php-fpm/ && \
#启动PHP
/usr/sbin/php-fpm && \
#启动Nginx
/usr/sbin/nginx "-g" "daemon off;"
```

拷贝`Shanghai`时区文件到dockerfile目录

```dockerfile
cp /usr/share/zoneinfo/Asia/Shanghai .
```

构建wordpress镜像（把需要拷贝到镜像中的文件提前放到当前目录）

```shell
docker build -f dockerfile -t wordpress:v6.0 .
```

基于镜像创建wordpress容器

```shell
docker run -d --name=wp -p 80:80 wordpress:v6.0
```

访问wordpress验证：http://192.168.0.31/

| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726218068182-18a4272c-d6c4-4687-9750-72583a4a4f7a.png) |
| ------------------------------------------------------------ |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726218105942-c285a5a6-1772-4444-a604-ee73bd95e902.png) |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726218127989-4ee1c447-1441-489f-a0f2-2d48b4bc60ec.png) |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726218141624-a6dc383f-f7eb-456e-9bc0-3b2a870fde82.png) |

## 镜像导出方式

```
格式：docker save -o  压缩文件名称 镜像名称:版本号
docker save -o wordpress-6.0.tar.gz wordpress:v6.0
```

## 镜像导入方式

```
格式：docker load -i  压缩文件名称
docker load -i wordpress-6.0.tar.gz
```

## 容器导出为镜像

格式：`dockre commit 容器ID 新镜像:版本`

```shell
docker commit 590b1977c5f4 mysql:wordpress
```

# 第四章：Docker网络模式

## 1. bridge网络（默认）

Docker使用Linux虚拟网络技术，在宿主机虚拟一个名为docker0的虚拟网卡，Docker在启动一个容器时，会根据docker0网卡的网段分配给容器IP地址（可通过：docker  inspect 容器名/ID 查看容器地址）

![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726219023344-7ffbf52c-73bf-46a5-a2d6-f25a0eab9c12.png)

同时docker0网卡地址作为每个容器的默认网关，同一宿主机内的容器都接入同一个docker0网卡，这样主机上的所有容器就通过docker0直接通信。

当创建一个容器的时候，同时也会创建一个名为veth开头的接口，这个接口对接容器内的eth0网卡，通过这种方式，主机可以跟容器通信。

**使用需求**：当需要多个容器在同一个docker主机上进行通信时，使用桥接模式（bridge）最佳。 



查看容器网络

```shell
docker inspect 容器名称
...
   "bridge": {   
```

## 2. Host模式

在Host类型的网络中，容器不会虚拟出自己的网卡，而是与物理机共享网络，拥有物理机的IP地址和网卡信息，而容器的其他方面，如文件系统、进程等还是和宿主机隔离的。

![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726219175472-714d25b1-96cd-4a22-b7db-d82a7a16de7b.png)

Host最大的优势就是网络性能比较好，不需要进行NAT，可以直接使用宿主机的IP:端口与外步通信。此模式下无法为容器做端口映射，容器直接占用宿主机端口。

**使用需求**：当容器需要与宿主机在同一网络，但又希望隔离容器的其他方面时，使用Host网络最佳。



创建容器并指定Host模式

格式：`docker run 选项 --net=host 镜像:版本`

```shell
docker run -d --name=ngx_host --net=host nginx:1.20.2
docker inspect ngx_host
...
   host": {  
```

## 3. Container模式

这个模式指定新创建的容器和已经存在的一个容器共享一个网络，两个容器之间通过lo网卡通信，除了网络方面，其他的如文件系统、进程等还是隔离的。

![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726219449081-f816ffa8-1276-436c-a762-93cd97053be6.png)

使用方式：`docker run 选项 --net=container:目标容器名称 镜像:版本`

## 4. None模式

使用none模式的容器仅有lo网卡，Docker并不会为容器分配其他网络，需要我们自己为容器添加网卡、配置IP等。

![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726219484417-fa6804af-544d-481c-929e-2962f2cea789.png)

使用方式：`docker run 选项 --net=none 镜像:版本`

## 自定义容器网络

自定义容器网络可使在同一个宿主机的容器使用不同的网络，避免相互影响。

查看所有docker网络：`docker network ls`

```shell
docker network ls
===========================================================
NETWORK ID     NAME        DRIVER    SCOPE
网络 ID        网络名称     网络模式   作用域
```

创建自定义bridge网络

```shell
docker network create --driver bridge --subnet 172.16.0.0/16 --gateway 172.16.0.254 mynet
```

<details class="lake-collapse"><summary id="u4dac4358"><strong><span class="ne-text" style="font-size: 22px">命令解释：</span></strong></summary><p id="u7737c665" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">docker network create         	</span><span class="ne-text" style="color: #DF2A3F">//创建网络</span></p><p id="u9d7f3b44" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">--driver  bridge     			</span><span class="ne-text" style="color: #DF2A3F">//定义网络模式为bridge</span></p><p id="u690c39b7" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">--subnet       				</span><span class="ne-text" style="color: #DF2A3F">//定义地址段</span></p><p id="u7cea64f8" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">--gateway      				</span><span class="ne-text" style="color: #DF2A3F">//定义网关</span></p><p id="ufd452487" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">mynet          				</span><span class="ne-text" style="color: #DF2A3F">//自定义网络名称</span></p></details>

查看网络详细信息：`docker  network  inspect  网络名称/ID`

```shell
docker network inspect mynet
```

创建容器使用自定义网络

```shell
docker run -d --name=ngx_mynet --net=mynet --ip 172.16.0.10 -p 81:80 nginx:1.22.0
```

<details class="lake-collapse"><summary id="u235cdd55"><strong><span class="ne-text" style="font-size: 22px">选项解释：</span></strong></summary><p id="u95bf3680" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">--net=网络名称       </span><span class="ne-text" style="color: #DF2A3F"> //指定网络名称</span></p><p id="u27ab04a2" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">--ip  IP地址          	</span><span class="ne-text" style="color: #DF2A3F">//定义容器IP，如果不自定义，将默认分配</span></p></details>

查看容器详细信息（通过详细信息查看容器网络）

```shell
docker inspect ngx_mynet
```



自定义网络和默认网络是不通的，这样就起到了隔离的作用，如果想要打通这两个网络，使用`connect`将容器加入到该网络。

现有的容器加入网络，格式：`docker network connect 网络名 容器名`

```shell
docker network connect mynet wordpress
docker inspect wordpress
#...
   "bridge": {                                      
   "mynet": {
```

提示：打通后不同网络之间的容器就可以互相通信。

断开容器以连接的网络：`docker network disconnect 网卡名 容器名`

```shell
docker network disconnect mynet wordpress
```

删除网络：`docker network rm  网络名`

```shell
docker network rm  mynet
```

提示：如果有其他容器使用该网络，需要先清理容器。

## 容器互联 --link

容器之间如果想要通过容器名称进行通信的话，可以通过`--link`来实现容器互联（单方向互联）

格式：`--link name:alias`

- name	//表示要链接的容器的名称

创建rocky01容器

```shell
docker run -id --name=rocky01 rockylinux:9.0
```

创建rocky02容器，并与rocky01互联

```shell
docker run -id --name=rocky02 --link rocky01 rockylinux:9.0
```

进入容器验证（注意：是单方向互联）

```shell
docker exec -it rocky02 /bin/bash
```

查看容器的`/etc/hosts`文件，当容器创建并启动后，会自动在hosts文件里添加解析

```shell
#...
172.17.0.3	rocky01 c64ac9f7fbb0
```

## 容器跨主机网络

docker默认的自定义网络的类型就是bridge，但是跨主机的时候bridge就明显不可用的。

**Overlay网络**

overlay网络用于连接不同机器上的docker容器，允许不同机器上的容器相互通信，同时支持对消息进行加密，在docker swarm集群环境overlay是最佳选择。

字面意思就是叠加的网络，指的就是在物理网络层上再搭建一层网络（称为逻辑网）他具有物理网络的所有特性，跟物理网络一模一样。



**Overlan环境准备**

| **主机名** | **IP地址**    |
| ---------- | ------------- |
| docker01   | 192.168.0.110 |
| docker02   | 192.168.0.111 |

**注：**每台宿主机的**主机名**必须不同。



**运行consul服务**

Overlay网络需要一个注册中心来保存每个节点信息（如：主机名、IP地址等）现在比较流行的有Consul、Nacos、ZooKeeper也都是docker支持的注册中心服务。

这三个注册中心Zookeeper没有管理界面，一般不建议使用，Nacos功能强大，但是需要更多的系统资源，Consul使用Go语言编写，本身比较轻量，方便部署，而且与Docker可无缝配合。



在docker01主机安装consul

<details class="lake-collapse"><summary id="u9fec091a"><strong><span class="ne-text">选项解释：</span></strong></summary><p id="ucfb42489" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="font-size: 16px">-server -bootstrap    	</span><span class="ne-text" style="color: #DF2A3F; font-size: 16px">//指定本机为server端</span></p><p id="u882d04bc" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="font-size: 16px">-h                    		</span><span class="ne-text" style="color: #DF2A3F; font-size: 16px">//设置容器主机名</span></p><p id="ueb3b169e" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="font-size: 16px">--restart always       	</span><span class="ne-text" style="color: #DF2A3F; font-size: 16px">//设置容器随机自启</span></p></details>

```shell
docker run -d -p 8500:8500 -h consul --name consul --restart always progrium/consul -server -bootstrap
```

修改`/usr/lib/systemd/system/docker.service`文件，将本机网络信息注册到consul中

```shell
# step 1：备份源文件
cp /usr/lib/systemd/system/docker.service{,.bak}
# step 1：修改文件内容
vim /usr/lib/systemd/system/docker.service
#注释掉原有参数
#ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock -H tcp://0.0.0.0:2376 --cluster-store=consul://192.168.0.110:8500 --cluster-advertise=192.168.0.110:2376
```

<details class="lake-collapse"><summary id="u038672c5"><strong><span class="ne-text">选项解释：</span></strong></summary><p id="u49dbc413" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">-H tcp://0.0.0.0:2376	</span><span class="ne-text" style="color: #DF2A3F">//开启Docker的2376端口</span></p><p id="u7c5dfb6d" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">--cluster-store    		</span><span class="ne-text" style="color: #DF2A3F">//指定consul地址和端口</span></p><p id="udca3c98e" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text">--cluster-advertise 		</span><span class="ne-text" style="color: #DF2A3F">//将本机IP和端口注册到consul中</span></p></details>

重启Docker

```shell
systemctl daemon-reload && systemctl restart docker
```

docker02主机下载docker-ce仓库

```shell
dnf install -y yum-utils && \
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

安装Docker

```shell
dnf install -y docker-ce-3:20.10.24-3.el9
```

修改`/usr/lib/systemd/system/docker.service`文件，将本机网络信息注册到consul中

```shell
# step 1：备份源文件
cp /usr/lib/systemd/system/docker.service{,.bak}
# step 1：修改文件内容
vim /usr/lib/systemd/system/docker.service
#注释掉原有参数
#ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock -H tcp://0.0.0.0:2376 --cluster-store=consul://192.168.0.110:8500 --cluster-advertise=192.168.0.111:2376
```

<details class="lake-collapse"><summary id="u9883a872"><strong><span class="ne-text">选项解释：</span></strong></summary><p id="ub96b042e" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="font-size: 16px">-H tcp://0.0.0.0:2376	</span><span class="ne-text" style="color: #DF2A3F; font-size: 16px">//开启Docker的2376端口</span></p><p id="u58ec6e8e" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="font-size: 16px">--cluster-store    		</span><span class="ne-text" style="color: #DF2A3F; font-size: 16px">//指定consul地址和端口</span></p><p id="uef47b865" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><span class="ne-text" style="font-size: 16px">--cluster-advertise 		</span><span class="ne-text" style="color: #DF2A3F; font-size: 16px">//将本机IP和端口注册到consul中</span></p></details>

启动Docker并设置随机自启

```shell
systemctl daemon-reload && systemctl enable docker --now
```



通过浏览器访问consul页面：[http://192.168.0.110:8500/](http://server_ip:8500/)

| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726305461589-dcc62bee-5324-49b9-b5e2-1253a1e4e3ee.png) |
| ------------------------------------------------------------ |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726305490371-d72692d6-6d6b-4038-9ad2-8472135f5768.png) |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726305533829-30f9da08-45b1-4dde-94ba-4ba56e59d787.png) |



**创建Overlan网络**

在任意一个节点创建一个网络

```shell
[root@docker01 ~]# docker network create -d overlay myovernet
[root@docker01 ~]# docker network inspect myovernet
                {
                    "Subnet": "10.0.0.0/24",
                    "Gateway": "10.0.0.1"
                }
```

**提示：**网络默认使用的10.0.0.0/24网段，如果需要自定义网络地址通过下边命令创建（别跟宿主机在同一个网络，会发生冲突）

```shell
docker network create -d overlay --subnet 172.16.1.0/24 --gateway 172.16.1.254  myovernet
```



**验证Overlan网络**

这时候在docker02节点查看也会有这个网络

```shell
[root@docker02 ~]# docker network ls
#...
60210366228b   myovernet   overlay   global
```

在docker01主机创建一个容器

```shell
docker run -d --name=nginx --net=myovernet nginx:1.20.2
```

在docker02主机创建一个容器

```shell
docker run -d --name=mysql --net=myovernet -e MYSQL_ROOT_PASSWORD=Admin123... mysql:8.0
```

docker01主机进到Nginx容器 ping docker02主机的MySQL

```shell
# step 1：进入容器
docker exec -it nginx /bin/bash
# step 2：在容器中安装ping工具
apt install iputils-ping
# step 3：ping MySQL容器（docker自定义的网落是自带域名解析的，所以直接ping容器名字即可）
[root@097d21377fcc /]# ping mysql
PING mysql (10.0.0.3) 56(84) bytes of data.
64 bytes from mysql.myovernet (10.0.0.3): icmp_seq=1 ttl=64 time=0.702 ms
64 bytes from mysql.myovernet (10.0.0.3): icmp_seq=2 ttl=64 time=1.31 ms
```



# 第五章：Docker资源限额

为防止容器过多使用主机资源，Docker提供了对容器的内存和CPU限制。

## 内存限制

格式：`-m `或 `--memory`

```shell
docker run -d --name=nginx01 -m 200m nginx:1.20.2
```

通过容器元数据过滤内存信息

```shell
docker inspect nginx01 | grep "Memory"
#...
            "Memory": 209715200,       //单位是Byte（字节）
```

后续可通过`docke update`更新资源

```shell
docker update -m 300m nginx01
docker inspect nginx01 | grep "Memory"
#...
            "Memory": 314572800,
```

## CPU限制

格式：`--cpuset-cpus  `

```shell
docker run -d --name=nginx02 --cpuset-cpus 1 nginx:1.20.2
```

通过容器元数据过滤CPU信息

```shell
docker inspect nginx02 | grep "CpusetCpus"
#...
            "CpusetCpus": "1",   
```

**提示**：后续可通过`docke update`更新资源。

# 第六章：Docker Compose容器编排

Docker官方提供容器编排工具，Docker Swarm是面向与集群的，Docker Compose是一款单机版容器编排工具，遵循YAML文件形式来创建或启动所有的容器。

项目地址：https://github.com/docker/compose

命令文档：https://docs.docker.com/reference/cli/docker/compose/

YML文件文档：https://docs.docker.com/compose/gettingstarted/



准备Docker compose程序文件

```shell
# step 1：下载docker-compose程序文件
wget https://github.com/docker/compose/releases/download/v2.22.0/docker-compose-linux-x86_64
# step 2：修改文件名称
mv docker-compose-linux-x86_64 docker-compose
# step 3：添加执行权限
chmod +x docker-compose
# step 4：移动程序文件到/usr/local/bin/目录
mv docker-compose /usr/local/bin
# step 5：查看docker-compose版本
docker-compose --version
```

## Docker Compose命令

`docker-compose` 命令说明及作用：

| 命令      | 作用                                               |
| --------- | -------------------------------------------------- |
| `-f`      | 指定文件路径，默认当前目录下`docker-compose.yml`   |
| `-p`      | 指定项目名称，默认使用当前目录名称作为项目名称     |
| `build`   | 定义构建或重建容器的镜像                           |
| `config`  | 验证`docker-compose.yml`文件语法                   |
| `cp`      | 从容器中复制文件到主机，或者从主机复制文件到容器中 |
| `create`  | 创建容器，但不启动                                 |
| `down`    | 停止并删除容器和网络（配合`--rmi`可删除卷和镜像）  |
| `images`  | 列出所有容器的镜像                                 |
| `kill`    | 强制停止运行中的容器                               |
| `logs`    | 查看容器日志                                       |
| `ls`      | 列出项目中的所有容器                               |
| `pause`   | 暂停运行中的容器                                   |
| `port`    | 显示主机和容器端口映射                             |
| `ps`      | 列出当前运行的容器                                 |
| `pull`    | 拉取镜像                                           |
| `push`    | 将本地镜像推送到镜像仓库                           |
| `restart` | 重启容器                                           |
| `rm`      | 删除停止的容器                                     |
| `run`     | 启动一个一次性任务容器                             |
| `scale`   | 调整服务的容器数量                                 |
| `start`   | 启动已创建但尚未运行的容器                         |
| `stop`    | 停止运行中的容器                                   |
| `top`     | 显示运行中的容器进程信息                           |
| `unpause` | 恢复暂停的容器                                     |
| `up`      | 创建并启动容器（结合 -d 让容器在后台运行）         |
| `version` | 显示`docker-compose`版本信息                       |
| `wait`    | 等待容器退出，并显示其退出码                       |
| `watch`   | 观察容器的变化（实时输出日志）                     |

## Docker Compose入门

通过docker compose管理Nginx容器

```shell
mkdir nginx && cd nginx
#version用于指定docker-compose文件格式版本，不同版本有不同的特性和支持的配置选项
#根据docker版本查看对应的文件格式版本，地址：https://dockerdocs.cn/compose/compose-file/compose-versioning/
version: '3.8'      
services:         #定义要运行的服务，在服务中定义容器配置
  web:            #服务名称自定义
    container_name: nginx  #服务里的容器名称（不指定会自动生成）
    image: nginx:1.20.2    #容器镜像
    expose:       #容器端口，不会直接暴露到主机上，只对服务内的其他容器可见
    - 80          
    ports:        #端口映射，"80:80" 表示将主机的80端口映射到容器的80端口
    - "80:80"
```

检测`docker-compose.yml`文件配置（不要指定文件名称）

```shell
docker-compose config
===========================================================
name: nginx
services:
  nginx-test:
    container_name: nginx
    expose:
      - "80"
    image: nginx:1.20.2
    networks:
      default: null
    ports:
      - mode: ingress
        target: 80
        published: "80"
        protocol: tcp
networks:
  default:
    name: nginx_default
```

默认会输出文件中的配置，可结合`-q`只显示错误输出，如果没有输出则表示配置正确

```shell
docker-compose config -q
```

通过`compose`创建容器

- -d ：指定容器在后台运行

```shell
docker-compose up -d
```

通过`docker-compose`查看容器信息，不是通过`compose`创建的容器不会显示

```shell
docker-compose ps
```

停止运行的容器

```shell
docker-compose stop
```

启动被停止的容器

```shell
docker-compose start
```

可通过服务名称管理服务中的容器（服务名称在`yml`文件中查看）

```shell
# step 1：停止web服务中的容器
docker-compose stop web
# step 2：启动web服务中的容器
docker-compose start web
```

**提示：**这种管理方式适合一个`compose`文件中有多个服务时，仅准对某个服务执行操作。

停止运行的容器并删除（容器和网络会被删除）

```shell
docker-compose down
```

## Docker Compose进阶

通过Docker Compose使用前边自己制作的镜像部署WordPress

<details class="lake-collapse"><summary id="u03183b73"><strong><span class="ne-text" style="font-size: 22px">参考地址：</span></strong></summary><p id="u647b05e9" class="ne-p" style="margin: 0; padding: 0; min-height: 24px"><a href="https://docs.docker.com/compose/use-secrets/" data-href="https://docs.docker.com/compose/use-secrets/" target="_blank" class="ne-link"><span class="ne-text" style="font-size: 16px">https://docs.docker.com/compose/use-secrets/</span></a></p></details>

```shell
mkdir compose-wordpress && cd mkdir compose-wordpress
version: '3.8'              #docker-compose文件格式版本
services:                   #定义运行的服务
  db:                       #服务名称
    container_name: mysql   #容器名称
    image: wp-mysql:8.0     #容器镜像
    expose:                 #容器暴露端口
    - 3306
    ports:                  #容器端口映射
    - "3306:3306"
    volumes:                #挂载volumes中定义容器数据卷
    - ./mysql_data/:/var/lib/mysql #自定义数据卷的挂载路径为当前目录:容器目录

  wordpress:                       #服务名称
    container_name: wordpress      #容器名称
    image: wordpress:v6.0          #容器镜像
    expose:                        #容器暴露端口
    - 80
    ports:                         #容器端口映射
    - "80:80"
```

以下是官方提供的`docker-compose.yml`文件部署的WordPress

```shell
version: '3'        
services:           
   db:                  #服务名称
     image: mysql:5.7    
     volumes:            
       - ./db_data:/var/lib/mysql   
     ports:              
       - "3306:3306"     
     restart: always     
     environment:        #定义一些容器内部环境变量
       MYSQL_ROOT_PASSWORD: 12345678   
       MYSQL_DATABASE: wordpress       

   wordpress:             #服务名称
     depends_on:          #容器排序，表示db容器优先
       - db
     image: wordpress
     volumes:
       - ./web_log:/var/log/nginx/
     ports:
       - "80:80"
     restart: always
     environment:        #定义一些容器内部环境变量
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: root
       WORDPRESS_DB_PASSWORD: 12345678
volumes:         
    db_data:
    web_log:
```

通过`compose`创建容器

```shell
docker-compose up -d
```



访问WordPress验证：[http://192.168.0.110/](http://server_ip/)

| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726218068182-18a4272c-d6c4-4687-9750-72583a4a4f7a.png) |
| ------------------------------------------------------------ |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726218105942-c285a5a6-1772-4444-a604-ee73bd95e902.png) |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726218127989-4ee1c447-1441-489f-a0f2-2d48b4bc60ec.png) |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726218141624-a6dc383f-f7eb-456e-9bc0-3b2a870fde82.png) |



# 第七章：Docker私有仓库

## Docker仓库概述

Docker官方的Docke rhub是一个公共镜像的仓库，我们可以从上面拉取镜像到本地，也可以把自己的镜像推送上去，但有时候我们的服务器无法访问互联网，或者不希望将自己的镜像放到公网中，那么我们可以自己搭建私有仓库来存储镜像。

## Harbor镜像仓库

Harbor是VMware公司提供的一款适用于企业内部使用的镜像仓库，提供了对用户友好的管理界面，提供了权限控制、分布式发布、强大的安全扫描与审查机制等功能。

## 安装 harbor仓库``

下载harbor离线安装包

```shell
wget https://github.com/goharbor/harbor/releases/download/v2.5.1/harbor-offline-installer-v2.5.1.tgz
```

解压harbor压缩包

```shell
tar xf harbor-offline-installer-v2.5.1.tgz
```

进入解压目录

```shell
cd harbor/ && ls
```

导入Harbro镜像

```shell
docker load -i harbor.v2.5.1.tar.gz
```

准备`harbor.yml`配置文件并修改

```shell
mv harbor.yml.tmpl harbor.yml
#上述内容省略...
hostname: 192.168.0.110   #通过主机IP访问Harbor，也可以定义域名访问
http:                     #访问方式为http（不用修改）
  port: 80                #默认端口（不用修改）

#https:                   #注释HTTPS访问方式（需要证书才可以使用）
# port: 443               #注释HTTPS端口

# certificate: /root/harbor/6864844_kubemsb.com.pem  注释证书文件
# private_key: /root/harbor/6864844_kubemsb.com.key  注释证书文件
 
harbor_admin_password: 12345    #admin密码
```



**提示：**Harbor的每个组件都是以容器的形式构建的，且需要通过docker-compose进行后期的启动、关闭等，如果在新的主机部署Harbro，需要提前安装docker环境和docker-compose程序。



执行当前路径的安装脚本

```shell
./install.sh
===========================================================
[Step 5]: starting Harbor ...
[+] Building 0.0s (0/0)   
[+] Running 10/10
 ✔ Network harbor_harbor        Created   
 ✔ Container harbor-log         Started  
 ✔ Container redis              Started 
 ✔ Container registryctl        Started 
 ✔ Container registry           Started  
 ✔ Container harbor-db          Started 
 ✔ Container harbor-core        Started 
 ✔ Container harbor-jobservice  Started 
 ✔ Container nginx              Starte
✔ ----Harbor has been installed and started successfully.---
```

查看容器信息

```shell
docker ps
===========================================================
IMAGE                                NAMES
goharbor/harbor-jobservice:v2.5.1    harbor-jobservice
goharbor/nginx-photon:v2.5.1         nginx 
goharbor/harbor-core:v2.5.1          harbor-core
goharbor/harbor-registryctl:v2.5.1   registryctl
goharbor/registry-photon:v2.5.1      registry
goharbor/redis-photon:v2.5.1         redis
goharbor/harbor-portal:v2.5.1        harbor-portal
goharbor/harbor-db:v2.5.1            harbor-db
goharbor/harbor-log:v2.5.1           harbor-log
```



 **Harbor 组件介绍：**

| 组件                  | 作用                                                         |
| --------------------- | ------------------------------------------------------------ |
| **harbor-jobservice** | 处理 Harbor 内部的定时任务和后台作业。包括镜像扫描、清理作业、同步操作等。 |
| **nginx**             | 作为反向代理服务器，处理进入 Harbor 的所有 HTTP 请求。它可以提供负载均衡、SSL 终端、缓存等功能。 |
| **harbor-core**       | Harbor 的核心组件，负责处理主要的业务逻辑，包括用户管理、项目管理、权限控制等。 |
| **registryctl**       | 管理 Harbor 内部的 Docker Registry 服务。它负责创建、删除和管理 Docker Registry 实例。 |
| **registry**          | 实际的 Docker 镜像存储和管理服务。Registry 存储和管理所有上传到 Harbor 的 Docker 镜像及其层。 |
| **redis**             | 用于缓存和存储 Harbor 的临时数据和会话信息。Redis 是一个高性能的内存数据库，通常用作缓存系统。 |
| **harbor-portal**     | Harbor 的前端用户界面，提供用户交互和管理功能。用户可以通过它访问镜像库、管理项目、查看镜像详情等。 |
| **harbor-db**         | Harbor 使用的数据库，通常是 PostgreSQL。它存储所有的元数据、用户信息、项目配置、镜像标签等。 |
| **harbor-log**        | 处理和存储 Harbor 组件的日志。帮助监控和排查系统中的问题。它可以集中管理日志数据并支持日志分析和审计。 |



登录Harbor页面，并创建一个镜像仓库：http://192.168.0.110

| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726565834661-ae81a9f2-5317-4fd9-9751-6469357f5cd4.png) |
| ------------------------------------------------------------ |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726565791261-d6c84cff-f242-4e4e-b1f1-636ab081cbc3.png) |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726565934768-5f64129c-f36d-45b1-9cba-24a3cd39a3be.png) |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726566035965-493ca74e-022e-4201-b683-0436c9bca57b.png) |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726566076426-fcaa1285-fc35-48d7-a86f-bcef420bf9f1.png) |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726566161072-e4d31d05-26ce-4c8a-9ccf-8cc476a9061e.png) |



3.通过命令行登录到Harbor，否则无法上传镜像

4.修改镜像标签后，上传镜像到Harbor仓库中

5.下载镜像验证仓库

## Harbor使用流程

1. 修改`/etc/docker/daemon.json`文件，指定Harbor地址

```shell
{
       "registry-mirrors": ["https://docker.rainbond.cc"],
       "insecure-registries": ["192.168.0.110"]
}
```

重启docker

```shell
systemctl restart docker
```

重新启动Harbor服务（默认它不会跟着docker重启）

```shell
docker-compose stop && docker-compose start
```

1. 修改镜像标签

```shell
docker tag wordpress:v6.0 192.168.0.110/wordpress/wordpress:v6.0
```

1. 通过命令行登录到Harbor（需要用户认证）

```shell
docker login 192.168.0.110
===========================================================
Username: admin  #用户名
Password: 12345  #密码
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credential-stores

Login Succeeded
```

1. 上传镜像到仓库

```shell
docker push 192.168.0.110/wordpress/wordpress:v6.0
```

1. 在仓库查看镜像

| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726567095500-6ab87f5d-86b7-4b57-a53a-568fbdb067ce.png) |
| ------------------------------------------------------------ |
| ![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726567198986-7f812806-8c86-4589-824d-bf977e370b9d.png) |



**在docker02主机拉取镜像**

1. 需要先修改`/etc/docker/daemon.json`文件，指定Harbor地址

```shell
{
       "registry-mirrors": ["https://docker.rainbond.cc"],
       "insecure-registries": ["192.168.0.110"]
}
```

1. 重启docker

```shell
systemctl restart docker
```

1. 拉取镜像（拉取镜像时不需要用户名/密码验证）

```shell
docker pull 192.168.0.110/wordpress/wordpress:v6.0
```



# 第八章：容器运行时Containerd

![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726567673946-bfa311fb-d00a-46aa-8a25-6c21350c8ca4.png)

## Containerd介绍

Containerd 最初是 Docker 的一个核心组件，负责底层容器管理，例如：创建容器、启动容器、停止容器、删除容器等；2016年Docker把containerd 捐给了CNCF管理。



| **主机名** | **IP地址**    |
| ---------- | ------------- |
| containerd | 192.168.0.111 |

## Containerd安装方式

下载阿里云docker-ce仓库安装containerd软件包

```shell
dnf install -y yum-utils
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

安装containerd

```shell
yum install containerd.io-1.6.20-3.1.el9.x86_64 -y
```

生成containerd配置文件

```shell
containerd config default | tee /etc/containerd/config.toml
```

## Containerd配置加速器

修改配置文件`/etc/containerd/config.toml`，找到`[plugins."io.containerd.grpc.v1.cri".registry]`，在`config_path`中定义一个目录， 目录可用于存放私有仓库证书和其他认证文件 

```shell
    [plugins."io.containerd.grpc.v1.cri".registry]
      config_path = "/etc/containerd/certs.d"
```

在该目录下创建存放仓库认证文件的目录

```shell
mkdir -p /etc/containerd/certs.d/docker.io
```

在该目录下创建一个`hosts.toml`文件，在文件中定义镜像加速器

```shell
[host."https://docker.1ms.run"]
  capabilities = ["pull","resolve","push"]
  skip_verify = true
```

启动containerd

```shell
systemctl enable containerd --now
```

下载nerdctl工具（Containerd容器及镜像管理工具）

```shell
wget https://github.com/containerd/nerdctl/releases/download/v1.5.0/nerdctl-1.5.0-linux-amd64.tar.gz
```

解压到`/usr/local/bin`目录

```shell
tar xf nerdctl-1.5.0-linux-amd64.tar.gz -C /usr/local/bin/
```

查看nerdctl版本

```shell
nerdctl -v
```

## Containerd管理镜像

单机版containerd可使用`crt和nerdctl`命令管理容器和镜像；k8s中containerd可使用k8s提供的`crictl`命令管理容器镜像。



**nerdctl镜像管理常用命令：**

| **命令**           | **描述**         |
| ------------------ | ---------------- |
| `nerdctl images`   | 查看镜像         |
| `nerdctl pull`     | 拉取镜像         |
| `nerdctl rmi `     | 删除镜像         |
| `nerdctl inspect ` | 查看镜像元数据   |
| `nerdctl tag `     | 修改镜像标签     |
| `nerdctl save `    | 保存镜像为压缩包 |
| `nerdctl load`     | 导入镜像         |
| `nerdctl push `    | 上传镜像         |



拉取nginx镜像

```shell
nerdctl pull nginx:1.27.0
```

查看镜像信息

```shell
nerdctl images
===========================================================
REPOSITORY    TAG       IMAGE ID        CREATED          PLATFORM       SIZE         BLOB SIZE
nginx         1.27.0    98f8ec75657d    3 minutes ago    linux/amd64    189.9 MiB    67.7 MiB
```

查看镜像元数据信息

```shell
nerdctl inspect nginx:1.27.0
```

修改镜像标签

```shell
nerdctl tag nginx:1.27.0 nginx-test:1.27.0
```

保存镜像为压缩包

```shell
nerdctl save -o nginx1.27.0-img.tar nginx:1.27.0
```

删除镜像

```shell
nerdctl rmi nginx:1.27.0
```

导入镜像

```shell
nerdctl load -i nginx1.27.0-img.tar
```



## Containerd管理容器

containerd自身并不具备为容器提供网络的能力，默认Containerd创建的容器仅有lo网络，如果容器需要配置网络，需要结合CNI（Container Network Interface）插件为容器配置网络。或者也可以让容器共享主机的网络。

CNI仓库地址：https://github.com/containernetworking/plugins



下载CNI插件压缩包

```shell
wget https://github.com/containernetworking/plugins/releases/download/v1.5.1/cni-plugins-linux-amd64-v1.5.1.tgz
```

解压到`/opt/cni/bin`目录，该目录再`config.toml`文件中以定义好

```shell
mkdir -p /opt/cni/bin
tar -xf cni-plugins-linux-amd64-v1.5.1.tgz -C /opt/cni/bin/
```



**nerdctl容器管理常用命令：**

| **命令**              | **作用**                       |
| --------------------- | ------------------------------ |
| `nerdctl ps`          | 显示创建的容器（支持`-a`选项） |
| `nerdctl run 选项...` | 创建容器                       |
| `nerdctl start`       | 启动容器                       |
| `nerdctl stop`        | 停止容器                       |
| `nerdctl restart`     | 重启容器                       |
| ` nerdctl exec -it `  | 进入容器                       |
| `nerdctl rm `         | 删除容器（支持`-f`选项）       |
| `nerdctl logs`        | 查看容器日志                   |



创建Nginx容器，并挂载数据卷

```shell
# step 1：提前创建数据卷目录，containerd无法自动创建目录
mkdir -p /volume-nginx/logs
# step 2：创建容器时不支持-id结合使用
nerdctl run -d --name=nginx -p 80:80 \
-v /volume-nginx/logs:/var/log/nginx \
nginx:1.27.0
```

查看容器信息

```shell
nerdctl ps
===========================================================
CONTAINER ID    IMAGE                             COMMAND                   CREATED          STATUS    PORTS                 NAMES
8f59c49246e0    docker.io/library/nginx:1.27.0    "/docker-entrypoint.…"    9 seconds ago    Up        0.0.0.0:80->80/tcp    nginx
```

查看容器日志

```shell
nerdctl logs nginx
```



访问容器：http://192.168.0.111/

![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1726669790188-862d0880-a064-45f6-8700-3d686daac9b6.png)



进入容器

```shell
nerdctl exec -it nginx /bin/bash
```

停止容器

```shell
nerdctl stop nginx
```

删除容器

```shell
nerdctl rm nginx
```



## Containerd名称空间管理

名称空间（namespace）用于在同一个主机中将容器、镜像等进行隔离，防止发生冲突。可以为每个项目或应用程序创建一个单独的名称空间，避免管理混乱。

**containerd名称空间管理命令：**

| 命令                         | 描述             |
| ---------------------------- | ---------------- |
| `nerdctl namespaces ls`      | 列出所有工作空间 |
| `nerdctl namespaces create ` | 创建新工作空间   |
| `nerdctl namespaces remove ` | 删除指定工作空间 |

查看命名空间

```shell
nerdctl ns ls

NAME       CONTAINERS    IMAGES    VOLUMES    LABELS
default    1             2         0 
```

创建命名空间方式

```shell
nerdctl ns create test
```

如需要在指定的名称空间管理创建容器、拉取镜像等需要使用 `--namespace`（或 `-n`）选项指定命名空间

1. 拉取镜像到test空间

```shell
nerdctl -n test pull tomcat:8.5-jre10-slim
```

1. 查看test空间镜像

```shell
nerdctl -n test images
===========================================================
REPOSITORY    TAG               IMAGE ID        CREATED               PLATFORM       SIZE         BLOB SIZE
tomcat        8.5-jre10-slim    15d530cd2186    About a minute ago    linux/amd64    312.6 MiB    111.4 MiB
```

1. 再test空间创建tomcat容器

```shell
nerdctl -n test run -d --name=tomcat -p 8080:8080 tomcat:8.5-jre10-slim
```

1. 查看test空间容器信息

```shell
nerdctl -n test ps
===========================================================
CONTAINER ID    IMAGE                                      COMMAND              CREATED          STATUS    PORTS                     NAMES
da836df4aa5d    docker.io/library/tomcat:8.5-jre10-slim    "catalina.sh run"    9 seconds ago    Up        0.0.0.0:8080->8080/tcp    tomcat
```

1. 删除tomcat容器

```shell
nerdctl -n test rm -f tomcat
```

1. 删除tomcat镜像

```shell
nerdctl -n test rmi tomcat:8.5-jre10-slim
```



删除命名空间方式（如果有容器或镜像等，无法删除，需要提前清理）

```shell
nerdctl ns rm test
```



## Containerd使用Harbor仓库

修改配置文件`/etc/containerd/config.toml`，找到`[plugins."io.containerd.grpc.v1.cri".registry]`，在`config_path`中定义一个目录， 目录可用于存放 SSL/TLS 证书（私有仓库证书）和其他认证文件  

```shell
    [plugins."io.containerd.grpc.v1.cri".registry]
      config_path = "/etc/containerd/certs.d"
```

创建目录，该目录是Harbor主机IP

```shell
mkdir -p /etc/containerd/certs.d/192.168.0.110/
```

在该目录下创建`hosts.toml`文件，在文件中定义镜像仓库地址

```shell
[host."http://192.168.0.110"]
  capabilities = ["pull","resolve","push"]
  skip_verify = true
```

重启Containerd生效

```shell
systemctl restart containerd
```

## buildkitd构建镜像

buildkit 从Docker公司的开源的镜像构建工具包，支持OCI（Open Container Initiative））镜像构建的一种规范。

buildkit地址: https://github.com/moby/buildkit

**buildkitd组成部分：**

buildkitd（服务端）目前支持runc和containerd作为镜像构建环境，默认是runc（容器管理工具），可以更换containerd。

buildctl（客户端）负责解析Dockerfile文件、并向服务端buildkitd发出构建请求。

![img](https://cdn.nlark.com/yuque/0/2024/png/44499768/1731565440619-c7bf7333-6b97-4313-93af-8a39d46aec2c.png)

下载buildkit

```shell
wget https://github.com/moby/buildkit/releases/download/v0.17.1/buildkit-v0.17.1.linux-amd64.tar.gz
```

安装buildkit

```shell
tar -xf buildkit-v0.17.1.linux-amd64.tar.gz
mv bin/* /usr/local/bin 
```

准备buildkit的socket文件

```shell
[Unit]
Description=buildkitd  # 描述服务的名称
Documentation=https://github.com/moby/buildkit  # 提供服务的文档链接

[Socket]
ListenStream=%t/buildkit/buildkit.sock  # 指定监听的套接字路径,%t通常会被替换为/run目录
SocketMode=0660  # 设置套接字的权限模式

[Install]
WantedBy=sockets.target  # 指定服务的目标，表示该服务在套接字目标下启用
```

准备buildkit的systemd服务文件

```shell
[Unit]
Description=buildkitd  # 描述服务的名称
Documentation=https://github.com/moby/buildkit  # 提供服务的文档链接
Requires=buildkit.socket  # 指定服务依赖的套接字
After=buildkit.socket  # 指定服务启动顺序

[Service]
ExecStart=/usr/local/bin/buildkitd --oci-worker=false --containerd-worker=true  # 指定服务启动的命令和参数，--oci-worker=false 表示禁用runc环境构建镜像，--containerd-worker=true 表示启用Containerd环境构建镜像

[Install]
WantedBy=multi-user.target  # 指定服务的目标运行级别
```

buildkitd指定私有镜像仓库地址及镜像加速器

```shell
mkdir -p /etc/buildkit
[registry]
  # 私有仓库地址
  [registry."192.168.0.110"]
  http = true  # 使用HTTP协议
  insecure = true  # 允许不安全的连接

  # 配置Docker官方镜像仓库地址
  [registry."docker.io"]
  mirrors = ["https://docker.1ms.run"]  # 镜像加速器地址
```

启动buildkit

```shell
systemctl daemon-reload
systemctl enable buildkit --now
systemctl status buildkit
```

通过nerdctl使用buildkit制作镜像，文件名称默认: Dockerfile

```dockerfile
FROM nginx:1.27.0
COPY ./Shanghai /etc/localtime
nerdctl build -t 192.168.0.110/nginx/nginx:v1 .
```



**总结：**Containerd虽然已经实现了大多数容器的管理功能，但在实际生产环境中，Containerd很少单独出现(功能有限、配置繁琐)，其设计目的是为了嵌入到k8s中使用，k8s在底层使用containerd实现容器的管理。