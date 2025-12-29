
## 📥 1、背景

行人检测作为计算机视觉领域的核心任务之一，是构建智能交通、自动驾驶和视频监控等系统的关键基础技术。近年来，随着深度学习的快速发展，以卷积神经网络为代表的检测模型极大地提升了检测精度与鲁棒性，能够更有效地应对复杂环境中的遮挡、尺度多变、姿态多样和光照变化等传统难点。然而，由于行人目标具有高度的灵活性和复杂性，现有方法仍在处理密集场景下的重叠遮挡、小尺度行人识别、实时检测效率等方面面临显著挑战。为构建更加安全可靠的智能感知系统，持续优化行人检测算法、提升其在真实场景中的适用性与稳定性，依然是当前研究的重要方向。

## 📌 2、数据集概览

| 项目        | 内容                                                                  |
| --------- | ------------------------------------------------------------------- |
| **数据集名称** | 自动驾驶数据集                                                             |
| **任务类型**  | 自动驾驶（Object Detection）                                              |
| **类别**    | 'car','person','truck','bicycle','bus','traffic light','motorcycle' |
| **标注格式**  | YOLO TXT 格式（`<类别> <中心x> <中心y> <宽度> <高度>`，坐标和尺寸均为相对于图像宽高的归一化值（0-1））  |
| **图片总数**  | 66620                                                               |
| **标注总数**  | 272542                                                              |

## 🗂 3、数据详情

| 类别ID   | 类别名称   | 图片数量      | 标注数量       |
| ------ | ------ | --------- | ---------- |
| 0      | person | 66620     | 272542     |
| **总计** | **-**  | **66620** | **272542** |
## ✨ 4、效果演示

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20251229231428.png)


## 🧠 5、模型训练


1、安装miniconda
地址： https://repo.anaconda.com/miniconda/Miniconda3-latest-Windows-x86_64.exe
环境变量（根据实际的安装目录配置）：
```
C:\ProgramData\miniconda3 
C:\ProgramData\miniconda3\Scripts 
C:\ProgramData\miniconda3\Library\bin 
```
2、创建虚拟环境
```shell
conda create -n yolo python==3.8
# 查看现有环境
conda env list
# 激活环境 
conda activate yolo
# 激活失败 （执行该命令后重新进入cmd）
conda init cmd.exe 
```
3、源码下载
https://github.com/ultralytics/ultralytics
（注意：不同版本的yolo在不同tag）

4、训练脚本
```python
import warnings  
warnings.filterwarnings('ignore')  
from ultralytics import YOLO  
  
if __name__ == '__main__':  
    model = YOLO('ultralytics/cfg/models/v8/yolov8n.pt')  # 指定YOLO模型对象，并加载指定配置文件中的模型配置  
    # model.load('yolov8s.pt')      #加载预训练的权重文件'yolov8s.pt'，加速训练并提升模型性能  
    model.train(data='ultralytics/cfg/datasets/data36.yaml',  # 指定训练数据集的配置文件路径，这个.yaml文件包含了数据集的路径和类别信息  
                cache=False,  # 是否缓存数据集以加快后续训练速度，False表示不缓存  
                imgsz=640,  # 指定训练时使用的图像尺寸，640表示将输入图像调整为640x640像素  
                epochs=100,  # 设置训练的总轮数为200轮  
                batch=8,  # 设置每个训练批次的大小为16，即每次更新模型时使用16张图片  
                close_mosaic=0,  # 设置在训练结束前多少轮关闭 Mosaic 数据增强，10 表示在训练的最后 10 轮中关闭 Mosaic                workers=16,  # 设置用于数据加载的线程数为8，更多线程可以加快数据加载速度  
                patience=300,  # 在训练时，如果经过50轮性能没有提升，则停止训练（早停机制）  
                device='0',  # 指定使用的设备，'0'表示使用第一块GPU进行训练  
                optimizer='SGD',  # 设置优化器为SGD（随机梯度下降），用于模型参数更新  
                )
```
5、配置文件 data.yaml

```yaml
path: D:/data/yoloTrain/autocar
train: images/train
val: images/val
nc: 7
names: ['car','person','truck','bicycle','bus','traffic light','motorcycle']
```

## 🛠 6、配套服务

我们提供一站式视觉解决方案，包括：

- 模型训练与调优指导
- 部署环境远程配置
- 定制标注与数据增强
- 毕业设计/课题辅导
- 企业项目合作开发

## ❓ 7、常见问题

**Q：标注格式如何转换？**  
A：提供Python转换脚本，支持YOLO→VOC/COCO，开箱即用：[点击下载转换脚本](https://download.csdn.net/download/qq_34645958/92486136?spm=1001.2014.3001.5503)

