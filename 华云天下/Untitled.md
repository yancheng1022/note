
## 📥 1、背景

自动驾驶技术的普及对环境感知系统的实时性与准确性提出了严苛要求，目标检测作为感知层核心任务，直接决定了自动驾驶车辆对行人、车辆、交通标志等道路元素的识别能力与行驶安全性。传统目标检测方法难以适配复杂交通场景下多尺度目标（尤其是远距离小目标）的检测需求，而两阶段检测算法虽精度较高却存在推理延迟，无法满足车载系统实时响应要求（通常需≥30 帧 / 秒）。YOLO 系列算法凭借 “单次前向传播完成定位与分类” 的单阶段检测架构，实现了检测速度与精度的初步平衡，成为自动驾驶目标检测的主流技术方向。然而，真实道路环境中的透视畸变、光照变化、目标遮挡等复杂因素，以及车载计算平台的资源限制，仍导致现有 YOLO 算法在小目标检测精度、模型轻量化与实时性能的平衡上存在瓶颈，因此围绕 YOLO 算法优化以提升其在自动驾驶场景下的鲁棒性与适配性，具有重要的研究价值与应用意义。

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
| 类别ID   | 类别名称          | 图片数量      | 标注数量       |
| ------ | ------------- | --------- | ---------- |
| 0      | car           | 14611     | 81979      |
| 1      | person        | 7789      | 19996      |
| 2      | truck         | 6652      | 10276      |
| 3      | bicycle       | 1550      | 2412       |
| 4      | bus           | 2371      | 2897       |
| 5      | traffic light | 2836      | 7318       |
| 6      | motorcycle    | 3340      | 5852       |
| **总计** | **-**         | **15715** | **130730** |
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

