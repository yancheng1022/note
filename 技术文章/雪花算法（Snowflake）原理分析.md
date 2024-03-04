---
title: 雪花算法（Snowflake）原理分析
date: 2023/11/15
categories:
  - coding
tags:
  - 技术文章
  - 雪花算法
  - snowflake
---
# 1、概念

雪花算法（Snowflake）是一种生成分布式全局唯一ID的算法，生成的ID称为Snowflake IDs或snowflakes。这种算法由Twitter创建，并用于推文的ID

雪花算法几个特性

- 生成的ID分布式唯一和按照时间递增有序，毫秒数在高位，自增序列在低位，整个ID都是趋势递增的。
- 不依赖数据库等三方系统，稳定性更高，性能非常高的。
- 可以根据自身业务特性分配bit位，非常灵活。

# 2、snowflake算法实现细节

## 2.1、拆解64bit位

snowflake生成的id通常是一个64bit数字，java中用long类型。

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202403031225315.png)


1、1bit-不用,因为二进制中最高位是符号位,1表示负数,0表示正数,生成的id一般都是用整数,所以最高位固定为0.
2、41bit-用来记录时间戳（毫秒）.41位可以表示2^41−1个数字，如果只用来表示正整数（计算机中正数包含0），可以表示的数值范围是：0 至 2^41-1，减1是因为可表示的数值范围是从0开始算的，而不是1。也就是说41位可以表示2^41-1个毫秒的值，转化成单位年则是:(2^41−1)/(1000∗60∗60∗24∗365)=69年 ，也就是说这个时间戳可以使用69年不重复（69年后换个服务器部署）
3、10bit-用来记录工作机器id。可以部署在2^10=1024个节点，包括5位datacenterId和5位workerId,可以部署在2^10=1024个节点
4、12bit-序列号，用来记录同毫秒内产生的不同id。12位（bit）可以表示的最大正整数是2^12−1=4095，即可以用0、1、2、3、....4095这4096个数字，来表示同一机器同一时间截（毫秒）内产生的4096个ID序号

> 同一毫秒的ID数量 = 1024 X 4096 = 4194304，所以最大可以支持单应用差不多四百万的并发量，这个妥妥的够用了


# 3、使用

## 3.1、引入依赖

```xml
<dependency>
    <groupId>com.github.beyondfengyu</groupId>
    <artifactId>snowflake-spring-boot-starter</artifactId>
    <version>1.0.0</version>
</dependency>

```

## 3.2、