# 1、上传源码

```shell
# 本地目录
lpwd 
# 切换本地目录
lcd C:\Users\admin1\Desktop\yc
# 切换远程目录
cd /home/lantrack
# 拷贝文件
put ragflow-plus-main.zip
# 解压
unzip ragflow-plus-main.zip
# 重命名
mv ragflow-plus-main ragflow-plus
```

# 2、运行

使用GPU运行：

```shell
docker compose -f docker/docker-compose_gpu.yml up -d
```

使用CPU运行：

```shell
docker compose -f docker/docker-compose.yml up -d
```

访问地址：`服务器ip:80`，进入到前台界面

访问地址：`服务器ip:8888`，进入到后台管理界面


docker compose -f docker-compose.yml down -v

docker compose -f docker-compose.yml up -d

docker compose -f docker-compose_gpu.yml up -d --build vllm-bge

docker compose -f docker-compose_gpu.yml up -d  --build  vllm-bge

docker compose -f docker-compose_gpu.yml stop vllm-deepseek

# 3、cuda安装
1、https://developer.nvidia.com/cuda-12-4-0-download-archive
获取runfile命令

2、执行sh cuda_12.4.0_550.54.14_linux.run
在执行run脚本时会出现安装的选项，如果你已经安装cuda driver了则把安装选项中的cuda driver勾掉即可，无需重复安装

2、修改环境变量
vim ~/.bashrc
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-12.4/lib64
export PATH=$PATH:/usr/local/cuda-12.4/bin
source ~/.bashrc
# 4、nvidia-container-toolkit 安装

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


# 5、迁移

迁移到新机器时,直接把项目拷贝过来还不可以

_要把每个容器的数据也拷贝过去(dify是直接放在项目下的,很方便)_

**1.先查看一个mysql容器的数据放在哪里(ragflow-mysql:容器名)**

```bash
docker inspect ragflow-mysql | grep Mounts -A 10
```

命令返回结果如下:

```bash
   
   "Mounts": [
                {
                    "Type": "volume",
                    "Source": "ragflow_mysql_data",
                    "Target": "/var/lib/mysql",
                    "VolumeOptions": {}
                }
            ],
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
--
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/home/scia/ragflow/docker/init.sql",
                "Destination": "/data/application/init.sql",
                "Mode": "rw",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "volume",
```

**2.实际物理存储路径(docker管理卷:ragflow_mysql_data)**

```bash
docker volume inspect ragflow_mysql_data | grep Mountpoint
```

命令返回的结果

```bash
  "Mountpoint": "/var/lib/docker/volumes/ragflow_mysql_data/_data",
```

**3.进入到 /var/lib/docker/volumes/**

```bash
ll
```

命令返回的结果

```bash
drwx-----x  3 root root   4096 Mar 11 14:25 ragflow_esdata01/
drwx-----x  3 root root   4096 Mar 11 14:25 ragflow_minio_data/
drwx-----x  3 root root   4096 Mar 11 14:25 ragflow_mysql_data/
drwx-----x  3 root root   4096 Mar 11 14:25 ragflow_redis_data/
```

把这些都打包了,依次执行以下命令

```bash
tar -cvf ragflow_esdata01.tar ragflow_esdata01
```

```bash
tar -cvf ragflow_minio_data.tar ragflow_minio_data
```

```bash
tar -cvf ragflow_mysql_data.tar ragflow_mysql_data
```

```bash
 tar -cvf ragflow_redis_data.tar ragflow_redis_data
```

4.将原项目也进行打包

```bash
tar -cvf ragflow.tar ragflow
```

5.将打包后的tar文件全部上传到新机器,并解压

```bash
tar -xvf 包名.tar
```

6.启动项目

```bash
#把原容器删了
docker compose -f docker-compose.yml -p ragflow down
docker compose -f docker-compose.yml -p ragflow up -d

查看服务日志
docker logs -f ragflow-server
```

# 6、ollama下载

docker run -d -v /home/lantrack/ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama

docker run -d -v /home/ollama:/root/.ollama -p 11435:11434 --name ollama ollama/ollama
docker exec -it ollama bash