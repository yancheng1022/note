
## 📥 1、背景

无人机视角下的目标检测是计算机视觉领域的重要研究方向，随着无人机技术的快速发展和广泛应用，其相关研究也日益受到关注。与传统固定摄像头或地面拍摄视角不同，无人机能够提供独特的俯视或倾斜视角，具备高度灵活、视野广阔的优势，已广泛应用于城市规划、农业监测、灾害救援、交通管理、安防巡逻等多个领域。然而，无人机视角也带来了一系列特有的技术挑战：目标通常尺度较小且分布密集；拍摄角度多变导致目标外观、姿态和光照条件变化剧烈；高速飞行可能引起运动模糊；同时还需兼顾实时处理与能耗限制。近年来，随着深度学习的突破，尤其是卷积神经网络和Transformer等模型的发展，无人机目标检测在精度和效率上取得了显著进展，但如何在复杂动态环境中实现鲁棒、高效、轻量化的检测，仍然是当前研究的关键问题。因此，探索适用于无人机视角的目标检测方法，对于推动相关实际应用具有重要意义。
## 📌 2、数据集概览

| 项目        | 内容                                                                         |
| --------- | -------------------------------------------------------------------------- |
| **数据集名称** | 无人机视角目标检测数据集                                                               |
| **任务类型**  | 目标检测（Object Detection）                                                     |
| **类别**    | pedestrian、people、bicycle、car、van、truck、tricycle、awning-tricycle、bus、motor |
| **标注格式**  | YOLO TXT 格式（`<类别> <中心x> <中心y> <宽度> <高度>`，坐标和尺寸均为相对于图像宽高的归一化值（0-1））         |
| **图片总数**  | 8629                                                                       |
| **标注总数**  | 457066                                                                     |

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

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20260104212003.png)


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