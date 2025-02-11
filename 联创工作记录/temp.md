



![d325f7f2d21fced0aa91b8dbbbf4b20.jpg](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/d325f7f2d21fced0aa91b8dbbbf4b20.jpg)










nvidia-smi
nvcc --version


import torch  
print(torch.__version__)  
print(torch.version.cuda)  
print(torch.version)  
print("当前可用的GPU数量: ", torch.cuda.device_count())  
print(torch.cuda.is_available())

nvidia-smi
nvcc --version


import torch  
print(torch.__version__)  
print(torch.version.cuda)  
print(torch.version)  
print("当前可用的GPU数量: ", torch.cuda.device_count())  
print(torch.cuda.is_available())








大家好，我来汇报一下4015告警数据生成的方案。

第一个是本地模型训练和推理，第二个是重庆天策AI摄像头的方案。我们目前的4015系统暂时使用的是第一种方案。

第一个是本地模型的训练和推理，首先什么是模型的训练？在机器学习和深度学习领域中，模型训练是指利用已有的数据集来调整模型的参数，使其能够从数据中学习并做出预测或分类。在视觉模型训练中，通常会使用包含图像或视频数据的数据集来训练模型，以使模型能够识别、分类或做出其他相关任务。这个概念有点复杂，举一个简单的例子就是：小孩子一开始是不认识猫和狗的，家长给孩子看很多猫和狗的图片，告诉他这个是猫，这个是狗，然后让孩子重复的去学习和认识这些图片，孩子就会慢慢的记住猫和狗的特征，然后在真的遇到猫或狗时小孩就能根据特征推断出来是猫还是狗。然后上面说到的模型训练也是同理，只不过是小孩变成了机器。然后下面是模型训练需要做的一些工作。第一个是数据集的收集和准备。。。。。类似于上面例子中的猫和狗的图片。第二个是模型训练。。。。可以理解为上面例子中让小孩去反复的看这些猫和狗的图片。第三步是。。。第四步是。。。第五步是。。。 以上就是模型训练的一个步骤

然后下面介绍一下目前模型训练的进度和成果。目前已经训练好的模型有：。。。。。然后我们可以看到训练集+验证集的图片数量大概在1w左右。第三列和第四列是训练集和验证集图片数量和，可以看到大概在8000张左右。然后我们需要关注的一个重要的参数就是第6列的mpa50，它可以简单理解为代表模型的准确性，数值是在0-1之前，数值越大代表模型的准确性越高，然后我们可以看到安全帽和吸烟。。。。明火是在0.8。。。。烟雾稍低0.45（这可能和它的特征提取比较难有关，可以考虑优化数据集）

然后是模型训练的一些优缺点，。。。


![我们阿森纳是不可战胜的.jpg](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/1737523448010.jpg)
















