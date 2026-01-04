
## 📥 1、背景

水稻作为全球半数以上人口的主粮，其稳定生产关乎粮食安全与社会稳定。然而，水稻在生长周期内容易受到稻瘟病、纹枯病、白叶枯病等多种病害的侵袭，这些病害若不及时识别与防控，将导致严重的产量与品质损失。传统的病害识别主要依赖农业专家田间目视检查，这种方法不仅效率低下、主观性强，且难以应对大面积监测的需求，易错过最佳防治时机。随着精准农业与人工智能技术的快速发展，基于计算机视觉的病害自动检测技术应运而生，为实现高效、无损、大规模的病害早期诊断与精准防控提供了革命性工具。特别地，基于深度学习的目标检测方法（如YOLO、Faster R-CNN系列）能够直接从田间复杂背景的图像中定位并识别出病害斑块，极大地提升了自动化水平。然而，水稻病害目标检测在实际应用中仍面临诸多挑战：田间自然环境光照变化剧烈；病害症状在初期表现细微、与健康部位对比度低；叶片重叠、姿态多样导致病灶被遮挡；以及不同病害间可能存在视觉相似性，对模型的判别能力提出了更高要求。因此，研究能够适应复杂田间环境、对小目标及相似类别具有高鲁棒性和高精度的轻量化病害检测模型，对于推动植保智能化、保障粮食安全生产具有重要的理论价值与现实意义。
## 📌 2、数据集概览

| 项目        | 内容                                                                 |
| --------- | ------------------------------------------------------------------ |
| **数据集名称** | 水稻病害目标检测数据集                                                        |
| **任务类型**  | 目标检测（Object Detection）                                             |
| **类别**    | 'Bacteria_Leaf_Blight', 'Brown_Spot', 'Leaf_smut'                  |
| **标注格式**  | YOLO TXT 格式（`<类别> <中心x> <中心y> <宽度> <高度>`，坐标和尺寸均为相对于图像宽高的归一化值（0-1）） |
| **图片总数**  | 8629                                                               |
| **标注总数**  | 457066                                                             |

## 🗂 3、数据详情

| 类别ID   | 类别名称            | 图片数量     | 标注数量       |
| ------ | --------------- | -------- | ---------- |
| 0      | pedestrian      | 7083     | 109187     |
| 1      | people          | 5226     | 38560      |
| 2      | bicycle         | 3496     | 13069      |
| 3      | car             | 8178     | 187005     |
| 4      | van             | 6537     | 32702      |
| 5      | truck           | 4567     | 16284      |
| 6      | tricycle        | 2270     | 6387       |
| 7      | awning-tricycle | 1604     | 4377       |
| 8      | bus             | 2992     | 9117       |
| 9      | motor           | 5516     | 40378      |
| **总计** | **-**           | **8629** | **457066** |
## ✨ 4、效果演示

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20260104220307.png)

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
path: E:/data/yoloTrain/uav
test: test/images  # test images
train: train/images  # train images
val: val/images  # val images
names:
  0: pedestrian
  1: people
  2: bicycle
  3: car
  4: van
  5: truck
  6: tricycle
  7: awning-tricycle
  8: bus
  9: motor

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


我这边客户资料同步身份证号