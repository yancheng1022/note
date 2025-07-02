# 并发相关
https://zhuanlan.zhihu.com/p/136422134

# 1、项目
煤矿AI视频辅助风险识别系统，主要功能是利用目标检测技术对煤矿井下，井面安全实现实时监控。包含未佩戴安全帽，离岗，人员入侵，烟雾，火灾这些场景的监测。用到的技术栈就是springboot，mybatis，springcloud alibaba，目标检测技术yolo这一套。然后我主要负责的就是数据集的整理标注，模型训练，然后布控，模型推理，生成报警等功能的开发。

数据预处理阶段增加了低光照增强算法（如Retinex）


1、技术栈公司要求java，yolo目标检测推理这个开源的可以参考的代码基本都是python，c++实现的。所以花了一些时间去研究java下的推理功能
2、4090,24G显存，开始支持 十几路，经过优化后。支持大概60路视频同时进行推理 （1）多线程异步处理，其实一开始就有多线程，但只是是对于一个布控任务，后来在测试过程中发现 拉流和生成告警数据都是比较耗时的操作，所以减小了线程的粒度，拉流，推理，生成告警分别都异步处理。（2）在业务层面上进行优化，因为很多场景在，比如离岗检测，1s30帧，没必要30帧都进行推理，可能1s推理1帧就够了，但对于皮带异物检测，皮带动的很快，需要30帧都做检测，所以设计了帧间隔的配置字段，可以灵活配置帧间隔，避免了无意义的推理


# 2、基础
## 2.1、抽象类和接口的区别
| 特性        | 接口(Interface)                      | 抽象类(Abstract Class) |
| --------- | ---------------------------------- | ------------------- |
| **定义关键字** | `interface`                        | `abstract class`    |
| **方法实现**  | 1.8前不能有方法实现，Java 8+ 可以有默认方法default | 可以有具体方法和抽象方法        |
| **成员变量**  | 只能是 `public static final` 常量       | 可以是各种类型的成员变量        |
| **构造方法**  | 不能有                                | 可以有构造方法             |
| **多继承**   | 一个类可以实现多个接口                        | 一个类只能继承一个抽象类        |
| **设计目的**  | 定义行为规范/契约                          | 提供部分实现，作为基类         |

# 3、spring全家桶、mybatis
## 3.1、bean生命周期
1、实例化：解析配置为BeanDefinition对象，通过反射创建实例
2、属性赋值：Spring 将值和bean的引用注入到bean对应的属性中
3、回调实现Aware接口的方法。BeanNameAware，BeanFactoryAware，ApplicationContextAware对应的方法
4、初始化（Initialization）：分别调用（1）BeanPostProcessor的前置处理器，（2）InitialzingBean的afterPropertiesSet（）方法，（3）调用init初始化方法（4）调用BeanPostProcessor的后置处理器
5、使用（In Use）：在初始化完成之后，Bean就可以被使用了。
6、销毁（Destruction）：当Spring容器关闭时，会销毁所有的Bean。在销毁Bean之前，（1）如果实现DisposableBean接口，Spring将调用它的destory()接口方法（2）如果bean使用destroy-method声明了销毁方法，该方法也会被调用

## 3.2、控制反转和依赖注入
### 控制反转(Inversion of Control, IoC)
控制反转是一种软件设计原则，它将传统程序流程的控制权从应用程序代码转移到了框架或容器中。在传统编程中，我们的代码直接调用依赖对象；而在IoC中，这种控制关系被"反转"了 - 框架或容器负责管理对象的生命周期和依赖关系

### 依赖注入(Dependency Injection, DI)
依赖注入是实现IoC的一种具体技术，它通过容器将依赖对象"注入"到需要使用它们的类中，而不是让类自己创建或查找依赖对象

## 3.3、spring依赖注入的方式

1. 构造方法注入

```java
@Service
public class AService {
    BService bService;
    
    @Autowired
    public AService(BService bService) {
        this.bService = bService;
    }
}
```

