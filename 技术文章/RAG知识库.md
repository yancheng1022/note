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

## 1.2、nvidia-container-toolkit


![](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250610172247.png)