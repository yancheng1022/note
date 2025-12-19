
## 📥 1、背景

道路作为交通基础设施的核心组成部分，其完好性与安全性直接关系到公众出行效率与生命财产安全。然而，在长期服役过程中，路面受车辆荷载、环境侵蚀及材料老化等因素影响，极易产生裂缝、坑洞等典型病害。这些病害若未能被及时识别与修复，不仅会加速路面结构性能的恶化，缩短道路使用寿命，更可能引发车辆颠簸、失控等安全隐患，对日常交通运行构成持续威胁。

传统的人工巡检方式存在效率低下、主观性强、覆盖范围有限且高危环境下作业风险较高等固有局限，难以满足大规模路网常态化、精细化养护的迫切需求。近年来，随着计算机视觉与深度学习技术的迅猛发展，基于图像或视频的自动化病害检测方法已成为智能交通与基础设施运维领域的研究热点。其中，YOLO（You Only Look Once）系列模型以其出色的实时检测性能与较高的精度，在目标检测任务中展现出显著优势，为快速、准确地从复杂道路场景中自动定位并识别裂缝、坑洞等病害提供了强有力的技术路径。构建高质量的YOLO专用数据集，是训练鲁棒、可靠检测模型的基础，也是推动该技术从研究走向实际工程应用的关键环节。
## 📌 2、数据集概览

| 项目        | 内容                                                                 |
| --------- | ------------------------------------------------------------------ |
| **数据集名称** | 道路表面缺陷检测数据集                                                        |
| **任务类型**  | 目标检测（Object Detection）                                             |
| **类别**    | Cracks、pothole                                                     |
| **标注格式**  | YOLO TXT 格式（`<类别> <中心x> <中心y> <宽度> <高度>`，坐标和尺寸均为相对于图像宽高的归一化值（0-1）） |
| **图片总数**  | 9074                                                               |
| **标注总数**  | 17576                                                              |

## 🗂 3、数据详情

| 类别ID   | 类别名称    | 图片数量     | 标注数量      |
| ------ | ------- | -------- | --------- |
| 0      | Cracks  | 5896     | 9146      |
| 1      | pothole | 3161     | 8430      |
| **总计** | **-**   | **9074** | **17576** |



## ✨ 4、效果演示

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20251219115052.png)


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
5、配置文件
```yaml


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
A：免费提供Python转换脚本，支持YOLO→VOC/COCO，开箱即用。

