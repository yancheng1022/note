1.显卡驱动查看 nvidia-smi 
2.yolo源码与模型 https://hub.nuaa.cf/ultralytics/ultralytics https://github.com/ultralytics/ultralytics 
3.anaconda和miniconda https://www.anaconda.com/download https://repo.anaconda.com/miniconda/Miniconda3-latest-Windows-x86_64.exe
环境变量：
```
C:\ProgramData\miniconda3 
C:\ProgramData\miniconda3\Scripts 
C:\ProgramData\miniconda3\Library\bin 
```


4.创建python环境 

```shell
conda create -n yolo python==3.8 
# 查看现有环境
conda env list
# 激活环境 
conda activate yolo
# 激活失败 （执行该命令后重新进入cmd）
conda init cmd.exe 
# 删除环境 
conda env remove -n yolo
```

5.安装库 

```shell
# 安装pytorch
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
# 安装pytorch cpu版本
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118 -i https://pypi.tuna.tsinghua.edu.cn/simple
# yolo8
pip install ultralytics==8.1 -i https://pypi.tuna.tsinghua.edu.cn/simple
# onnx
pip install onnx -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install onnxsim -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install onnxruntime -i https://pypi.tuna.tsinghua.edu.cn/simple
# 最新的yolo8改用默认GPU版的onnxruntime，要安装一下下面的库，否则ONNX转换会有警告
pip install onnxruntime-gpu -i https://pypi.tuna.tsinghua.edu.cn/simple
```

6、推理检测
```shell
yolo predict model=yolov8n.pt source=test.jpg
```


yolo detect train data=e:/yolotrain/test/dianzan.yaml model=e:/yolotrain/yolov8s.pt epochs=100 imgsz=640