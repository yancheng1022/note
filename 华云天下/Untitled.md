
## 📥 1、背景

在全球气候变化与极端天气事件频发的背景下，城市内涝已成为威胁公共安全、影响交通运行与造成重大经济损失的突出问题。特别是在我国快速城市化进程中，部分区域排水系统建设相对滞后或超负荷运行，使得强降雨天气下的道路积水现象愈发普遍。传统的人工巡查与定点传感器监测方式，存在覆盖范围有限、响应滞后、人力成本高且在恶劣天气下实施困难等固有缺陷，难以满足对大面积、实时性道路积水态势感知的迫切需求。近年来，随着计算机视觉、物联网与边缘计算等技术的成熟，基于视觉感知的道路积水检测方法展现出巨大潜力。该技术能够利用广泛部署的道路监控摄像头或移动设备，实时识别与量化积水区域，为城市应急管理部门提供关键的决策支持，助力实现从“被动应对”到“主动预警”的防洪排涝模式转变。然而，实际应用仍面临诸多挑战：复杂光照条件（如反光、夜晚）、动态场景干扰（车辆驶过激起的水花、波浪）、积水形态多变以及不同材质路面反射特性差异等，都对检测算法的鲁棒性、准确性与实时性提出了极高要求。因此，研究一种能够适应复杂城市场景、精准高效的道路积水视觉检测方法，对于构建智慧防汛体系、保障民众出行安全与城市生命线稳定运行具有重要的理论价值与现实意义。
## 📌 2、数据集概览

| 项目        | 内容                                                                 |
| --------- | ------------------------------------------------------------------ |
| **数据集名称** | 钢材表面缺陷数据集                                                          |
| **任务类型**  | 目标检测（Object Detection）                                             |
| **类别**    | crazing、inclusion、patches、pitted_surface、rolled-in_scele、scratches |
| **标注格式**  | YOLO TXT 格式（`<类别> <中心x> <中心y> <宽度> <高度>`，坐标和尺寸均为相对于图像宽高的归一化值（0-1）） |
| **图片总数**  | 1800                                                               |
| **标注总数**  | 4189                                                               |

## 🗂 3、数据详情

| 类别ID   | 类别名称            | 图片数量     | 标注数量     |
| ------ | --------------- | -------- | -------- |
| 0      | crazing         | 300      | 689      |
| 1      | inclusion       | 382      | 1011     |
| 2      | patches         | 342      | 881      |
| 3      | pitted_surface  | 301      | 432      |
| 4      | rolled-in_scale | 300      | 628      |
| 5      | scratches       | 300      | 548      |
| **总计** | **-**           | **1800** | **4189** |

## ✨ 4、效果演示

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20260108220358.png)

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
path: E:/data/yoloTrain/data1
train: images/train  # train images
val: images/val # val images
nc: 1
names: ['puddle']
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


