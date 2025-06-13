# 1、部署

## 1.1、docker

由于Centos7自带的yum源中的Docker版本过低，默认的版本1.13.1

```shell
docker version
# 1、删除docker
yum remove docker docker-common docker-client
# 2、安装相关依赖
yum install -y yum-utils device-mapper-persistent-data lvm2
# 3、设置yum源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# 4、查看可用docker版本
yum list docker-ce --showduplicates | sort -r
# 5、安装docker
yum -y install docker-ce-25.0.3-1.el7
# 6、启动docker
systemctl start docker
# 7、设置开机启动
systemctl enable docker
```

## 1.2、cuda
1、https://developer.nvidia.com/cuda-12-4-0-download-archive
获取runfile命令

2、执行sh cuda_12.4.0_550.54.14_linux.run
在执行run脚本时会出现安装的选项，如果你已经安装cuda driver了则把安装选项中的cuda driver勾掉即可，无需重复安装

2、修改环境变量
vim ~/.bashrc
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-12.4/lib64
export PATH=$PATH:/usr/local/cuda-12.4/bin
source ~/.bashrc

## 1.3、nvidia-container-toolkit
vi  /etc/yum.repos.d/nvidia-container-toolkit.repo

```shell
[root@localhost docker]# cat /etc/yum.repos.d/nvidia-container-toolkit.repo
[nvidia-container-toolkit]
name=nvidia-container-toolkit
baseurl=https://mirrors.ustc.edu.cn/libnvidia-container/stable/rpm/$basearch
repo_gpgcheck=1
gpgcheck=0
enabled=1
gpgkey=https://mirrors.ustc.edu.cn/libnvidia-container/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
    
[nvidia-container-toolkit-experimental]
name=nvidia-container-toolkit-experimental
baseurl=https://mirrors.ustc.edu.cn/libnvidia-container/experimental/rpm/$basearch
repo_gpgcheck=1
gpgcheck=0
enabled=0
gpgkey=https://mirrors.ustc.edu.cn/libnvidia-container/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
```

 sudo yum clean all
 sudo yum install -y nvidia-container-toolkit


## 1.4、ollama

```shell
docker run -d --gpus=all -p 11434:11434 -v /home/lantrack/ollama:/root/.ollama --name ollama ollama/ollama

docker exec -it ollama bash
```


# 7、attu(Milvus客户端)

```shell
docker run -d --name attu -p 18000:3000 \
  -e MILVUS_URL=host.docker.internal:19530 \
  --add-host=host.docker.internal:host-gateway zilliz/attu:v2.3.8
```

docker compose -f docker-compose.yml down -v

docker compose -f docker-compose.yml up -d


![](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250610172247.png)