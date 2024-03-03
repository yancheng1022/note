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

- 1-bit不用于生成ID(符号位) long 范围[-2^(64-1), 2^(64-1) ] , (64-1)中的1代表的就是符号位
- 41-bit时间戳(毫秒)可以表示1 x 2^41 / (1000 x 3600 x 24 x 365) = 69年的时间
- 10-bit可以分别表示1 x 2^10 = 1024台机器,范围[0,1023]
- 12-bit表示1ms内自动递增的序列号，1 x 2^12 = 4096个 范围[0,4095]。单机1ms可以生成4096个不重复的ID