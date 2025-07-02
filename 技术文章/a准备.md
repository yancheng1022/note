# 并发相关
https://zhuanlan.zhihu.com/p/136422134

# 1、项目
煤矿AI视频辅助风险识别系统，主要功能是利用目标检测技术对煤矿井下，井面安全实现实时监控。包含未佩戴安全帽，离岗，人员入侵，烟雾，火灾这些场景的监测。用到的技术栈就是springboot，mybatis，springcloud alibaba，目标检测技术yolo这一套。然后我主要负责的就是数据集的整理标注，模型训练，然后布控，模型推理，生成报警等功能的开发。

数据预处理阶段增加了低光照增强算法（如Retinex）


1、技术栈公司要求java，yolo目标检测推理这个开源的可以参考的代码基本都是python，c++实现的。所以花了一些时间去研究java下的推理功能
2、4090,24G显存，开始支持 十几路，经过优化后。支持大概60路视频同时进行推理 （1）多线程异步处理，其实一开始就有多线程，但只是是对于一个布控任务，后来在测试过程中发现 拉流和生成告警数据都是比较耗时的操作，所以减小了线程的粒度，拉流，推理，生成告警分别都异步处理。（2）在业务层面上进行优化，因为很多场景在，比如离岗检测，1s30帧，没必要30帧都进行推理，可能1s推理1帧就够了，但对于皮带异物检测，皮带动的很快，需要30帧都做检测，所以设计了帧间隔的配置字段，可以灵活配置帧间隔，避免了无意义的推理


# 2、基础

# 3、spring全家桶、mybatis
## 3.1、bean生命周期
1、实例化：解析配置为BeanDefinition对象，通过反射创建实例
2、属性赋值：Spring 将值和bean的引用注入到bean对应的属性中
3、回调实现Aware接口的方法。BeanNameAware，BeanFactoryAware，ApplicationContextAware对应的方法
4、初始化（Initialization）：分别调用（1）BeanPostProcessor的前置处理器，（2）InitialzingBean的afterPropertiesSet（）方法，（3）调用init初始化方法（4）调用BeanPostProcessor的后置处理器
5、使用（In Use）：在初始化完成之后，Bean就可以被使用了。
6、销毁（Destruction）：当Spring容器关闭时，会销毁所有的Bean。在销毁Bean之前，（1）如果实现DisposableBean接口，Spring将调用它的destory()接口方法（2）如果bean使用destroy-method声明了销毁方法，该方法也会被调用


# 4、spring cloud

# 5、mybatis


# 6、数据库

## 6.1、mysql架构
分客户端，server层，存储层


![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250702141645.png)
