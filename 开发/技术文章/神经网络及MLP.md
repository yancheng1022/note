## 1、神经网络

大脑是由处理信息的神经元细胞和连接神经元的细胞进行信息传递的突触构成的。树突(Dendrites)从一个神经元接受电信号，信号在细胞核(Cell Body)处理后，然后通过轴突(Axon)将处理的信号传递给下一个神经元。

计算机处理信息类比为输入向量，通过加权求值线性得出，再经过非线性变换输出一个标量。如下图所示：

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250409105847.png)


神经网络的变种目前有很多，如误差反向传播（Back Propagation，BP）神经网路、概率神经网络、[卷积神经网络](https://zhida.zhihu.com/search?content_id=225739438&content_type=Article&match_order=1&q=%E5%8D%B7%E7%A7%AF%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C&zhida_source=entity)（Convolutional Neural Network ，CNN-适用于图像识别）、[长短记忆神经网络](https://zhida.zhihu.com/search?content_id=225739438&content_type=Article&match_order=1&q=%E9%95%BF%E7%9F%AD%E8%AE%B0%E5%BF%86%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C&zhida_source=entity)（Long short-term Memory Network ，LSTM-适用于语音识别），Attention类网络等。

神经网络中最简单的则是MLP（Multi-Layer Perception ，多层感知器）

多层感知机是人工神经网络的一种最简单形式，除了输入输出层，它中间可以有多个隐层，最简单的MLP只含一个隐层，即三层的网络结构。如下图所示：

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250409105922.png)


  

其中比较重要的部分为：前向传播，损失计算和反向传播。

**举例**：

结合上图，假设要对以下2维数据使用神经网络分类，这里分为三类。

  
![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250412203951.png)


  

## 2、前向传播

  下标：维度
  上标：层次

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250412203925.png)


  

前向计算过程中，主要包含中间 Layer2层（乘法，加法和激活函数）和Layer3（乘法，加法）层的计算，

xi(2)=σ(w1i(2)∗x1(1)+w2i(2)∗x2(1)+bi(2))

xi(3)=w1i(3)∗x1(2)+w2i(3)∗x2(2)+w3i(3)∗x3(2)+w4i(3)∗x4(2)+bi(3)

可以使用矩阵法则比较的简洁。将上面的例子一般化，并写成矩阵乘法的形式：

x(l)=W(l)∗x(l−1)+b(l)

另外上式子中使用了激活函数，常见的有 Sigmoid，ReLU，PReLU 等，这里使用了sigmoid。表达式如上图所示。

## 3、损失计算

损失函数定义：顾名思义，就是预测值和真实值之间的损失差距，一般用Loss来表示，差距越大，Loss越大，我们的优化的目标就是减小Loss。在反向求导和优化均基于损失函数对比产生，因此损失函数的重要性不言而喻。这里先介绍两个最常见的：

### 1 均方误差（Mean Squared Error )

均方误差损失也是一种比较常见的损失函数，其定义为：

其中：表示预测量，表示真实值MSE=1N∑i=1N(pi−yi)2;其中：pi表示预测量，yi表示真实值

  

MSE 是 ML 回归模型（例如线性回归）中常用的损失函数。

### 2 [交叉熵损失函数](https://zhida.zhihu.com/search?content_id=225739438&content_type=Article&match_order=1&q=%E4%BA%A4%E5%8F%89%E7%86%B5%E6%8D%9F%E5%A4%B1%E5%87%BD%E6%95%B0&zhida_source=entity)（Cross Entropy Loss Function）

**① 二分类问题**

在二分的情况下，模型最后需要预测的结果只有两种情况，对于每个类别我们的预测得到的概率为 p, 1-p。

L2=1N∑i=1N−[yi∗ln(pi)+(1−yi)∗ln(1−pi)]

其中：

：表示第个的标签，一般地，正例为，负例为表示第个样本预测为正例的概率。yi：表示第i个的标签，一般地，正例为1，负例为0pi:表示第i个样本预测为正例的概率。

