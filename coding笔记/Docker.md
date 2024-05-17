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

安装环境：CentOS 7.3+

1、如果之前安装了旧版docker，请先删除。

```shell
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

2、安装仓库
```shell
sudo yum install -y yum-utils

sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

3、安装docker engine

```shell
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

4、启动docker，运行hello world，查看是否成功

```shell
sudo systemctl start docker
sudo docker run hello-world
```

5、配置国内镜像仓库地址
新建/etc/docker/daemon.json文件，输入如下内容：

```json
{
  "registry-mirrors": [
    "https://registry.docker-cn.com",
    "http://hub-mirror.c.163.com",
    "https://fsp2sfpr.mirror.aliyuncs.com/"
  ]
}
```

6、然后重启，配置开机启动
```shell
systemctl restart docker
systemctl enable docker
systemctl enable containerd
```

# 3、docker run开箱即用

## 3.1、docker架构

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20240517143534.png)

**registry 镜像仓库**

registry可以理解为镜像仓库，用于保存docker image。

Docker Hub 是docker官方的镜像仓库，docker命令默认从docker hub中拉取镜像。我们也可以搭建自己的镜像仓库。

**image 镜像**

image可以理解为一个只读的应用模板。image包含了应用程序及其所需要的依赖环境，例如可执行文件、环境变量、初始化脚本、启动命令等。

**container 容器**

容器是image的一个运行实例。当我们运行一个image，就创建了一个容器。


## 3.2、docker pull拉取镜像

从镜像仓库拉取镜像到本地

```shell
docker pull nginx 不写默认是latest
docker pull nginx:latest
docker pull nginx:1.22
docker pull nginx:1.22.0-alpine
```

一般不建议使用latest，因为最新的镜像是滚动更新的，过一段时间，可能跟你本地的不是同一个。
使用docker images命令查看本地镜像

## 3.3、docker run命令

```shell
docker run [可选参数] 镜像名:版本 []

docker run --name some-nginx -d -p 8080:80 nginx:1.22
默认情况下，容器无法通过外部网络访问。
需要使用-p参数将容器的端口映射到宿主机端口，才可以通过宿主机IP进行访问。
浏览器打开 http://192.168.56.106:8080

```

# 3、Docker使用生态



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

### 5.2.1、联合文件系统
联合文件系统是实现联合挂载技术的文件系统，联合挂载技术可以实现在一个挂载点同时挂载多个文件系统，将挂载点的原目录与被挂载内容进行整合，使得最终的文件系统包含整合后的各层文件和目录

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20240517110423.png)

## 5.3、Docker容器镜像操作命令



### 8.3.1  docker commit

docker 通过 commit 和 build 操作实现镜像的构建。commit 将容器提交为一个镜像，build 在一个镜像的基础上构建镜像。


~~~powershell
# docker commit 355e99982248
sha256:8965dcf23201ed42d4904e2f10854d301ad93b34bea73f384440692e006943de
~~~





~~~powershell
# docker images
REPOSITORY   TAG       IMAGE ID       CREATED              SIZE
<none>       <none>    8965dcf23201   About a minute ago   231MB
~~~





image 短 ID 8965dcf23201 即为容器提交的镜像，查看镜像的 imagedb 元数据：



~~~powershell
# cat  /var/lib/docker/image/overlay2/imagedb/content/sha256/8965dcf23201ed42d4904e2f10854d301ad93b34bea73f384440692e006943de
......
"os":"linux","rootfs":{"type":"layers","diff_ids":["sha256:74ddd0ec08fa43d09f32636ba91a0a3053b02cb4627c35051aff89f853606b59","sha256:551c3089b186b4027e949910981ff1ba54114610f2aab9359d28694c18b0203b"]}}
~~~



可以看到镜像层自上而下的前1个镜像层 diff_id 和 centos 镜像层 diff_id 是一样的，说明每层镜像层可以被多个镜像共享。而多出来的一层镜像层内容即是上节我们写入文件的内容：



~~~powershell
# echo -n "sha256:74ddd0ec08fa43d09f32636ba91a0a3053b02cb4627c35051aff89f853606b59 sha256:551c3089b186b4027e949910981ff1ba54114610f2aab9359d28694c18b0203b" | sha256sum -
92f7208b1cc0b5cc8fe214a4b0178aa4962b58af8ec535ee7211f335b1e0ed3b  -
~~~



~~~powershell
# cd /var/lib/docker/image/overlay2/layerdb/sha256/92f7208b1cc0b5cc8fe214a4b0178aa4962b58af8ec535ee7211f335b1e0ed3b
[root@192 92f7208b1cc0b5cc8fe214a4b0178aa4962b58af8ec535ee7211f335b1e0ed3b]# ls
cache-id  diff  parent  size  tar-split.json.gz


[root@192 92f7208b1cc0b5cc8fe214a4b0178aa4962b58af8ec535ee7211f335b1e0ed3b]# cat cache-id
250dc0b4f2c5f27952241a55cd4c286bfaaf8af4b77c9d0a38976df4c147cb95


[root@192 92f7208b1cc0b5cc8fe214a4b0178aa4962b58af8ec535ee7211f335b1e0ed3b]# ls /var/lib/docker/overlay2/250dc0b4f2c5f27952241a55cd4c286bfaaf8af4b77c9d0a38976df4c147cb95
diff  link  lower  work


[root@192 92f7208b1cc0b5cc8fe214a4b0178aa4962b58af8ec535ee7211f335b1e0ed3b]# ls /var/lib/docker/overlay2/250dc0b4f2c5f27952241a55cd4c286bfaaf8af4b77c9d0a38976df4c147cb95/diff
msb.txt

~~~



### 8.3.2 docker save

> 导出容器镜像，方便分享。



~~~powershell
# docker save -o centos.tar centos:latest  
~~~



~~~powershell
# ls

centos.tar  
~~~



### 8.3.3 docker load

> 把他人分享的容器镜像导入到本地，这通常是容器镜像分发方式之一。



~~~powershell
# docker load -i centos.tar
~~~



### 8.3.4 docker export

> 把正在运行的容器导出



~~~powershell
# docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED       STATUS       PORTS     NAMES
355e99982248   centos:latest   "bash"                   7 hours ago   Up 7 hours             fervent_perlman
~~~



~~~powershell
# docker export -o centos7.tar 355e99982248
~~~



~~~powershell
# ls
centos7.tar
~~~



### 8.3.5 docker import

> 导入使用docker export导入的容器做为本地容器镜像。



~~~powershell
# ls
centos7.tar 
~~~



~~~powershell
# docker import centos7.tar centos7:v1
~~~



~~~powershell
# docker images
REPOSITORY   TAG       IMAGE ID       CREATED              SIZE
centos7      v1        3639f9a13231   17 seconds ago       231MB
~~~



通过docker save与docker load及docker export与docker import分享容器镜像都是非常麻烦的，有没有更方便的方式分享容器镜像呢？


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