2. set方法注入

```java
@Service
public class BService {
    AService aService;

    @Autowired
    public void setaService(AService aService) {
        this.aService = aService;
    }
}
```

3. 属性注入

```java
@Service  
public class BService {  
	@Autowired  
	AService aService;  
	//...  
}
```


# 4、spring cloud
## 4.1、nacos注册中心工作流程

**服务注册**:Nacos Client会通过发送REST请求的方式向Nacos Server注册自己的服务，提供自身的元数据，比如ip地址、端口等信息。Nacos Server接收到注册请求后，就会把这些元数据信息存储在一个双层的内存Map中（外层key是namespace，内层key是group:service）。

**服务心跳**:在服务注册后，Nacos Client会维护一个定时心跳来持续通知Nacos Server，说明服务一直处于可用状态，防止被剔除。默认5s发送一次心跳。

**服务同步**:Nacos Server集群之间会互相同步服务实例，用来保证服务信息的一致性。

**服务发现**:服务消费者(Nacos Client)在调用服务提供者的服务时，会发送一个REST请求给NacosServer，获取上面注册的服务清单，并且缓存在Nacos Client本地，同时会在Nacos Client本地开启个定时任务（默认35s）定时拉取服务端最新的注册表信息更新到本地缓存

**服务健康检查**:Nacos Server会开启一个定时任务用来检查注册服务实例的健康情况，对于超过15s没有收到客户端心跳的实例会将它的healthy属性置为false(客户端服务发现时不会发现)，如果某个实例超过30秒没有收到心跳，直接别除该实例(被剔除的实例如果恢复发送心跳则会重新注册)

## 4.2、nacos领域模型

nacos的服务由三元组唯一确定（namespace、group、serviceName）
nacos的配置由三元组唯一确定（namespace、group、dataId）

| 模型名称      | 解释                                     |
| --------- | -------------------------------------- |
| Namespace | 实现环境隔离，默认值public（一般用于区分测试、生产环境等）       |
| Group     | 不同的service可以组成一个Group，默认值Default-Group |
| Service   | 服务名称                                   |
| Cluster   | 对指定的微服务虚拟划分，默认值Default                 |
| Instance  | 某个服务的具体实例（ip+端口）                       |

## 4.3、服务调用open Feign
RestTemplate发起远程调用的代码时会存在一些问题比如：代码可读性差，参数复杂URL难以维护
OpenFeign的设计宗旨式简化Java Http客户端的开发。用户只需创建一个接口并添加相应的注解，即可实现对远程服务的调用。OpenFeign 是 Spring Cloud 的一部分，它支持 Spring MVC 的注解，如 @RequestMapping，使得使用 HTTP 请求访问远程服务就像调用本地方法一样直观和易于维护。OpenFeign集成了Ribbon，实现了客户端负载均衡


1、调用方添加依赖

```xml
<dependency>  
    <groupId>org.springframework.cloud</groupId>  
    <artifactId>spring-cloud-starter-openfeign</artifactId>  
</dependency>
```

2、启动类添加注解@EnableFeignClients

```java
@SpringBootApplication  
@EnableFeignClients  
public class NacosConsumer8080 {  
  
    public static void main(String[] args) {  
        SpringApplication.run(NacosConsumer8080.class, args);  
    }  
  
}
```

3、调用方定义pai

```java
@FeignClient("depart-provider")  
public interface ProviderServiceApi {  
  
    @GetMapping("/provider/depart/list")  
    List<Depart> getInfo();  
}
```

4、调用方调用pai

```java
    @GetMapping("/list")  
    public List<Depart> listHandle() {  
//        String url = SERVICE_PROCIER + "/list";  
//        return template.getForObject(url, List.class);  
        return providerServiceApi.getInfo();  
    }
```

