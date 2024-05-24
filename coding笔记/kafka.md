# 1、概述

## 1.1、介绍

kafka在设计之初的时候 开发人员们在除了消息中间件以外，还想吧kafka设计为一个能够存储数据的系统，有点像常见的非关系型数据库，比如说NoSql等。除此之外 还希望kafka能支持持续变化，不断增长的数据流, 可以发布 和订阅数据流，还可以对于这些数据进行保存

也就是说kafka的本质 是一个数据存储平台，流平台 ， 只是他在做消息发布，消息消费的时候我们可以把他当做消息中间件来用。

而且kafka在设计之初就是采用分布式架构设计的，基于集群的方式工作，且可以自由伸缩，所以 kafka构建集群非常简单

## 1.2、基础架构

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20240524141407.png)

1、Producer：Producer即生产者，消息的产生者，是消息的入口。
2、Broker：Broker是kafka实例，每个服务器上有一个或多个kafka的实例，我们姑且认为每个broker对应一台服务器。每个kafka集群内的broker都有一个不重复的编号，如图中的broker-0、broker-1等……
3、Topic：消息的主题，可以理解为消息的分类，kafka的数据就保存在topic。在每个broker上都可以创建多个topic。
4、Partition：Topic的分区，每个topic可以有多个分区，分区的作用是做负载，提高kafka的吞吐量。同一个topic在不同的分区的数据是不重复的，partition的表现形式就是一个一个的文件夹
5、Replication:每一个分区都有多个副本，副本的作用是做备胎。当主分区（Leader）故障的时候会选择一个备胎（Follower）上位，成为Leader。在kafka中默认副本的最大数量是10个，且副本的数量不能大于Broker的数量，follower和leader绝对是在不同的机器，同一机器对同一个分区也只可能存放一个副本（包括自己）。
6、Message：每一条发送的消息主体。
7、Consumer：消费者，即消息的消费方，是消息的出口。
8、Consumer Group：我们可以将多个消费组组成一个消费者组，在kafka的设计中同一个分区的数据只能被消费者组中的某一个消费者消费。同一个消费者组的消费者可以消费同一个topic的不同分区的数据，这也是为了提高kafka的吞吐量！
9、Zookeeper：kafka集群依赖zookeeper来保存集群的的元信息，来保证系统的可用性。

> kafka2.8.0后也可以配置不采用zk

# 2、安装kafka

## 2.1、linux安装

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