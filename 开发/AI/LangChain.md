# 1、基本概念
## 1.1、什么是LangChain
LangChain是 2022年10月 ，由哈佛大学的 Harrison Chase （哈里森·蔡斯）发起研发的一个开源框架，
用于开发由大语言模型（LLMs）驱动的应用程序。
比如，搭建“智能体”（Agent）、问答系统（QA）、对话机器人、文档搜索系统、企业私有知识库等。

LangChain ≠ LLMs
LangChain 之于 LLMs，类似 Spring 之于 Java

顾名思义，LangChain中的“Lang”是指language，即⼤语⾔模型，“Chain”即“链”，也就是将⼤模型与外部数据&各种组件连接成链，以此构建AI应⽤程序。

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