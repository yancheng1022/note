
## 📥 1、背景

随着智能交通系统（ITS）的高速演进与智慧城市建设的深入推进，车辆类型精准识别已成为交通流量管控、违章执法取证、停车场智能管理及自动驾驶环境感知的核心技术支撑。当前城市路网中乘用车、商用车、特种作业车等多类型车辆混行特征显著，据统计，我国城市主干道高峰时段车辆类型占比波动幅度可达 40% 以上，这对识别算法的鲁棒性与适应性提出了严苛要求。然而传统车辆类型识别方法存在诸多局限：基于人工特征提取的算法易受光照突变、天气干扰、车辆遮挡等复杂场景影响，识别准确率不足 75%；基于多阶段检测的深度学习模型虽精度有所提升，但推理时延普遍超过 200ms，难以满足实时交通监测的需求。此外，不同车型间的尺度差异大、相似车型特征区分度低等问题，进一步制约了传统算法在实际交通场景中的规模化应用。在此背景下，兼具检测精度与实时性优势的算法框架，为车辆类型识别提供了高效可行的技术路径，其单阶段端到端的检测模式，可有效整合目标定位与类型分类任务，通过特征金字塔网络与多尺度融合策略，显著提升对小型乘用车、大型货车等不同尺度车型的识别能力，为智能交通系统的落地应用奠定了坚实基础。
## 📌 2、数据集概览

| 项目        | 内容                                                                 |
| --------- | ------------------------------------------------------------------ |
| **数据集名称** | 车辆类型数据集                                                            |
| **任务类型**  | 目标检测（Object Detection）                                             |
| **类别**    | 'car', 'bus', 'truck'                                              |
| **标注格式**  | YOLO TXT 格式（`<类别> <中心x> <中心y> <宽度> <高度>`，坐标和尺寸均为相对于图像宽高的归一化值（0-1）） |
| **图片总数**  | 7000                                                               |
| **标注总数**  | 9221                                                               |

## 🗂 3、数据详情
| 类别ID   | 类别名称  | 图片数量      | 标注数量      |
| ------ | ----- | --------- | --------- |
| 0      | car   | 12251     | 43867     |
| 1      | bus   | 3952      | 6069      |
| 2      | truck | 6127      | 9973      |
| **总计** | **-** | **16270** | **59909** |
## ✨ 4、效果演示

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20260103230549.png)



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
path: D:/data/yoloTrain/车辆目标类型
train: images/train
val: images/val
nc: 3
names: ['car', 'bus', 'truck']
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

