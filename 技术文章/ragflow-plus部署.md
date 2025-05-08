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

docker compose -f docker-compose_gpu.yml up -d

docker compose -f docker-compose_gpu.yml up -d --build vllm-bge

docker compose -f docker-compose_gpu.yml up -d  --build  vllm-bge

docker compose -f docker-compose_gpu.yml stop vllm-bge

vim ~/.bashrc


export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-12.4/lib64
export PATH=$PATH:/usr/local/cuda-12.4/bin

source ~/.bashrc