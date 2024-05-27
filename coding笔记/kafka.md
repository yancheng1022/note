---
title: kafka
categories:
  - coding
tags:
  - kafka
  - 大数据
  - 消息队列
date: 2024/05/25
---

# 1、概述

## 1.1、介绍

Kafka是由LinkedIn公司开发的一款分布式的流数据处理系统，它是一个高可靠、高吞吐量（每秒百万级别）、低延迟的分布式发布订阅消息系统。Kafka最初是为解决LinkedIn的大规模实时数据处理需求而设计的，后来被Apache基金会接收并成为一个开源项目（主要的开发语言是 Java 和 Scala）

## 1.2、设计目标

1、高吞吐率：在廉价的商用机上支持每秒100w数据的读写
2、消息持久化：所有消息均被持久化到磁盘，无消息丢失，支持消息重放
3、完全分布式：producer，broker，consumer均支持水平拓展
4、同时适应在线流处理和离线批处理

## 1.3、基础架构

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20240524141407.png)

1、Producer：Producer即生产者，消息的产生者，是消息的入口。
2、Broker：Broker是kafka实例，每个服务器上有一个或多个kafka的实例，我们姑且认为每个broker对应一台服务器。每个kafka集群内的broker都有一个不重复的编号，如图中的broker-0、broker-1等……
3、Topic：消息的主题，可以理解为消息的分类，kafka的数据就保存在topic。在每个broker上都可以创建多个topic。
4、Partition：Topic的分区，每个topic可以有多个分区，分区的作用是做负载，提高kafka的吞吐量。同一个topic在不同的分区的数据是不重复的，partition的表现形式就是一个一个的文件夹
5、Replication:每一个分区都有多个副本，副本的作用是做备胎。当主分区（Leader）故障的时候会选择一个备胎（Follower）上位，成为Leader。在kafka中默认副本的最大数量是10个，且副本的数量不能大于Broker的数量，follower和leader绝对是在不同的机器，同一机器对同一个分区也只可能存放一个副本（包括自己）。
6、Message：每一条发送的消息主体。
7、Consumer：消费者，即消息的消费方，是消息的出口。
8、Consumer Group：我们可以将多个消费组组成一个消费者组，在kafka的设计中同一个分区的数据只能被消费者组中的某一个消费者消费。同一个消费者组的消费者可以消费同一个topic的不同分区的数据，这也是为了提高kafka的吞吐量
9、Zookeeper：kafka集群依赖zookeeper来保存集群的的元信息，来保证系统的可用性。

> kafka2.8.0后也可以配置不采用zk

# 2、安装kafka

## 2.1、单机版安装

1、下载地址：https://kafka.apache.org/downloads，到/opt/software
2、解压：tar -zxvf kafka_2.12-3.7.0.tgz -C /opt/module/
3、修改名称： mv kafka_2.12-3.7.0/ kafka
4、修改config/server.properties


```properties
# 注意要保证每个节点brokerId不同
broker.id=0
# 日志存放目录
log.dirs=/opt/module/kafka/datas
# zk地址
zookeeper.connect=localhost:2181
```

5、配置环境变量

```
vi /etc/profile
# 添加内容
export KAFKA_HOME=/opt/module/kafka/
export PATH=$KAFKA_HOME/bin:$PATH
# 刷新配置
source /etc/profile
```

6、安装zookeeper（过程不再记录）
7、启动kafka

```
bin/kafka-server-start.sh -daemon config/server.properties
```

8、查看是否安装成功


![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20240524160039.png)

# 3、kafka的操作

## 3.1、主题（topic）

```shell
# 帮助命令
kafka-topics.sh --help
# 查看主题列表
kafka-topics.sh --list --bootstrap-server localhost:9092
# 创建主题
kafka-topics.sh --create --bootstrap-server localhost:9092 --topic yc --replication-factor 1 --partitions 1
```
