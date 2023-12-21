# 1、项目

## 1.1、项目技术架构

![image.png|525](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202312091541151.png)

## 1.2、项目过程遇到的问题？怎么解决
业务方面的问题比较多，但说起来可能比较麻烦我就说一个技术方面的吧
有几张表数据量非常大，查询和插入开销较大，所以考虑分表。然后这块的工作是我独立做的
1.增加sharding的数据源，所以涉及到多个数据源，做了一个数据源动态切换
2.定时任务自动建表，该月27号创建下个月的分表，还需要考虑sharding配置的刷新，因为对于运行中的项目，sharing读取的分表是在一开始确定的，所以我这里通过反射去刷新shading的分表配置
3.历史数据重新分配


## 1.3、怎么做的登录
shiro

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
2. 使用@Async注解将doSomething()方法标记为异步执行的方法。Spring会自动使用注入的ThreadPoolTaskExecutor来执行带有@Async注解的方法

## 4.2、核心线程数可以为0吗

可以，如果设为0，那么当线程进入时，将会至少创建一个非核心线程进行处理，并且其他的线程进来时要看队列的情况，如果队列能装得下，那么就只用这一个非核心线程处理，一次一条，装不下，那么就会添加非核心线程，直到达到最大线程数

## 4.3、线程池状态

1. RUNNING状态：线程池创建后，初始状态为RUNNING。
2. SHUTDOWN状态：当调用线程池的shutdown()方法时，线程池进入SHUTDOWN状态。此时线程池不再接受新的任务，但会执行已经提交的任务。当所有任务都执行完毕后，线程池会转换到TIDYING状态。
3. STOP状态：当调用线程池的shutdownNow()方法时，线程池进入STOP状态。此时线程池不再接受新的任务，并且会中断正在执行的任务。当所有任务都执行完毕后，线程池会转换到TIDYING状态。
4. TIDYING状态：当线程池处于SHUTDOWN或STOP状态时，所有任务都执行完毕后，线程池会进入TIDYING状态。在此状态下，线程池会进行清理工作，如关闭线程池中的所有线程等。当清理工作完成后，线程池会转换到TERMINATED状态。
5. TERMINATED状态：线程池处于TERMINATED状态时，表示线程池已经完全终止，所有任务已经执行完毕并且清理工作也已经完成。此时线程池不再能接受新的任务。

## 4.4、中止一个运行中的线程

interrupt虽然是打断线程的方法，但不会立即结束线程运行，而是将线程的打断状态设置为true，在线程的isInterrupted方法来查看打断状态

## 4.5、
# 5、JUC
# 5.1、synchronized使用