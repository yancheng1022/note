# 1、项目

## 1.1、项目技术架构

![image.png|525](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202312091541151.png)

## 1.2、项目过程遇到的问题？怎么解决
业务方面的问题比较多，但说起来可能比较麻烦我就说一个技术方面的吧
有几张表数据量非常大，查询和插入开销较大，所以考虑分表。然后这块的工作是我独立做的
1.增加sharding的数据源，所以涉及到多个数据源，做了一个数据源动态切换
2.定时任务自动建表，该月27号创建下个月的分表，还需要考虑sharding配置的刷新，因为对于运行中的项目，sharing读取的分表是在一开始确定的，所以我这里通过反射去刷新shading的分表配置
3.历史数据重新分配

# 2、数据库
## 2.1、数据库优化方案

[11.5、sql优化的方案有哪些？](coding笔记/mysql.md#11.5、sql优化的方案有哪些？)

1.表结构优化。对经常使用的查询条件加索引
2.查询语句优化。使用explain分析工具
关注字段：
type 连接类型，访问类型，表示MySQL在访问表时所采取的方式
性能：性能： null > system/const > eq_ref > ref > ref_or_null   >  range > index >  all 
index：使用的索引
exart：额外信息  using fileSort


# 3、网络
3.1、三次握手四次挥手？为什么？


# 4、ssm

## 4.1、项目中怎么使用线程池

在Spring Boot中，可以使用Java的ThreadPoolExecutor类或Spring框架提供的ThreadPoolTaskExecutor类来创建和管理线程池。我们的项目使用的是spring框架的ThreadPoolTaskExecutor类
1. ThreadPoolTaskExecutor 配置类中配置参数
2. 