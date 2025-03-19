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