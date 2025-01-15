
# 1、拉取镜像

```shell
docker pull minio/minio
```
# 2、配置挂载目录和上传文件目录

```shell
mkdir -p /opt/minio/config

mkdir -p /opt/minio/data
```

# 3、创建容器并运行

```shell
docker run \
-p 9000:9000 \
-p 9001:9001 \
--net=host \
--name minio \
-d --restart=always \
-e "MINIO_ACCESS_KEY=admin" \
-e "MINIO_SECRET_KEY=lantrack@123456" \
-v /opt/minio/data:/data \
-v /opt/minio/config:/root/.minio \
minio/minio server \
/data --console-address ":9001" -address ":9000"

```

使用 虚拟机ip:9001访问控制台
springBoot集成的话，使用9000端口


# 4、防火墙配置

```shell
iptables -I INPUT -p tcp --dport 9000 -j ACCEPT
service iptables save
```

# 5、新建buckets、access keys


access key和secret key：

```txt
4lmXJON1Tg6wcva3yms2
ptwEzlaFVPWThTfLcDDQKuiy7mpG9NM2waBtcnVX
```

