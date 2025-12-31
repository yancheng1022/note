
## 📥 1、背景

后疫情时代，公共卫生安全防控已成为城市治理与公共服务的常态化需求，口罩作为阻断呼吸道传染病传播的关键防护手段，其规范佩戴监督仍是重点防控环节。同时，在地铁、机场、医院等人员密集场所，传统依赖人工巡查的口罩佩戴检查模式，存在效率低下、覆盖面有限、易产生接触性风险及主观误判等弊端，难以适配大规模、高流动性的场景防控需求。此外，随着智慧安防、智慧城市建设的深入推进，对公共场景下人员行为的自动化、智能化监测提出了更高要求。在此背景下，基于计算机视觉、深度学习等技术的口罩识别系统应运而生，其能够实现对人员口罩佩戴状态的实时检测、精准识别与快速预警，为公共卫生防控、智慧安防监管提供高效技术支撑，对于筑牢公共卫生安全防线、提升城市精细化管理水平具有重要的现实意义与应用价值。

## 📌 2、数据集概览

| 项目        | 内容                                                                 |
| --------- | ------------------------------------------------------------------ |
| **数据集名称** | 道路安全数据集                                                            |
| **任务类型**  | 目标检测（Object Detection）                                             |
| **类别**    | face、face_mask                                                     |
| **标注格式**  | YOLO TXT 格式（`<类别> <中心x> <中心y> <宽度> <高度>`，坐标和尺寸均为相对于图像宽高的归一化值（0-1）） |
| **图片总数**  | 7952                                                               |
| **标注总数**  | 16508                                                              |

## 🗂 3、数据详情
| 类别ID   | 类别名称      | 图片数量     | 标注数量      |
| ------ | --------- | -------- | --------- |
| 0      | face      | 5010     | 12482     |
| 1      | face_mask | 3218     | 4026      |
| **总计** | **-**     | **7952** | **16508** |
## ✨ 4、效果演示

![3ee4a7cb899802af882ad5543ff8bac.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/3ee4a7cb899802af882ad5543ff8bac.png)

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
path: D:/data/yoloTrain/口罩/face_mask
train: images/train
val: images/val
nc: 2
names: ['face','face_mask']
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

