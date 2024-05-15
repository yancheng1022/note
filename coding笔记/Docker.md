# 1、概念
## 1.1、基本概念

Docker本身是一个容器运行载体或称之为管理引擎。我们把引用程序和配置依赖打包好形成一个可交付的运行环境，这个打包好的运行环境就是image镜像文件。只有通过这个镜像文件才能生成Docker容器实例

>比较Docker和传统虚拟化方式的不同之处：
 1.传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需要应用进程；
 2.容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便；
 3.每个容器之间互相隔离，每个容器有自己的文件系统，容器之间进程不会相互影响，能区分计算资源
 
## 1.2、Docker组成
![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20240515145403.png)

们发现在安装docker时，不仅会安装docker engine与docker cli工具，而且还会安装containerd，这是为什么呢？

Docker最初是一个单体引擎，主要负责容器镜像的制作、上传、拉取及容器的运行及管理。随着容器技术的繁荣发展，为了促进容器技术相关的规范生成和Docker自身项目的发展，Docker将单体引擎拆分为三部分，分别为runC、containerd和dockerd

其中：

- runC主要负责容器的运行和生命周期的管理（即低层运行时）
- containerd主要负责容器镜像的下载和解压等镜像管理功能（即高层运行时）
- dockerd主要负责提供镜像制作、上传等功能同时提供容器存储和网络的映射功能，同时也是Docker服务器端的守护进程，用来响应Docker客户端（命令行CLI工具）发来的各种容器、镜像管理的任务。

Docker公司将runC捐献给了OCI，将containerd捐献给了CNCF，剩下的dockerd作为Docker运行时由Docker公司自己维护。


# 2、Docker的安装

> 关于Docker版本：
> Docker-ce Docker社区版，主要用于个人开发者测试使用，免费版本
> Docker-ee Docker企业版，主要用于为企业开发及应用部署使用，收费版本，免费试用一个月，2020年因国际政治原因曾一度限制中国企业使用。

## 2.1、使用yum源安装

>使用阿里云开源软件镜像站。

``` shell
wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
yum -y install docker-ce
systemctl enable --now docker
```

## 2.2、使用二进制安装


# 2、Docker安装
## 2.1、仓库设置

1、安装所需的软件包。yum-utils 提供了 yum-config-manager ，并且 device mapper 存储驱动程序需要 device-mapper-persistent-data 和 lvm2

```shell
  yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

2、使用以下命令来设置稳定的仓库（官方download.docker.com较慢）

```shell
yum-config-manager \  
    --add-repo \  
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

## 2.2、安装 Docker Engine-Community

```shell
# 安装最新版本
yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
# 安装指定版本
yum list docker-ce --showduplicates | sort -r
yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```

## 2.3、启动Docker

```shell
systemctl start docker
```

## 2.4、验证

```shell
docker run hello-world
```

# 3、容器的使用

## 3.1、获取镜像

```shell
docker pull mysql
```

## 3.2、启动容器

```shell
# 通过镜像启动，后台运行
docker run -itd --name ubuntu-test ubuntu /bin/bash
# 启动停止运行的容器
docker start 容器ID
```

参数说明：

- **-i**: 交互式操作。
- **-t**: 终端。
- **-d**: 在大部分的场景下，我们希望 docker 的服务是在后台运行的，我们可以过 -d 指定容器后台运行
- **--name**：指定容器名字
- **ubuntu**: ubuntu 镜像。
- **/bin/bash**：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 /bin/bash

要退出终端，直接输入 **exit**:

## 3.3、停止、重启容器

```shell
docker stop <容器 ID>
docker restart <容器 ID>
```

## 3.4、进入容器

在使用 -d 参数时，容器启动后会进入后台。此时想要进入容器，可以通过以下指令进入：
docker attach ：退出时会导致容器停止，不推荐
docker exec：推荐使用 docker exec 命令，退出不会导致容器的停止

```shell
docker exec -it 243c32535da7 /bin/bash
```

## 3.5、导入、导出容器

```shell
# 导出容器
docker export 1e560fca3906 > ubuntu.tar
# 导入容器快照
cat docker/ubuntu.tar | docker import - test/ubuntu:v1
```

## 3.6、删除容器

删除容器使用 **docker rm** 命令

```shell
docker rm -f 1e560fca3906
```

# 4、镜像的使用


容器运行时就是运行和管理容器进程、镜像的工具















