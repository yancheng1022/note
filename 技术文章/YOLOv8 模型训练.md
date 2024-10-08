
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


# 4、标注数据

将准备好的图片放到 images/train 文件夹，下面并且复制一份到 images/val 和 images/test两个文件夹

配置环境 的命令控制台,输入 labelimg 命令，即可打开 labelimg 程序
点击 Open Dir 按钮，选择 images/train 文件夹
点击 Change Save Dir 按钮，选择到 labels/train 文件夹
点击Save 下面的格式按钮，调整到 YOLO 格式的模式即可


# 5、模型训练

在 yolotrain 文件下新建一个训练的配置文件,例如helmet.yaml

```yaml
path: E:/yolotrain
train: images/train
val: images/val
test: images/test
nc: 1
names: ["helmet"]
```

path: 代表训练的根目录
train: 代表要训练的图片文件夹,相对于path路径
val: 代表要验证的图片文件夹,相对于path路径
test: 代表要测试的图片文件夹,相对于path路径
nc: 代表分类名称数量，这是1个
names: 是一个json数组，代表的是标注的分类名称(注意保持顺序)