---
title: YOLO
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
yolo detect train data=d:/data/yoloTrain/smoke/smoke.yaml model=d:/data/yoloTrain/yolov8s.pt epochs=150 imgsz=640 device=0 close_mosaic=0

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

yolo export model=d:/data/yoloTrain/smoke/runs/detect/train/weights/best.pt format=onnx simplify=True

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


# 3、模型转换

## 3.1、tflite

TensorFlow Lite 是TensorFlow在移动和嵌入式设备上的轻量级推理框架，专门针对资源受限的环境进行了优化，以便在手机、嵌入式设备上运行深度学习模型。
优势：缩减了模型的大小和功耗，适合嵌入式等资源受限的设备。
坑点：tfl 目前主要支持 cnn 相关的算子 ，对 rnn 等其他网络中的算子还没有很好的支持。对自家的tf框架支持比较好，对于其他框架支持不佳


pytorch - onnx - tensorflow -tflite


![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250327220037.png)

浮点版转换：文件大，准确度高
整数量化版转换：文件小，速度快，准确率低

yolo5

python export.py --weights E:\temp\best.pt --include tflite --img 640

python export.py --weights E:\temp\3\best.pt --include tflite --int8 --img 640

yolo8 

代码exportTflite.py


python export.py --weights E:\temp\5\mobilefacenet_fp32.onnx --include tflite --img 640

## 3.2、ncnn

NCNN是腾讯优图实验室开源的高性能神经网络推理框架，专为移动端及嵌入式设备设计，以轻量化、高效率为核心优势


yolo8 

代码exportTflite.py

## 3.3、kmodel

E:\code\kmodel

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250605235120.png)

yolo5导出onnx（不要执行export.py）：
python export.py   --weights D:\code\yolo\yolov5-master\runs\train\exp73\weights\best.pt   --include onnx   --img 640   --batch 1   --simplify   --opset 12   --dynamic  

yolo5,8导出kmodel：
python to_kmodel.py --target k230 --model D:\code\yolo\yolov5-master\runs\train\exp73\weights\best.onnx --dataset E:\data\yoloTrain\data11\train\images --input_width 640 --input_height 640 --ptq_option 0

python export.py   --weights E:\temp\8\shoppingbest5.pt   --include onnx   --img 640   --batch 1   --simplify   --opset 12   --dynamic


# 4、数据集


| 序号  | 任务/数据集   | 序号  | 任务/数据集  | 序号  | 任务/数据集   | 序号  | 任务/数据集     | 序号  | 任务/数据集    |
| --- | -------- | --- | ------- | --- | -------- | --- | ---------- | --- | --------- |
| 1   | 无人机视角数据集 | 2   | 遛狗未牵绳检测 | 3   | 水果品质检测   | 4   | 篮球篮筐检测     | 5   | 消火栓检测     |
| 6   | 工地安全检测   | 7   | 无人机检测   | 8   | 行人目标检测   | 9   | 跌倒检测       | 10  | 钢材表面缺陷    |
| 11  | 人体关键点检测  | 12  | 肺部CT疾病  | 13  | 狗种类识别    | 14  | 中国象棋识别     | 15  | 扑克牌识别     |
| 16  | 道路表面缺陷   | 17  | 道路交通标志  | 18  | 钓鱼识别     | 19  | 人物头部识别     | 20  | 口罩识别      |
| 21  | 安全帽检测    | 22  | 抽烟识别    | 23  | 火灾烟雾识别   | 24  | 玩手机识别      | 25  | 电动车检测     |
| 26  | 煤矿安全帽检测  | 27  | 红绿灯识别   | 28  | CF数据集    | 29  | 车辆行人检测     | 30  | 车辆类型识别    |
| 31  | 员工行为检测   | 32  | 水稻病害识别  | 33  | 骑电动未戴头盔  | 34  | 疲劳驾驶检测     | 35  | 煤矿皮带大块    |
| 36  | 矿石分类识别   | 37  | 六类可回收物  | 38  | RTTS图像去雾 | 39  | COCO2017结果 | 40  | apex识别    |
| 41  | 道路安全识别   | 42  | cs2识别   | 43  | 道路自动驾驶   | 44  | X光安检识别     | 45  | 肺部CT肿瘤    |
| 46  | 厨师帽检测    | 47  | 老鼠检测    | 48  | 反光背心检测   | 49  | 无人机车辆检测    | 50  | 打架检测      |
| 51  | 数字识别     | 52  | 井盖检测    | 53  | 森林火灾检测   | 54  | 肺部疾病分类     | 55  | 无人机车辆     |
| 56  | 明厨亮灶检测   | 57  | 无人机行人检测 | 58  | 水果分类识别   | 59  | 睡岗睡觉检测     | 60  | 中草药检测     |
| 61  | 灭火器检测    | 62  | 道路积水检测  | 63  | 钢筋检测     | 64  | 玉米病害检测     | 65  | 垃圾桶溢满     |
| 66  | 船舶检测     | 67  | 车牌识别    | 68  | 焊缝缺陷检测   | 69  | 重型设备车辆检测   | 70  | 猜拳识别      |
| 71  | 攀爬检测     | 72  | 垃圾分类识别  | 73  | 传送带缺陷检测  | 74  | 混凝土裂缝检测    | 75  | 木材缺陷检测    |
| 76  | 人车第一视角   | 77  | 斑马线红绿灯  | 78  | 国内交通标志   | 79  | 西红柿成熟度识别   | 80  | 遥感小目标     |
| 81  | 皮肤病检测    | 82  | 花卉识别    | 83  | 摔倒检测     | 84  | 草莓病害检测     | 85  | 无人机检测2    |
| 86  | 猪识别检测    | 87  | 皮带检测    | 88  | 交通事故检测   | 89  | 苹果叶病害检测    | 90  | 鸟类识别      |
| 91  | 气球检测     | 92  | 红外船舶检测  | 93  | 管道泄漏检测   | 94  | 无人机道路损害检测  | 95  | 草莓成熟度     |
| 96  | 鱼类检测     | 97  | 火焰检测    | 98  | 电动车进电梯   | 99  | 课堂行为检测     | 100 | 柑橘叶片病害数据集 |
 