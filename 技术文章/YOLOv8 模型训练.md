---
title: YOLOv8 模型训练
date: 2024/11/01
updated: 2023/11/02
categories:
  - coding
tags:
  - yolo
  - 视觉分析
  - 小模型
---
# 1、模型训练
## 1.1、安装anaconda或miniconda

1、anaconda

下载anaconda，启动这个软件，新建一个虚拟环境：

地址： https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-4.0.0-Windows-x86_64.exe

2、miniconda

地址： https://repo.anaconda.com/miniconda/Miniconda3-latest-Windows-x86_64.exe
环境变量：
```
C:\ProgramData\miniconda3 
C:\ProgramData\miniconda3\Scripts 
C:\ProgramData\miniconda3\Library\bin 
```

## 1.2、创建虚拟环境

```shell
conda create -n yolo8 python==3.8 
# 查看现有环境
conda env list
# 激活环境 
conda activate yolo8
# 激活失败 （执行该命令后重新进入cmd）
conda init cmd.exe 
# 删除环境 
conda env remove -n yolo8
```
## 1.3、环境配置

```shell
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple/
pip config set install.trusted-host pypi.tuna.tsinghua.edu.cn
pip install yolo
pip install ultralytics==8.1 -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install labelimg
```

## 1.4、新建训练

新建文件夹，名称叫做 yolotrain,在 yolotrain 新建labels和images文件夹，在images文件夹新建test,train,val三个文件夹，在labels下面新建train,val两个文件夹

> images文件夹下面的test,train,val三个文件夹，全部存放相同的要训练的图片(测试，训练，验证)
> labels下面的train,val两个文件夹存放的是用labelimg标注的标签数据文件


## 1.5、标注数据

将准备好的图片放到 images/train 文件夹，下面并且复制一份到 images/val 和 images/test两个文件夹

配置环境 的命令控制台,输入 labelimg 命令，即可打开 labelimg 程序
点击 Open Dir 按钮，选择 images/train 文件夹
点击 Change Save Dir 按钮，选择到 labels/train 文件夹
点击Save 下面的格式按钮，调整到 YOLO 格式的模式即可

>注：模型训练前要将train下的文件复制到val下

## 1.6、模型训练

在 yolotrain 文件下新建一个训练的配置文件,smoke.yaml

```yaml
path: E:/yolotrain/smoke
train: images/train
val: images/val
test: images/test
nc: 1
names: ['smoke']
```

path: 代表训练的根目录
train: 代表要训练的图片文件夹,相对于path路径
val: 代表要验证的图片文件夹,相对于path路径
test: 代表要测试的图片文件夹,相对于path路径
nc: 代表分类名称数量，这是1个
names: 是一个json数组，代表的是标注的分类名称(注意保持顺序)

开始训练：

进入虚拟机根目录：
yolo detect train data=d:/data/yoloTrain/OD-01/data.yaml model=d:/data/yoloTrain/yolov8n.pt epochs=100 imgsz=640 device=0 close_mosaic=0

```shell
# cpu训练次数
yolo detect train data=e:/yolotrain/smoke/smoke.yaml model=e:/yolotrain/yolov8s.pt epochs=100 imgsz=640
# GPU
yolo detect train data=e:/yolotrain/smoke/smoke.yaml model=e:/yolotrain/yolov8s.pt epochs=100 imgsz=640 device=0
```

会在根目录下生成runs，里面就是训练的结果


## 1.7、验证模型

训练完成后，可以对图片进行验证
cmd控制台输入命令：

```shell
# model 参数代表是 需要验证的模型，data 代表检测的配置
yolo detect val data=e:/yolotrain/smoke/smoke.yaml  model=e:/yolotrain/smoke/runs/detect/train/weights/best.pt

```

## 1.8、导出模型

将YOLOv8训练得到的模型导出为ONNX格式，有助于更好地部署模型，并在不同的框架和平台之间进行快速交换和移植，提高模型的效用和可用性

