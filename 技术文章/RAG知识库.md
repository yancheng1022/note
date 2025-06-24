# 1、部署

## 1.1、docker

由于Centos7自带的yum源中的Docker版本过低，默认的版本1.13.1

```shell
docker version
# 1、删除docker
yum remove docker docker-common docker-client
# 2、安装相关依赖
yum install -y yum-utils device-mapper-persistent-data lvm2
# 3、设置yum源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# 4、查看可用docker版本
yum list docker-ce --showduplicates | sort -r
# 5、安装docker
yum -y install docker-ce-25.0.3-1.el7
# 6、启动docker
systemctl start docker
# 7、设置开机启动
systemctl enable docker
```

## 1.2、cuda
1、https://developer.nvidia.com/cuda-12-4-0-download-archive
获取runfile命令

2、执行sh cuda_12.4.0_550.54.14_linux.run
在执行run脚本时会出现安装的选项，如果你已经安装cuda driver了则把安装选项中的cuda driver勾掉即可，无需重复安装

2、修改环境变量
vim ~/.bashrc
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-12.4/lib64
export PATH=$PATH:/usr/local/cuda-12.4/bin
source ~/.bashrc

## 1.3、nvidia-container-toolkit
vi  /etc/yum.repos.d/nvidia-container-toolkit.repo

```shell
[root@localhost docker]# cat /etc/yum.repos.d/nvidia-container-toolkit.repo
[nvidia-container-toolkit]
name=nvidia-container-toolkit
baseurl=https://mirrors.ustc.edu.cn/libnvidia-container/stable/rpm/$basearch
repo_gpgcheck=1
gpgcheck=0
enabled=1
gpgkey=https://mirrors.ustc.edu.cn/libnvidia-container/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
    
[nvidia-container-toolkit-experimental]
name=nvidia-container-toolkit-experimental
baseurl=https://mirrors.ustc.edu.cn/libnvidia-container/experimental/rpm/$basearch
repo_gpgcheck=1
gpgcheck=0
enabled=0
gpgkey=https://mirrors.ustc.edu.cn/libnvidia-container/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
```

 sudo yum clean all
 sudo yum install -y nvidia-container-toolkit


## 1.4、ollama

```shell
docker run -d --gpus=all -p 11434:11434 -v /home/lantrack/ollama:/root/.ollama --name ollama ollama/ollama

docker exec -it ollama bash
```


## 1.5、attu(Milvus客户端)

```shell
docker run -d --name attu -p 18000:3000 \
  -e MILVUS_URL=host.docker.internal:19530 \
  --add-host=host.docker.internal:host-gateway zilliz/attu:v2.3.8
```

docker compose -f docker-compose.yml down -v

docker compose -f docker-compose.yml up -d

# 2、RAG学习

## 2.1、基本概念

### 2.1.1、为什么使用rag
我们的需求：绝对的隐私保护和个性化知识库构建
场景：比如如果你希望大模型能根据你们企业的规章制度来回答问题，那么首先你肯定需要将这些规章制度或者模型作为附件上传，但你仍然可能面临一些别的
1、直接使用网页AI对话模型问题：
- 一个是数据隐私问题；联网使用大模型，所有数据均会上传到DeepSeek服务器，数据隐私性无法得到保证；
- 第二个是上传文件的限制问题；一般来说网页版AI对于文件上传数量是有限制的，因为解析附件需要额外的算力；即使你是付费用户，有时你想要上传的文件不是一个两个，而是几百个文件，也就是一个专业领域的知识库，模型同样可能无力支持；
- 第三个问题是上传文件附件方式使用起来繁琐：仅仅只是将文件作为附件加入对话上下文，每一次想要让模型根据这些附件回答问题的时候，都需要重新上传附件；想要新增、删除、修改已有的附件，同样也是很难实现的；

2、解决：
（1）隐私保护
- 通过对话大模型（如DeepSeek）的本地部署解决隐私问题；

（2）个性化知识库构建
使用RAG技术（Retrieval-Augmented Generation，检索增强生成）构建个人知识库。为此我们需要：
- 本地部署RAG技术所需要的开源框架RAGFlow；
- 本地部署Embedding大模型（或者直接部署自带Embedding模型的RAGFlow版本）；

### 2.1.2、RAG和模型微调的区别

大模型不知道你的这些私有知识，当它回答自己不知道的问题时会出现“幻觉”问题（输出错误虚假内容）；

微调技术和RAG技术的区别：
微调：在已有的预训练模型基础上，再结合特定任务的数据集进一步对其进行训练，使得模型在这一领域中表现更好（微调是考前复习，模型通过训练，消化吸收了这些知识然后给你回复）；

RAG：在生成回答之前，通过信息检索从外部知识库中查找与问题相关的知识，增强生成过程中的信息来源，从而提升生成的质量和准确性。（RAG是开卷考试，模型看到你的问题，开始翻你的知识库，以实时生成更准确的答案）；

RAG（Retrieval-Augmented Generation）技术原理：
（1）检索（Retrieval）：当用户提出问题时，系统会从外部的知识库中检索出与用户输入相关的内容。
（2）增强（Augmentation）：系统将检索到的信息与用户的输入结合，扩展模型的上下文。这让生成模型（也就是Deepseek）可以利用外部知识，使生成的答案更准确和丰富。
（3）生成（Generation）：生成模型基于增强后的输入生成最终的回答。它结合用户输入和检索到的信息，生成符合逻辑、准确且可读的文本内容。

### 2.1.3、什么是Embedding

文本是由自然语言组成的，这种格式不利于机器直接计算相似度。embedding 模型要做的，就是将自然语言转化为高维向量，然后通过向量来捕捉到单词或句子背后的语义信息。embedding 会将单词映射到相近的向量空间中；这样通过embeding，机器就可以学习到自然语言之间的深度语义关系，并且依此计算出不同文本之间的相似关系了；


## 2.2、RAG架构流程

![](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250610172247.png)

首先将资料文字向量化（就是将文字转化为一堆数字），存储到向量数据库。后续在进行检索的时候，将问题进行向量化，根据向量的相似度在向量库进行检索。然后调用大模型生成结果


# 3、RAG方案

1、fastgpt和ragflow都不支持外挂数据库，dify支持（但对外部知识库的api格式有要求，fastgpt和ragflow都不满足）
2、三个项目都提供api，知识库操作相关接口，但格式都不一致

## 3.1、fastgpt

### 3.1.2、修改知识库信息

docker exec -it 681 /bin/bash
mongo --username root --password 123456 --authenticationDatabase admin
use fastgpt
db.getCollectionNames()
db.datasets.find().pretty()

db.datasets.find({"name":"1"}).pretty()

db.datasets.updateOne(
  { name: "演示test" },
  { $set: { vectorModel: "bge-m3" } }
)
## 3.2、ragflow
