
1、首先是知识库一些基础知识学习，之前没接触过这块内容
2、尝试在本地部署ragflow+deepseek模型。尝试用了不同的分段方案，效果不如刘奇哥测试文档
3、首先是看现有ragflow怎么去优化一下 + 尝试去了解了一些别的方案  Gemini2.5Pro

# 1、基本概念
我们的需求：绝对的隐私保护和个性化知识库构建
场景：比如如果你希望大模型能根据你们企业的规章制度来回答问题，那么首先你肯定需要将这些规章制度或者模型作为附件上传，但你仍然可能面临一些别的1、直接使用网页AI对话模型问题：
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

# 2、RAG和模型微调的区别

大模型不知道你的这些私有知识，当它回答自己不知道的问题时会出现“幻觉”问题（输出错误虚假内容）；
微调技术和RAG技术的区别：

微调：在已有的预训练模型基础上，再结合特定任务的数据集进一步对其进行训练，使得模型在这一领域中表现更好（微调是考前复习，模型通过训练，消化吸收了这些知识然后给你回复）；

RAG：在生成回答之前，通过信息检索从外部知识库中查找与问题相关的知识，增强生成过程中的信息来源，从而提升生成的质量和准确性。（RAG是开卷考试，模型看到你的问题，开始翻你的知识库，以实时生成更准确的答案）；

RAG（Retrieval-Augmented Generation）技术原理：
（1）检索（Retrieval）：当用户提出问题时，系统会从外部的知识库中检索出与用户输入相关的内容。
（2）增强（Augmentation）：系统将检索到的信息与用户的输入结合，扩展模型的上下文。这让生成模型（也就是Deepseek）可以利用外部知识，使生成的答案更准确和丰富。
（3）生成（Generation）：生成模型基于增强后的输入生成最终的回答。它结合用户输入和检索到的信息，生成符合逻辑、准确且可读的文本内容。

# 3、什么是Embedding

检索（Retrieval）：当用户提出问题时，系统会从外部的知识库中检索出与用户输入相关的内容。
1、准备外部知识库：外部知识库可能来自本地的文件、搜索引擎结果、API 等等。这样，RAG 不仅依赖于模型本身的内在的、通过训练得到的知识，还能实时调用外部的信息进行补充。我们要构建的就是一个基于本地文件的外部知识库；
2、通过 embedding 模型，对知识库文件进行解析：文本是由自然语言组成的，这种格式不利于机器直接计算相似度。embedding 模型要做的，就是将自然语言转化为高维向量，然后通过向量来捕捉到单词或句子背后的语义信息。embedding 会将单词映射到相近的向量空间中；而深度学习会被映射到离他们较远的向量空间中，这样通过embeding，机器就可以学习到自然语言之间的深度语义关系，并且依此计算出不同文本之间的相似关系了；
3、通过 embedding 模型，对用户的提问进行处理：用户的输入同样会经过嵌入（Embedding）处理，生成一个高维向量。
拿用户的提问去匹配本地知识库：使用这个用户输入生成的这个高纬向量，去查询知识库中有无相关的文档片段。系统会利用某些相似度度量（如余弦相似度）去判断这个相似度。这一整个过程可以理解为，上传并解析了知识库之后，相当于给知识库中每一个小的文段都生成了一个指纹；然后用户输入问题之后，这个问题同样也会生成一个独一无二的指纹，接着RAGFLOW系统就会拿着用户输入的这个指纹，在指纹库也就是知识库中匹配，找到相似的指纹，然后把将检索到的这些相关文段与用户的输入结合，扩展模型的上下文，再喂给对话模型DeepSeek。

模型的分类：Chat模型、Embedding模型；

# 4、本地部署流程

## 4.1、下载ollama

通过ollama将DeepSeek模型下载到本地运行
 1、下载ollama平台
 https://ollama.com/
 ollama是一个用于本地运行和管理大语言模型（LLM）的工具。

2、环境变量配置
（1）OLLAMA_HOST=0.0.0.0:11434：让虚拟机里的程序能访问本机上运行的 Ollama 模型（因为RAGFlow是部署在虚拟机里的，默认情况下，Ollama 只能允许本机访问（监听 localhost:11434），其他设备（比如虚拟机）是无法连接的）
（2）OLLAMA_MODELS ：默认模型下载到C盘，如果希望下载到其他盘可以配置

>配置完环境变量后要重启电脑

3、通过ollama下载模型deepseek-r1:32b
ollama run deepseek-r1:32b；


## 4.2、docker部署RAGflow

1、下载RAGflow源代码
https://github.com/infiniflow/ragflow

2、安装docker

3、轻量版改为完整版
轻量版是不携带embedding模型，需要修改配置下载完整版
docker/.env文件

```
RAGFLOW_IMAGES=
```


![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250424095000.png)



3、利用提前编译好的docker镜像启动服务器
```
cd ragflow/docker
docker compose -f docker-compose.yml up -d
```

4、访问 localhost:80 

# 5、在RAGFlow中构建个人知识库

用户头像 - 模型提供商 - 添加ollama - 添加模型 
开启Hyper-V，ipconfig查看ip
cmd -ollama list可以看到安装的模型

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250424134728.png)
