# Docker命令与Ctr命令对照

### 容器管理相关命令

| 功能        | Docker 命令                               | `ctr` 命令                                                |
| --------- | --------------------------------------- | ------------------------------------------------------- |
| 拉取镜像      | `docker pull <image>`                   | `ctr image pull <image>`                                |
| 查看镜像列表    | `docker images`                         | `ctr image list`                                        |
| 删除镜像      | `docker rmi <image>`                    | `ctr image remove <image>`                              |
| 启动容器      | `docker run <image>`                    | `ctr container create --tty <image>`                    |
| 列出正在运行的容器 | `docker ps`                             | `ctr task list`                                         |
| 列出所有容器    | `docker ps -a`                          | `ctr container list`                                    |
| 停止容器      | `docker stop <container>`               | `ctr task kill <task-id>`                               |
| 删除容器      | `docker rm <container>`                 | `ctr container delete <container>`                      |
| 重启容器      | `docker restart <container>`            | 需要先停止并重新启动，分别用 `kill` 和 `create`                        |
| 进入容器      | `docker exec -it <container> /bin/bash` | `ctr task exec --exec-id <id> -t <container> /bin/bash` |
| 显示容器日志    | `docker logs <container>`               | `ctr task logs <container>`                             |

### 镜像相关命令

| 功能     | Docker 命令                       | `ctr` 命令                          |
| ------ | ------------------------------- | --------------------------------- |
| 推送镜像   | `docker push <image>`           | `ctr image push <image>`          |
| 保存镜像   | `docker save -o <file> <image>` | `ctr image export <file> <image>` |
| 加载镜像   | `docker load -i <file>`         | `ctr image import <file>`         |
| 查看镜像详情 | `docker inspect <image>`        | `ctr image info <image>`          |

### 容器任务管理相关命令

| 功能        | Docker 命令                  | `ctr` 命令                    |
| --------- | -------------------------- | --------------------------- |
| 启动已存在的容器  | `docker start <container>` | `ctr task start <task-id>`  |
| 停止正在运行的容器 | `docker stop <container>`  | `ctr task kill <task-id>`   |
| 删除任务      | `docker rm <container>`    | `ctr task delete <task-id>` |

### 网络和卷管理相关命令

| 功能   | Docker 命令                         | `ctr` 命令                         |
| ---- | --------------------------------- | -------------------------------- |
| 查看网络 | `docker network ls`               | `ctr network list`（需要插件支持）       |
| 创建网络 | `docker network create <network>` | 需要插件支持                           |
| 删除网络 | `docker network rm <network>`     | 需要插件支持                           |
| 查看卷  | `docker volume ls`                | `ctr snapshot list`              |
| 创建卷  | `docker volume create <volume>`   | `ctr snapshot create <snapshot>` |
| 删除卷  | `docker volume rm <volume>`       | `ctr snapshot remove <snapshot>` |

### 其他相关命令

| 功能          | Docker 命令                             | `ctr` 命令                                                    |
| ----------- | ------------------------------------- | ----------------------------------------------------------- |
| 查看容器或镜像详细信息 | `docker inspect <container or image>` | `ctr container info <container>` 或 `ctr image info <image>` |
| 查看系统信息      | `docker info`                         | `ctr version`                                               |

请注意，`ctr` 是 Containerd 的命令行工具，它更接近底层操作，与 Docker CLI 相比，使用起来稍显复杂。同时，某些 Docker 提供的功能（如高级网络配置）在 `ctr` 中可能无法直接实现，需要通过其他插件或工具支持。
