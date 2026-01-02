
## 📥 1、背景

在全球人口健康保障体系中，肺部疾病（如肺癌、肺炎、肺结核等）因其高发病率、高致死率及强传染性，已成为威胁人类生命健康的重大公共卫生问题，早期精准诊断是提升治疗效果、降低死亡率的关键。肺部 CT 作为无创、高分辨率的影像学检查手段，能够清晰呈现肺部细微结构病变，是肺部疾病筛查与诊断的核心技术支撑。但传统肺部 CT 诊断高度依赖医师的临床经验，存在主观误差大、阅片效率低等局限，尤其在大规模筛查场景下，易因影像数据海量、病变特征复杂（如早期微小病灶）导致漏诊、误诊。随着深度学习技术在医学影像领域的深度渗透，基于人工智能的肺部 CT 影像分析方法应运而生，为实现病变的自动化、精准化检测与分割提供了新方向，有效缓解医师诊疗压力、提升诊断效率，契合当前精准医疗发展需求，成为医学影像智能诊断领域的研究重点与前沿方向。
## 📌 2、数据集概览

| 项目        | 内容                                                                 |
| --------- | ------------------------------------------------------------------ |
| **数据集名称** | 肺部CT数据集                                                            |
| **任务类型**  | 目标检测（Object Detection）                                             |
| **类别**    | "tumor", "normal"                                                  |
| **标注格式**  | YOLO TXT 格式（`<类别> <中心x> <中心y> <宽度> <高度>`，坐标和尺寸均为相对于图像宽高的归一化值（0-1）） |
| **图片总数**  | 10000                                                              |
| **标注总数**  | 10109                                                              |

## 🗂 3、数据详情

| 类别ID | 类别名称 | 图片数量 | 标注数量 |
|--------|----------|----------|----------|
| 0 | UAV | 9997 | 10109 |
| **总计** | **-** | **10000** | **10109** |
## ✨ 4、效果演示

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20260102233425.png)



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
# number of classes
nc: 2
# class names
names: ["tumor", "normal"]
# paths
train: data/images/train  # 训练集图片路径
val: data/images/val      # 验证集图片路径
test: data/images/test    # 测试集图片路径
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

