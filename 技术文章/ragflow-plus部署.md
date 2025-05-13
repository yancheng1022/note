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