## 4.4、流量治理-sentinel
sentinel 的使用可以分为两个部分:
1、核心库（Java 客户端）：不依赖任何框架/库，能够运行于 Java 8 及以上的版本的运行时环境，同时对 Dubbo / Spring Cloud 等框架也有较好的支持。
2、控制台（Dashboard）：Dashboard 主要负责管理推送规则、监控、管理机器信息等。基于 Spring Boot 开发，打包后可以直接运行。


## 4.5、网关-GateWay
在微服务架构中，一个系统会被拆分为很多个微服务。那么作为客户端要如何去调用这么多的微服务呢？如果没有网关的存在，我们只能在客户端记录每个微服务的地址，然后分别去调用。这样肯定是不合理的。
为解决上面的问题所以引入了网关的概念：所谓的**API网关**，就是指系统的统一入口，提供内部服务的路由中转，为客户端提供统一服务，一些与业务本身功能无关的公共逻辑可以在这里实现，诸如认证、鉴权、监控、路由转发等

# 5、mybatis


# 6、数据库

## 6.1、mysql架构
分客户端，server层，存储层

1、客户端通过连接器和服务端建立连接
2、分析器进行词法分析拆分为token(如SELECT、FROM等)，然后语法分析判断是否符合语法规范
3、优化器进行优化处理，比如决定使用哪个索引，生成执行计划
4、然后通过执行器调用存储引擎执行具体的sql
5、存储引擎有很多实现（myisam、innodb等）innodb是目前主流的方案
6、inndb分两层，内存层和磁盘层。内存主要做运行时的读取写入（undo log buffer，redo log buffer），磁盘主要做持久化（.ibd文件，undo log，redo log，bin log）
> 注意：bin log是在任何存储引擎都有，undo log和redo log是在innodb下独有的

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250702141645.png)

## 6.2、undo log、redo log、bin log

>想象你正在只用一个笔记本写日记。为了避免内容丢失或出错，你会怎么做?是不是通常这样做
1、用铅笔先打草稿，这样方便之后擦除
2、用钢笔正式写下内容。
3、额外复印一份保存，这样就不怕内容丢失了
MySQL的三大日志系统也是类似的道理，他们各自承担着不同的职责，共同保证数据的安全性和可靠性。其中:

1、undo log就相当于使用铅笔写字，可以随时擦除。主要用来记录事务执行前的数据状态状态。主要用于:（1）事务回滚:当事务执行失败需要撤销时，根据undolog恢复数据。（2）实现MVCC:支持数据库的并发访问，让不同事务能看到对应时间点的数据版本
2、redo log就相当于用钢笔写下正式内容。主要记录事务修改后的数据状态。主要作用:（1）确保事务的持久性:即使数据库崩溃，也能通过redolog恢复已提交的事务（2）提升性能:先将修改写入redolog(顺序写，速度快)再慢慢更新到磁盘数据文件(随机写，速度慢)
3、binlog就相当于把日志复印一份保存。主要记录所有数据库的变更操作。主要作用:（1）主从复制:将主库的变更同步到从库（2）数据恢复:通过历史bin log恢复某个时间点的数据状态

| 特性   | undo Log  | redo Log  | Binlog  |
| :--- | :-------- | :-------- | ------- |
| 层级   | InnoDB引擎层 | innoDB引擎层 | server层 |
| 内容   | 数据修改前的值   | 数据修改后的值   | SQL操作语句 |
| 作用   | 事务回滚和MVCC | 崩溃恢复      | 主从复制和备份 |
| 写入时机 | 事务开始前     | 事务执行中     | 事务提交时   |

## 6.3、mvcc原理

多版本并发控制，是事务隔离级别一种无锁的实现方式。数据库隔离级别读已提交、可重复读 都是基于MVCC实现的，相对于加锁简单粗暴的方式，它用更好的方式去处理读写冲突，能有效提高数据库并发性能。简单来说就是维护一个数据的多个版本，使得读写操作没有冲突【注意】：只有快照读才会使用MVCC，当前读使用行锁+间隙锁（临键锁Next-Key Locks）实现、

>快照读：普通的select语句
>当前读：delete、 update 、insert、select for update

