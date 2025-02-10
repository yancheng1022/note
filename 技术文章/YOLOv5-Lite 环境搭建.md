# 1、下载源码

https://github.com/ppogg/YOLOv5-Lite

# 2、安装环境

pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple


# 3、权重文件放到weights目录下

v5lite_s,复制到yolov5的文件夹

np.int 后面加_

.long()

nvidia-smi
nvcc --version


import torch  
print(torch.__version__)  
print(torch.version.cuda)  
print(torch.version)  
print("当前可用的GPU数量: ", torch.cuda.device_count())  
print(torch.cuda.is_available())