**② 多分类问题**

多分类的情况实际上就是对二分类的扩展：

L2=1N∑i=1N∑c=1C−yic∗ln(pic)

：表示类别的数量；表示第个样本类别的真实类别，真实取，否则取，一般只有一个，及表示预测的第个属于类别的概率。C：表示类别的数量；yic:表示第i个样本类别的真实类别，真实取1，否则取0，一般只有一个1，及∑cyic=1pic:表示预测的第i个属于类别c的概率。

在训练的过程中，就是不断降低loss，以达到提升精度的目的。

**举个简单的例子：**

假设我们进行3类的类被进行训练，它们的 label 依次为：[1,0,0]， [0,1,0]， [0,0,1]；一个数据的输出和 label为：[0.3,0.4,0.3] , [1,0,0]

- 利用交叉熵计算 loss = −(1∗log(0.3)+0+0)=1.20
- 随着训练次数的增加，模型的参数得到优化，若输出变成：[0.8,0.1,0.1]，则 loss=−(1∗log(0.8)+0+0)=0.22

loss由1.20减小为0.22，而判断输入数据为1的概率由原本的0.3增加为0.8，说明训练得到的概率分布越来越接近真实的分布，这样就能提高预测的准确性。

**③ 交叉熵损失函数验证**

```python3
import torch
import torch.nn.functional as F
import torch.nn as nn
input = torch.randn(2, 3)
target = torch.tensor([0, 2])

# 使用公式来计算
one_hot = F.one_hot(target).float() # 对标签进行one_hot编码
p = torch.exp(input)/torch.sum(torch.exp(input), dim = 1).reshape(-1, 1)
loss_com = -torch.sum(one_hot * torch.log(p)) / target.shape[0]


# 下面用 torch.nn.function 实现
log_softmax = F.log_softmax(input, dim = 1)
loss_func = F.nll_loss(log_softmax, target) # 无需对标签做 one_hot 编码


# 最后我们直接用 torch.nn.CrossEntropyLoss 验证
loss_entropy = F.cross_entropy(input, target)

print(f"input:\n{input} \n")
print(f"target:\n{target} \n")    
print(f"one_hot:\n{one_hot} \n")
print(f"p(softmax(input)):\n{p} \n")   
print(f"log_softmax:\n{log_softmax}\n")
print(f"loss_com:{loss_com}; \nloss_func:{loss_func};\n loss_entropy:{loss_entropy}")
```

结果如下图：

  

