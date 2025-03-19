# 1、docker

## 1.1、更新yum

如果不升级更新yum 可能在后续docker部署后再更新容器会出现oci runtime error等
```shell
yum update -y
```

## 1.2、安装yum工具类准备

```shell
yum install -y yum-utils device-mapper-persistent-data lvm2
```

## 1.3、配置yum源为阿里云yum源

```shell
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

## 1.4、docker安装

```shell
yum install docker-ce
```

## 1.5、启动docker服务

```shell
systemctl start docker
# 开机自启
systemctl enable docker
# 查看状态
systemctl status docker
```

## 1.6、设置Docker国内镜像源
由于docker默认从docker hub https://registry.hub.docker.com/ 下载镜像，所以速度非常慢，可以通过设置为国内镜像源（加速器）

（1）通过 vi/vim 进入/etc/docker/daemon.json，如果没有这个文件，需要在 /etc/docker目录下手动进行创建这个目录。

```json

{
	"registry-mirrors": [
		"https://2a6bf1988cb6428c877f723ec7530dbc.mirror.swr.myhuaweicloud.com",
		"https://docker.m.daocloud.io",
		"https://hub-mirror.c.163.com",
		"https://mirror.baidubce.com",
		"https://your_preferred_mirror",
		"https://dockerhub.icu",
		"https://docker.registry.cyou",
		"https://docker-cf.registry.cyou",
		"https://dockercf.jsdelivr.fyi",
		"https://docker.jsdelivr.fyi",
		"https://dockertest.jsdelivr.fyi",
		"https://mirror.aliyuncs.com",
		"https://dockerproxy.com",
		"https://mirror.baidubce.com",
		"https://docker.m.daocloud.io",
		"https://docker.nju.edu.cn",
		"https://docker.mirrors.sjtug.sjtu.edu.cn",
		"https://docker.mirrors.ustc.edu.cn",
		"https://mirror.iscas.ac.cn",
		"https://docker.rainbond.cc"
	]
}

```

## 1.7、重启docker服务

```shell
# 重新加载 systemd 的配置
sudo systemctl daemon-reload
 
# 重启 Docker 服务
sudo systemctl restart docker
```


# 2、zlmediaki

## 2.1、拉取镜像

```shell
docker pull zlmediakit/zlmediakit:master
```

## 2.2、运行

```shell
docker run -id -p 1935:1935 -p 8080:80 -p 8443:443 -p 8554:554 -p 10000:10000 -p 10000:10000/udp -p 8000:8000/udp -p 9000:9000/udp zlmediakit/zlmediakit:master
```

| 宿主机端口（协议）       | 容器端口  | 协议类型       | 主要功能             | 典型应用场景                |
| --------------- | ----- | ---------- | ---------------- | --------------------- |
| **1935** (TCP)  | 1935  | RTMP       | 实时消息传输协议，支持直播推拉流 | 摄像头推流、直播平台流媒体分发       |
| **8080** (TCP)  | 80    | HTTP       | Web服务及管理界面访问     | 通过浏览器访问流媒体管理页面或API接口  |
| **8443** (TCP)  | 443   | HTTPS      | 加密的Web服务访问       | 安全传输流媒体配置或敏感数据        |
| **8554** (TCP)  | 554   | RTSP       | 实时流协议，用于视频监控设备通信 | IPC摄像头推流、客户端拉取监控流     |
| **10000** (TCP) | 10000 | WebRTC/SRT | 低延迟音视频传输（TCP通道）  | 实时视频通话、SRT加密流传输       |
| **10000** (UDP) | 10000 | WebRTC/SRT | 低延迟音视频传输（UDP通道）  | WebRTC实时通信、SRT流媒体传输   |
| **8000** (UDP)  | 8000  | RTP/RTCP   | 实时传输协议及控制协议      | 音视频数据包传输、流媒体质量控制      |
| **9000** (UDP)  | 9000  | RTC/SRT    | 实时通信或安全可靠传输      | WebRTC ICE协商、SRT流媒体传输 |

# 3、ffmpeg

## 3.1、拉取镜像

```shell
docker pull jrottenberg/ffmpeg
```

## 3.2、运行

```shell
docker run -itd --name ffmpeg -p 8081:8080 -v /home/ffmpeg/:/mnt/app/ --entrypoint='bash' jrottenberg/ffmpeg
```

## 3.3、测试文件拷贝到容器内

```shell
docker cp /test/test.mp4 fervent_panini:/
```

## 3.4、进入容器推流

```shell
docker exec -it 3c615cda1714 /bin/bash

ffmpeg -re -i "smoke.mp4" -vcodec h264 -b:v 1000k -maxrate 1000k -bufsize 2000k -vf "scale=1280:720" -acodec aac -b:a 128k -f rtsp -rtsp_transport tcp rtsp://111.53.30.112:8554/live/test
```

## 3.5、脚本编写

```shell
vim rtmp_test.sh

docker exec -i  9074663f321a /bin/bash -c 'ffmpeg -re -i "smoke.mp4" -vcodec h264 -b:v 1000k -maxrate 1000k -bufsize 2000k -vf "scale=1280:720" -acodec aac -b:a 128k -f rtsp -rtsp_transport tcp rtsp://111.53.30.112:8554/live/test'

chmod +x rtmp_test.sh 
./rtmp_test.sh 
```