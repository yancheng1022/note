
# 1、安装anaconda或miniconda

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

# 2、创建虚拟环境

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
# 2、环境配置

```shell
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple/
pip config set install.trusted-host pypi.tuna.tsinghua.edu.cn
pip install yolo
pip install ultralytics==8.1 -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install labelimg
```

# 3、新建训练

新建文件夹，名称叫做 yolotrain,在 yolotrain 新建labels和images文件夹，在images文件夹新建test,train,val三个文件夹，在labels下面新建train,val两个文件夹

> images文件夹下面的test,train,val三个文件夹，全部存放相同的要训练的图片(测试，训练，验证)
> labels下面的train,val两个文件夹存放的是用labelimg标注的标签数据文件


# 4、标注数据

将准备好的图片放到 images/train 文件夹，下面并且复制一份到 images/val 和 images/test两个文件夹

配置环境 的命令控制台,输入 labelimg 命令，即可打开 labelimg 程序
点击 Open Dir 按钮，选择 images/train 文件夹
点击 Change Save Dir 按钮，选择到 labels/train 文件夹
点击Save 下面的格式按钮，调整到 YOLO 格式的模式即可

>注：模型训练前要将train下的文件复制到val下

# 5、模型训练

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

```shell
# cpu训练次数
yolo detect train data=e:/yolotrain/smoke/smoke.yaml model=e:/yolotrain/yolov8s.pt epochs=100 imgsz=640
# GPU
yolo detect train data=e:/yolotrain/smoke/smoke.yaml model=e:/yolotrain/yolov8s.pt epochs=100 imgsz=640 device=0
```

会在根目录下生成runs，里面就是训练的结果


# 6、验证模型

训练完成后，可以对图片进行验证
cmd控制台输入命令：

```shell
# model 参数代表是 需要验证的模型，data 代表检测的配置
yolo detect val data=e:/yolotrain/smoke/smoke.yaml  model=e:/yolotrain/smoke/runs/detect/train/weights/best.pt

yolo predict model=best.pt source=1.jpg
```

# 7、导出模型

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

