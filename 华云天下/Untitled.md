
## 📥 1、背景

在制造业高端化、智能化转型的浪潮中，钢铁作为基础性关键材料，其表面质量直接关系到下游装备的可靠性、安全性与使用寿命。传统的人工目视检测方法，不仅效率低下、劳动强度大，而且受人员经验、主观判断和疲劳因素影响，难以满足现代高速连铸连轧生产线对检测精度与一致性的严苛要求。随着深度学习与机器视觉技术的突破，基于视觉的自动化表面缺陷检测已成为提升钢材质量管控水平、实现“工业质量之眼”的核心技术路径。该技术通过实时、非接触式地识别各类表面缺陷（如裂纹、划痕、孔洞、结疤等），能够实现生产过程的早期干预与质量分级，从而显著降低次品率、节约生产成本并提升产品竞争力。然而，钢铁生产的实际环境给缺陷检测带来了巨大挑战：高速运动的生产线、复杂多变的照明条件、缺陷形态与尺度的多样性、以及背景纹理（如氧化皮、轧制纹路）的强干扰，都对算法的检测精度、泛化能力与实时处理速度提出了极高要求。因此，研究一种能够适应复杂工业现场、兼具高精度与高鲁棒性的钢材表面缺陷智能检测方法，对于推动钢铁行业智能化升级、保障国家重大工程材料质量、实现从“制造”到“智造”的跨越具有重大的工程应用价值与战略意义。
## 📌 2、数据集概览

| 项目        | 内容                                                                 |
| --------- | ------------------------------------------------------------------ |
| **数据集名称** | 骑电动未佩戴头盔数据集                                                        |
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
train: /root/autodl-tmp/ultralytics-main/data/NEU-DET/train
val: /root/autodl-tmp/ultralytics-main/data/NEU-DET/test
nc: 6 #标签数量
names: ['crazing', 'inclusion', 'patches', 'pitted_surface', 'rolled-in_scale', 'scratches']#标签名称
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