``` shell
# 安装onnx
pip install onnx -i https://pypi.tuna.tsinghua.edu.cn/simple
# onnxsim 用于简化和优化onnx模型，移除冗余的操作符，使模型运行更快
pip install onnxsim -i https://pypi.tuna.tsinghua.edu.cn/simple 
pip install onnxruntime -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install onnxruntime-gpu -i https://pypi.tuna.tsinghua.edu.cn/simple
yolo export model=e:/yolotrain/smoke/runs/detect/train/weights/best.pt format=onnx simplify=True
```

yolo export model=d:/data/yoloTrain/test/runs/detect/train/weights/best.pt format=onnx simplify=True

（yolo7）
python export.py --weights D:/data/temp/best_cs2_model.pt --grid --simplify --dynamic --img-size 640 640       
## 1.9、安装cuda和cudnn和pytorch

1、cuda地址：https://developer.nvidia.cn/cuda-toolkit

```
nvcc -V
```


2、cudnn地址：https://developer.nvidia.com/cudnn-archive

3、pytorch安装：https://pytorch.org/

```
https://pytorch.org/
```

# 2、yolo原理

## 2.1、训练部分

### 2.1.1、yolo输入参数介绍

图片 标签 置信度（这可以理解为一个隐藏的参数，因为是我们人工标注，所以可以认为置信度为1）

对应的数据格式：

 0 0.40729166666666666 0.6916666666666667 0.06041666666666667 0.18888888888888888

类别 中心点x 中心点y 宽度占比 高度占比


### 2.1.2、yolo的图像和标签处理

一般实际是19X19的361个格子，用分类和定位算法，逐一的对每个格子进行查看，如果没找到预置框的位置，生成分类0的一条数据，如果找到，则生成对应分类的数据（包含中心点坐标宽高等n个参数），最终会生成19X19Xn的一个三维的张量

### 2.1.3、yolo的训练过程

原始图像（100X100X3） -  CNN  -  卷积  -  池化 -  预算处理，映射到nXnX8，然后反向的一个tio下降，得到一个最好best.pt

## 2.2、检测部分

### 2.2.1、如何做检测

通过best.pt得到卷积，输入的图片就相当于参数，得到推理结果（包含中心点坐标，宽高，置信度等），这样就完成了一次检测



### 2.2.2、交并比 iou

IOU = 交集/并集 >= 0.5

非最大抑制算法

遍历19X19个框中PC大于0.4的，预测框，交并比，找出不同的对象，是为了输出



## 2.3、linux+docker使用GPU推理

0、安装cuda、cudnn

1、安装NVIDIA Container Toolkit

在做算法服务容器化部署的时候需要搭建支持调用NV GPU的Docker环境，这就需要NVIDIA Container Toolkit工具包，NVIDIA Container Toolkit 使用户能够构建和运行 GPU 加速的容器

https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html#installing-with-yum-or-dnf

2、容器配置文件修改

```yml
services:  
  analyzer:  
    image: ${ANALYZER_SERVICES_IMAGE_NAME}:${ANALYZER_SERVERS_IMAGE_TAG}  
    restart: always  
    container_name: analyzer  
    hostname: analyzer  
    network_mode: default  
    ports:  
      - 40154:8083  
    environment:  
      - NVIDIA_DRIVER_CAPABILITIES=compute,utility  
      - NVIDIA_VISIBLE_DEVICES=all  
      - LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH  
    volumes:  
      - /home/lantrack/4015/module/upload:/upload  
      - /home/lantrack/cdeslogs/analyzer:/cdeslogs  
      - /usr/local/cuda/lib64:/usr/local/cuda/lib64  
    devices:  
      - /dev/nvidiactl  
      - /dev/nvidia-uvm  
      - /dev/nvidia-uvm-tools  
    runtime: nvidia
```

3、进入容器查看是否生效

```shell
docker exec -it analyzer /bin/bash
nvidia-smi
```