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

```shell
wget https://download.docker.com/linux/static/stable/x86_64/docker-24.0.4.tgz
tar xf docker-24.0.4.tgz
cp docker/* /usr/bin/
dockerd & 
```

# 3、Docker使用生态

![image.png|650](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20240515160029.png)


## 3.1 Docker Host

用于安装Docker daemon的主机，即为Docker Host，并且该主机中可基于容器镜像运行容器。

## 3.2 Docker daemon

用于管理Docker Host中运行的容器、容器镜像、容器网络等，管理由Containerd.io提供的容器。

## 3.3 Registry

容器镜像仓库，用于存储已生成容器运行模板的仓库，用户使用时，可直接从容器镜像仓库中下载容器镜像，即容器运行模板，就可以运行容器镜像中包含的应用了。例如：DockerHub,也可以使用Harbor实现企业私有的容器镜像仓库。

## 3.4 Docker client

Docker Daemon客户端工具，用于同Docker Daemon进行通信，执行用户指令，可部署在Docker Host上，也可以部署在其它主机，能够连接到Docker Daemon即可操作。

## 3.5 Image

把应用运行环境及计算资源打包方式生成可再用于启动容器的不可变的基础设施的模板文件，主要用于基于其启动一个容器。

## 3.6 Container

由容器镜像生成，用于应用程序运行的环境，包含容器镜像中所有文件及用户后添加的文件，属于基于容器镜像生成的可读写层，这也是应用程序活跃的空间。

## 3.7 Docker Desktop

> 仅限于MAC、Windows、部分Linux操作系统上安装使用。

Docker Desktop 提供了一个简单的界面，使您能够直接从您的机器管理您的容器、应用程序和映像，而无需使用 CLI 来执行核心操作。


# 4、Docker命令

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20240515161620.png)

## 4.1 docker run


```
docker run -it --name c1 centos:latest bash
```

>命令解释
 docker run 运行一个命令在容器中，命令是主体，没有命令容器就会消亡
 -i 交互式
 -t 提供终端
 --name c1 把将运行的容器命名为c1
 centos:latest 使用centos最新版本容器镜像
 bash 在容器中执行的命令(也可以写成/bin/bash)

>操作说明
 在上述提示符处按住ctrl键，再按p键与q键，可以退出交互式的容器，容器会处于运行状态。

## 4.2、docker ps

```shell
docker ps 查看正在运行的容器
docker ps --all 可以查看正在运行的和停止运行的容器
```

## 4.3、docker inspect

以json格式得到 docker 镜像/容器的元数据

```shell
docker inspect [容器id]
```

## 4.4、docker exec

```shell
docker exec -it c2 ls /root
```

>命令解释
 docker exec 在容器外实现与容器交互执行某命令
 -it 交互式
 c2 正在运行的容器名称
 ls /root 在正在运行的容器中运行相关的命令

## 4.5、docker attach

```shell
docker attach c2
```

>命令解释
 docker attach 类似于ssh命令，可以进入到容器中
 c2 正在运行的容器名称

>说明
 docker attach 退出容器时，如不需要容器再运行，可直接使用exit退出；如需要容器继续运行，可使用ctrl+p+q


## 4.6、docker stop 

```shell
docker stop 9f2eea # 停止容器
docker stop $(docker ps -a -q) # 批量停止容器
```

## 4.7、docker top

在Docker Host查看容器中运行的进程信息

```shell
docker top c2
```

| UID  | PID   | PPID  | C    | STIME | TTY   | TIME     | CMD  |
| ---- | ----- | ----- | ---- | ----- | ----- | -------- | ---- |
| root | 69040 | 69020 | 0    | 18:37 | pts/0 | 00:00:00 | bash |

>命令解释
 docker top 查看container内进程信息，指在docker host上查看，与docker exec -it c2 ps -ef不同。

>输出说明
 UID 容器中运行的命令用户ID
 PID 容器中运行的命令PID
 PPID 容器中运行的命令父PID，由于PPID是一个容器，此可指为容器在Docker Host中进程ID
 C     占用CPU百分比
 STIME 启动时间
 TTY   运行所在的终端
 TIME  运行时间
 CMD   执行的命令

## 3.8、docker rm

如果容器已停止，使用此命令可以直接删除；如果容器处于运行状态，则需要提前关闭容器后，再删除容器

```shell
# 单个删除
docker rm c2
# 批量删除
docker rm $(docker ps -a -q)
```

# 5、Docker容器镜像


## 5.1、Docker容器镜像操作

```shell
# 查看本地容器镜像
docker images
docker image list
# 查看docker容器镜像本地存储位置
ls /var/lib/docker
# 搜索docker hub容器镜像
docker search centos
# 下载镜像
docker pull centos
# 删除镜像
docker rmi centos:7
```

## 5.2、Docker容器镜像介绍

docker镜像为只读的容器模板，是docker容器的基础。为docker容器提供了静态文件系统运行环境（rootfs），是容器的静止状态，容器时镜像的运行状态。



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















