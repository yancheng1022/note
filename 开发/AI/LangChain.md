# 1、基本概念
## 1.1、什么是LangChain
LangChain是 2022年10月 ，由哈佛大学的 Harrison Chase （哈里森·蔡斯）发起研发的一个开源框架，
用于开发由大语言模型（LLMs）驱动的应用程序。
比如，搭建“智能体”（Agent）、问答系统（QA）、对话机器人、文档搜索系统、企业私有知识库等。

LangChain ≠ LLMs
LangChain 之于 LLMs，类似 Spring 之于 Java

顾名思义，LangChain中的“Lang”是指language，即⼤语⾔模型，“Chain”即“链”，也就是将⼤模型与外部数据&各种组件连接成链，以此构建AI应⽤程序。

LangChain 是一个帮助你构建 LLM 应用的 全套工具集 。这里涉及到prompt 构建、LLM 接入、记忆管理、工具调用、RAG、智能体开发等模块

![image.png|800](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250916123739.png)

## 1.2、大模型应用开发框架

![image.png|800](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250916123840.png)

LangChain ：这些工具里出现最早、最成熟的，适合复杂任务分解和单智能体应用

LlamaIndex ：专注于高效的索引和检索，适合 RAG 场景。（注意不是Meta开发的）

LangChain4J ：LangChain还出了Java、JavaScript（LangChain.js）两个语言的版本，LangChain4j的功能略少于LangChain，但是主要的核心功能都是有的。

SpringAI/SpringAI Alibaba ：有待进一步成熟，此外只是简单的对于一些接口进行了封装

SemanticKernel ：也称为sk，微软推出的，对于C#同学来说，那就是5颗星

## 1.3、为什么需要LangChain

在大语言模型（LLM）如 ChatGPT、Claude、DeepSeek 等快速发展的今天，开发者不仅希望能“使用”这些模型，还希望能 将它们灵活集成到自己的应用中 ，实现更强大的对话能力、检索增强生成（RAG）、工具调用（Tool Calling）、多轮推理等功能。LangChain 为更方便解决这些问题，而生的。比如：大模型默认不能联网，如果需要联网，用langchain

## 1.4、架构设计

![image.png|700](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250916124904.png)

图中展示了LangChain生态系统的主要组件及其分类，分为三个层次：架构(Architecture)、组件(Components)和部署(Deployment)。

### 1.4.1、LangChain
langchain：构成应用程序认知架构的Chains，Agents，Retrieval strategies等
>构成应⽤程序的链、智能体、RAG

langchain-community：第三方集成⽐如：Model I/O、Retrieval、Tool & Toolkit；合作伙伴包 langchain-openai，langchain-anthropic等。

langchain-Core：基础抽象和LangChain表达式语言 (LCEL)

小结：LangChain，就是AI应用组装套件，封装了一堆的API。langchain框架不大，但是里面琐碎的知识点特别多。就像玩乐高，提供了很多标准化的乐高零件（比如，连接器、轮子等)

### 1.4.2、LangGraph
LangGraph可以看做基于LangChain的api的进一步封装，能够协调多个Chain、Agent、Tools完成更复杂的任务，实现更高级的功能。

### 1.4.3、LangSmith
链路追踪。提供了6大功能，涉及Debugging (调试)、Playground (沙盒)、Prompt Management (提示管理)、Annotation (注释)、Testing (测试)、Monitoring (监控)等。与LangChain无缝集成，帮助你从原型阶段过渡到生产阶段

### 1.4.4、LangServe

将LangChain的可运行项和链部署为REST API，使得它们可以通过网络进行调用。Java怎么调用langchain呢？就通过这个langserve。将langchain应用包装成一个rest api，对外暴露服务。同时，支持更高的并发，稳定性更好

>LangChain当中，最有前途的两个模块就是：LangGraph，LangSmith。LangChain能做RAG，其它的⼀些框架也能做，而且做的也不错，⽐如LlamaIndex。所以这时候LangChain要在Agent这块发⼒，那就需要LangGraph。而LangSmith，做运维、监控。故⼆者是LangChain⾥最有前途的

# 2、大模型应用开发
## 2.1、基于RAG架构的开发
背景：大模型的知识冻结、大模型幻觉。而RAG就可以非常精准的解决这两个问题。