![](https://pic3.zhimg.com/v2-2ed8fef00223013097cbe300b4c353d2_1440w.jpg)

  

不难发现pytorch中定义的交叉熵损失函数就是由表达式计算得来，只是中间进行了套娃调用。这一点可以通过源码进行阅读，而且期间还进行了变更，1.6版本使用了

**④ 分类问题为何不用MSE**

- 分类问题中label的值大小在欧氏空间中是没有意义的。所以分类问题不能用mse作为损失函数。
- MSE（均方误差）对于每一个输出的结果都非常看重(让正确分类变大的同时，也让错误分类变得平均)，而交叉熵只对正确分类的结果看重。
- mse已经是非凸函数了，有多个极值点

## 4、反向传播

### 1 权重更新

  

![](https://pic1.zhimg.com/v2-cce0d058c0fa804c18460dd73d19de60_1440w.jpg)

wjk(i)(new)=wjk(i)(old)−lr∗∂L∂wjk(i)

∂L∂w11(3)=∂L∂p1∗∂p1∂x1(3)∗∂x1(3)∂w11(3)+∂L∂p2∗∂p2∂x1(3)∗∂x1(3)∂w11(3)+∂L∂p3∗∂p3∂x1(3)∗∂x1(3)∂w11(3)

  

以更新上述标红的部分为例进行说明。使用链式法则更新权重。涉及参数如下：

∂L∂pi=−yi∗1pi

其中：∂p1∂x1(3)=∂(ex1(3)ex1(3)+ex2(3)+ex3(3))∂x1(3)=ex1(3)∗(ex1(3)+ex2(3)+ex3(3))−ex1(3)∗ex1(3)(ex1(3)+ex2(3)+ex3(3))2=p1−p12其中：p1=ex1(3)ex1(3)+ex2(3)+ex3(3)

其中：∂p2∂x1(3)=∂(ex2(3)ex1(3)+ex2(3)+ex3(3))∂x1(3)=0−ex2(3)∗ex1(3)(ex1(3)+ex2(3)+ex3(3))2=−p1∗p2其中：p2=ex2(3)ex1(3)+ex2(3)+ex3(3)

同理：同理：∂p3∂x1(3)=−p1∗p3

∂L∂w11(3)=∂L∂p1∗∂p1∂x1(3)∗∂x1(3)∂w11(3)+∂L∂p2∗∂p2∂x1(3)∗∂x1(3)∂w11(3)+∂L∂p3∗∂p3∂x1(3)∗∂x1(3)∂w11(3)=−y1∗1p1∗(p1−p12)∗x1(2)+−y2∗1p2∗(−p1∗p2)∗x1(2)+−y3∗1p3∗(−p1∗p3)∗x1(2)=(−yi+y1∗p1+y2∗p1+y3∗p1)∗x1(2)

  

### 2 优化器

损失函数是评价指标，或者说我们要求网络改进的方向。优化器则是网络的优化策略，也即如何达到最优解。一个网传比较流行的的图例如下所示：

  

![动图封面](https://pic3.zhimg.com/v2-5d5166a3d3712e7c03af74b1ccacbeac_b.jpg)

  

可以看出一个合适的优化器可以加速优化。通常可分三类：

- [梯度下降](https://zhida.zhihu.com/search?content_id=225739438&content_type=Article&match_order=1&q=%E6%A2%AF%E5%BA%A6%E4%B8%8B%E9%99%8D&zhida_source=entity)
- 带[动量优化](https://zhida.zhihu.com/search?content_id=225739438&content_type=Article&match_order=1&q=%E5%8A%A8%E9%87%8F%E4%BC%98%E5%8C%96&zhida_source=entity)
- [自适应调整](https://zhida.zhihu.com/search?content_id=225739438&content_type=Article&match_order=1&q=%E8%87%AA%E9%80%82%E5%BA%94%E8%B0%83%E6%95%B4&zhida_source=entity)

下面单独来看看：

**梯度下降优化器**

**1）SGD：Stochastic Gradient Descent (随机梯度下降)**

单条数据就可对参数进行一次更新。每个epoch参数更新M（样本数）次，随机是指每次选取样本是随机的，每个epoch样本更新的顺序是随机的。

- 表达式：
- 其中为学习率，为时的梯度wt+1=wt−lr∗g(wt);其中:lr为学习率，g(wt)为t时的梯度

  

- 优点：参数更新速度快。
- 缺点：

- 由于每次参数更新时采用的数据量很小，造成梯度更新时震荡幅度大
- 容易受到异常值的影响，在最优解附近会有加大波动，但大多数情况都是向着梯度减小的方向。

**2）BGD：Batch Gradient Descent （批量梯度下降）**

BGD批量梯度下降参数更新原则：每次将所有样本的梯度求和，然后根据梯度和对参数进行更新，每个epoch参数更新1次。公式同SGD，只是更新的梯度不是单个，而是批量的求和。

由于每次参数更新时采用的数据量很大。有以下优缺点：

- 优点：梯度更新时很平滑
- 缺点：

- 造成参数更新速度慢，
- 内存消耗大，
- 因为梯度更新平滑随机性差，容易陷入局部最优解。

**3）MBGD：Mini-batch gradient descent （小批量梯度下降）**

MBGD批量梯度下降参数更新原则：每次将小批量样本的梯度求和，然后根据梯度和对参数进行更新，每个epoch参数更新多次。公式同SGD，只是更新的梯度不是单个，而是小批量的求和。（**而当使用 mini-batch 时，通常也使用术语 SGD** ）

- 优点：

- 降低了参数更新的方差，可以更稳定地收敛（与 SGD 相比较）
- 利用深度学习库对常见大小 min−batch 的矩阵进行高度优化的特性，可非常高效计算出其梯度。

- 缺点:不过 Mini-batch gradient descent 不能保证很好的收敛性

以上优化器均存在一个问题：就是收敛速度可能比较慢。因此借助物理惯性思路，提出动量概念。

**带动量优化**

**4）SGD+Momentum**

使用动量(Momentum)的随机梯度下降法(SGD)，主要思想是引入一个积攒历史梯度信息动量来加速SGD。

**理解：**由于当前权值的改变会受到上一次权值改变的影响，类似于小球向下滚动的时候带上了惯性。这样可以加快小球向下滚动的速度。比较形象化的理解如下：

  

![](https://pic3.zhimg.com/v2-f9c6f86a4e5bf70cb022e45b412f75da_1440w.jpg)

  

  

![](https://pic4.zhimg.com/v2-b13b428c9427482c7a79e1340509991b_1440w.jpg)

其中表示动量值，一般取，表示模型前面时刻梯度的平均值，初始，为学习率，为时的梯度vt=μvt−1−lr∗g(wt)wt+1=wt+vt;其中:∙μ表示动量值，一般取0.9，∙vt表示模型前面t时刻梯度的平均值，初始t0=0，∙lr为学习率，g(wt)为t时的梯度

  

**5）NAG：Nesterov accelerated gradient (牛顿加速梯度动量优化)**

拿着上一步的速度先走一小步，再看当前的梯度然后再走一步。也即取未来梯度进行更新。

wt=wt+μvt−1vt=μvt−1−lr∗g(wt)wt+1=wt+vt;

  

  

![](https://picx.zhimg.com/v2-8c5f624be854dc1a170a52178cb7e599_1440w.jpg)

  

**优点：** 梯度下降的方向更加准确

**缺点：** 对收敛率作用不是很大

**自适应调整**

**6）AdaGrad（自适应梯度）**

在我们训练模型的初期我们的学习率一般比较大，因为这时我们的位置离最优点比较远；当训练快结束时，我们通常会降低学习率，因为训练快结束时我们离最优点比较近，这时使用大的学习率可能会跳过最优点。Adagrad 能使得参数的学习率在训练的过程中越来越小，具体计算方法如下：

其中防止分母为，通常取，为学习率，通常取，为时的梯度wt+1=wt−lr∑tgt2+ϵ∗g(wt);其中:∙ϵ防止分母为0，通常取10−8，∙lr为学习率，通常取0.01，g(wt)为t时的梯度

  

- 优点：自动调节参数的学习率；
- 缺点：学习率下降会比较快，可能造成学习提早停止；

论文网址：[https://arxiv.org/pdf/1609.04747.pdf](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/1609.04747.pdf)

**7）AdaDelta**

Adadelta 对 Adagrad 做了轻微的修改，使其比 Adagrad 更加稳定，其公式如下：

其中防止分母为，通常取，表示前个梯度平方和的期望，也就是梯度平方和的指数加权平均为学习率，通常取，为时的梯度E[g2]t=βE[g2]t−1+(1−β)gt−12wt+1=wt−lrE[g2]t+ϵ∗g(wt);其中:∙ϵ防止分母为0，通常取10−8，∙E[g2]t表示前t个梯度平方和的期望，也就是梯度平方和的指数加权平均∙lr为学习率，通常取0.01，g(wt)为t时的梯度

Adadelta 把 Adagrad 分母中的梯度平方和换成了梯度平方的指数加权平均，这使得 Adadelta 学习率的下降速度没有 Adagrad 那么快。

**8）RMSprop（Root Mean Squre propogation）**

RMSprop 可以看做是 Adadelta 的一个特例，由Geoff Hinton发明。一般建议β取0.9，lr取0.001。

特点

- 其实RMSprop依然依赖于全局学习率
- 适合处理非平稳目标——对于RNN效果很好。
- RMSProp借鉴了Adagrad的思想，观察表达式，由于取了个加权平均，避免了学习率越来越低的的问题，而且能自适应地调节学习率。
- RMSProp算法在经验上已经被证明是一种有效且实用的深度神经网络优化算法。目前它是深度学习从业者经常采用的优化方法之一。

**9）Adam（ Adaptive Moment Estimation）自适应矩估计**

其可看作是 Momentum + RMSprop。Adam 使用梯度的指数加权平均（一阶矩估计）和梯度平方的指数加权平均（二阶矩估计）来动态地调整每个参数的学习率。

其中分别是梯度的指数加权平均（一阶矩估计）和梯度平方的指数加权平均（二阶矩估计）是期望的无偏估计通常设为，通常设为，防止分母为，通常取mt=βmt−1+(1−β)gtnt=γnt−1+(1−γ)ntmt^=mt1−βtnt^=nt1−γtwt+1=wt−lrnt^+ϵ∗mt^;其中:∙mt,nt分别是梯度的指数加权平均（一阶矩估计）和梯度平方的指数加权平均（二阶矩估计）∙mt^,nt^是期望的无偏估计∙β通常设为0.9，γ通常设为0.999，ϵ防止分母为0，通常取10−8

## 5、MLP 实验

### 1 代码简介

一共包含三个：

1. network.py ：定义网络模型
2. train.py ：加载数据集并训练，训练集计算loss，测试集计算accuracy，保存训练好的网络权重参数
3. inference.py：得到训练好的网络参数后，用自己找的图像进行分类测试

说明：后续大多数也是如此分布。

**1）network.py**

非常简单，就是如图所示的网络结构，输入2维度，中间4维，输出3维。

```python3
import torch.nn as nn
import torch
import torch.nn.functional as F


#  定义网络和模型 三层的网络
class MLP(torch.nn.Module):
    def __init__(self):
        super(MLP, self).__init__()
        self.n_hidden = nn.Linear(2, 4)
        self.out = nn.Linear(4, 3)

    def forward(self, x):
        x= self.n_hidden(x)
        x = torch.relu(x)
        x = self.out(x)
        return x
```

**2）train.py**

**① 总体介绍**

可以分成几个部：

- 导入包及参数设置
- 构造数据（实际应用中为加载输入数据）
- 训练（最重要的部分）
- 验证

部分代码如下：

```python3
def main(args):
    device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
    print("using {} device.".format(device))

    #  定义数据，这里是自己建立
    torch.manual_seed(1)
    n_data = torch.ones(1000, 2)  # 内容为一个 100 行 2 列 的 tensor
    x0 = torch.normal(1 * n_data, 1)
    y0 = torch.zeros(1000)
    x1 = torch.normal(-4 * n_data, 1)
    y1 = torch.ones(1000)
    x2 = torch.normal(5 * n_data, 1)
    y2 = torch.ones(1000) * 2
    x = torch.cat((x0, x1, x2)).type(torch.FloatTensor)
    y = torch.cat((y0, y1, y2)).type(torch.LongTensor)
    torch_dataset = tud.TensorDataset(x, y)

    net = MLP()
    net.to(device)

    # pata = list(net.parameters())
    optimizer = torch.optim.SGD(net.parameters(), lr=0.1)
    scheduler = torch.optim.lr_scheduler.StepLR(optimizer, step_size=5, gamma=0.1)  # 设置学习率下降策略
    criterion = nn.CrossEntropyLoss()

    best_acc = 0.0
    for epoch in range(args.epochs):
        # train
        net.train()
        running_loss = 0.0
        train_bar = tqdm(train_loader, file=sys.stdout)
        for step, train_data in enumerate(train_bar):
            train_x, labels = train_data
            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(train_x.to(device))
            loss = criterion(outputs, labels.to(device))
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if step % 2000 == 1999:  # print every 2000 mini-batches
                print(f'train_data:[{epoch + 1}, {step + 1:5d}] loss: {running_loss / 2000:.3f}')
                running_loss = 0.0

        scheduler.step()  # 更新学习率
       # print(scheduler.get_last_lr())

        # validate
        net.eval()
        acc = 0.0  # accumulate accurate number / epoch
        with torch.no_grad():
            val_bar = tqdm(val_loader, file=sys.stdout)
            for val_bar in val_bar:
                val_x, val_labels = val_bar
                outputs = net(val_x.to(device))
                predict_y = torch.max(outputs, dim=1)[1]
                acc += torch.eq(predict_y, val_labels.to(device)).sum().item()

        val_accurate = acc / val_size
        print(f'[epoch {epoch+1}] , val_accuracy: {val_accurate:.3f}')
        if val_accurate > best_acc:
            best_acc = val_accurate
            torch.save(net.state_dict(), args.save_path)
    print('Finished Training')


if __name__ == '__main__':
    args_ = parse_option()
    main(args_)
```

网络中很重要的权重更新部分：

![](https://picx.zhimg.com/v2-aae9bfe6b0874a28bf49561c2bbd952f_1440w.jpg)

  

**② 训练过程**

  

![](https://picx.zhimg.com/v2-5c36359bcd4c398d75f7bc1b50620e13_1440w.jpg)

  

图片来源于：寒武纪课程《[Cambricon PyTorch 的训练入门](https://link.zhihu.com/?target=https%3A//developer.cambricon.com/index/curriculum/details/id/40/classid/7.html)》：

**3）inference.py**

```python3
import torch
import torch.nn.functional as F
from network import MLP


def main():
    net = MLP()
    net.load_state_dict(torch.load('../../models/MLP.pth'))

    torch.manual_seed(2)
    n_data = torch.ones(1, 2)
    x0 = torch.normal(1 * n_data, 1)
    # y0 = torch.zeros(1)
        
    net.eval() 
    with torch.no_grad():
        outputs = net(x0)
        print(F.softmax(outputs, dim=1))
        predict = torch.max(outputs, dim=1)[1].numpy()
        print(predict)


if __name__ == '__main__':
    main()
```

与训练的区别是，不用计算损失和反向计算。

- net.eval() # 负责改变batchnorm、dropout的工作方式，如在eval()模式下，dropout是不工作的。
- torch.no_grad() 关闭方向计算

### 2 CPU或GPU

注意：

网络构建的代码没有写上softmax，因为：大部分框架在使用交叉熵损失函数时默认加上softmax，这样无论你的输出层是什么，只要用了nn.CrossEntropyLoss就默认加上了softmax。

代码运行：

```powershell
git clone https://gitee.com/yifanrensheng/deep_learning_for_cv.git
cd deep_learning_for_cv/1_classification/cv_c01_mlp
python train.py
python inference.py
```

### 3 国产芯片MLU上运行

```powershell
# 1 直接使用脚本转换，将得到对应的 c02_MLP_mlu
python /torch/src/catch/tools/torch_gpu2mlu.py --input ./c02_MLP/
cd c02_MLP_mlu
python train.py
python inference.py
```

内部会生成report.md，也即是改动的点，可以发现改动的部分很少。

```powershell
# Cambricon PyTorch Model Migration Report
## Cambricon PyTorch Changes
| No. |  File  |  Description  |
| 1 | train.py:3 | add "import torch_mlu" |
| 2 | train.py:22 | change "device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")" to "device = torch.device("mlu:0" if torch.mlu.is_available() else "cpu") " |
| 3 | network.py:2 | add "import torch_mlu" |
| 4 | inference.py:1 | add "import torch_mlu" |
```