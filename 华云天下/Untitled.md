
## 📥 1、背景

随着电动自行车成为我国城市居民短途出行的主要交通工具，其带来的交通安全管理问题日益凸显。其中，骑行人员未佩戴安全头盔是导致交通事故伤亡率居高不下的关键风险因素。尽管相关法律法规已明确要求，但依靠传统警力进行人工稽查与处罚，存在监管盲区大、执法效率低、难以全天候覆盖等现实困境。在此背景下，利用智能化视频感知技术实现对“骑电动未佩戴头盔”行为的自动检测与识别，已成为提升交通执法效能、构建精准化公共安全治理体系的迫切需求。这一技术能够无缝接入日益完善的城市道路监控网络，对海量视频流进行实时分析，自动发现、预警并记录违规行为，从而形成强大的威慑效应，有效引导公众养成安全骑行习惯。然而，该任务的实现面临复杂的技术挑战：动态变化的拍摄角度与距离、复杂街道背景的干扰、不同款式头盔的外观多样性、多人密集场景下的遮挡问题，以及昼夜光照和天气条件的影响，均对检测算法的准确性、鲁棒性与实时性构成了严峻考验。因此，研究一种适应复杂城市场景、能够精准高效识别未佩戴头盔骑行行为的人工智能检测模型，对于创新社会治理模式、降低交通事故伤亡、保障人民生命财产安全具有重要的社会价值和示范意义。
## 📌 2、数据集概览

| 项目        | 内容                                                                 |
| --------- | ------------------------------------------------------------------ |
| **数据集名称** | 骑电动未佩戴头盔数据集                                                        |
| **任务类型**  | 目标检测（Object Detection）                                             |
| **类别**    | head、helmet                                                        |
| **标注格式**  | YOLO TXT 格式（`<类别> <中心x> <中心y> <宽度> <高度>`，坐标和尺寸均为相对于图像宽高的归一化值（0-1）） |
| **图片总数**  | 6840                                                               |
| **标注总数**  | 11024                                                              |

## 🗂 3、数据详情
| 类别ID   | 类别名称   | 图片数量     | 标注数量      |
| ------ | ------ | -------- | --------- |
| 0      | head   | 2394     | 3911      |
| 1      | helmet | 4848     | 7113      |
| **总计** | **-**  | **6840** | **11024** |
## ✨ 4、效果演示

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20260108220749.png)

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
path: D:/data/yoloTrain/骑电动不佩戴头盔
train: images/train
val: images/val
nc: 2
names: ['head', 'helmet']
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


