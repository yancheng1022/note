
# 1、安装anaconda

下载anaconda，启动这个软件，新建一个虚拟环境：

地址： https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-4.0.0-Windows-x86_64.exe

# 2、环境配置

```shell
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple/
pip config set install.trusted-host pypi.tuna.tsinghua.edu.cn
pip install yolo
pip install ultralytics
pip install labelimg
```

# 3、新建训练

新建文件夹，名称叫做 yolotrain,在 yolotrain 新建labels和images文件夹，在images文件夹新建test,train,val三个文件夹，在labesl下面新建train,val两个文件夹

> images文件夹下面的test,train,val三个文件夹，全部存放相同的要训练的图片(测试，训练，验证)
> labels下面的train,val两个文件夹存放的是用labelimg标注的标签数据文件