它的实现依赖概念：版本链和ReadView

1. 版本链：多个事务并行操作某一行数据时，不同事务对该行数据的修改会产生多个版本，然后通过回滚指针（roll_pointer），连成一个链表，这个链表就称为版本链(最新记录+undo-log)
2. ReadView：执行查询，创建一个读视图，其实就是一个数据结构，包含：当前事务，未提交事务ids，未开始事务

流程：
1. 在快照读时生成一个ReadView（注意在读已提交下，每次快照读都生成一个ReadView）
2. 遍历版本链，判断是否符合ReadView规则（这个过程其实就是找它最近一次事务提交的数据版本）
3. 返回符合规则的数据


## 6.4、事务隔离级别
用来产生并发事务产生的一些问题

| **级别** | **名字** | **隔离级别**         | **脏读** | **不可重复读** | **幻读**      | **数据库默认隔离级别**     | 解释                       |
| ------ | ------ | ---------------- | ------ | --------- | ----------- | ----------------- | ------------------------ |
| 1      | 读未提交   | read uncommitted | 是      | 是         | 是           |                   | 事务A可以读取到事务B未提交的数据        |
| 2      | 读已提交   | read committed   | 否      | 是         | 是           | Oracle和SQL Server | 事务A只能读取其它事务已提交的数据（避免了脏读） |
| 3      | 可重复读   | repeatable read  | 否      | 否         | 是（innodb除外） | MySQL             | 保证在同一个事务中多次读取同样数据的结果是一样的 |
| 4      | 串行化    | serializable     | 否      | 否         | 否           |                   | 事务串行化顺序执行                |

> 隔离级别越高，安全性就越高，性能越低

## 6.5、数据库索引分类


1. 按物理存储分类
聚簇索引(Clustered Index)：索引的叶子节点直接存储完整的数据行（数据即索引）
非聚簇索引(Non-Clustered Index)：也叫二级索引(Secondary Index)，索引的叶子节点存储的是主键值（索引和数据分离）
>MyISAM的所有索引都是非聚簇索引，InnoDB的非主键索引都是二级索引

2. 按逻辑功能分类
主键索引(Primary Key)：唯一且非空的索引，一个表只能有一个主键索引
唯一索引(Unique Index)：确保索引列的值唯一，允许有空值
普通索引(Normal Index)：最基本的索引类型，没有唯一性限制
复合索引(Composite Index)：多个列组合的索引，遵循最左前缀原则
前缀索引(Prefix Index)：对列的前面部分建立索引，节省索引空间

## 6.6、什么是回表？怎么避免？
回表是指当使用非聚簇索引（二级索引）查询时，MySQL需要先通过二级索引找到主键值，再通过主键值去聚簇索引中查找完整数据行的过程

避免回表：使用覆盖索引。创建包含所有查询字段的复合索引，使查询只需访问索引而无需回表

## 6.7、索引设计的原则

基本的原则就是：查询更快，占用空间更小，代码先行，索引后上

**适合建索引的场景**
1. 选择合适的列，经常用于查询，连接，过滤，排序的列作为索引列
2. 覆盖索引，覆盖索引可以减少回表次数，根据业务情况尽可能使用覆盖索引
3. 避免过长的索引：长字符串采用前缀索引，前缀索引长度不建议超过20

**不适合建索引的场景**
1. 频繁更新的字段不适合建立索引（需要重建索引）
2. 数据重复且分布比较均匀的的字段不适合建索引（唯一性太差的字段不适合建立索引），例如性别，真假值

# 7、设计题
## 7.1、如何设计一个高并发系统
高并发系统特点就是短时间内大量用户请求访问系统，需要系统能快速稳定的响应（高性能，高可用）
1、系统拆分：微服务体系
2、缓存加速：构建缓存层，redis
3、MQ削峰：对一些流量特别大的场景
4、数据分离：垂直分库，水平分表
5、读写分离：mysql设置主从，主库写数据，从库读取数据
6、服务监控
![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20250702151217.png)


