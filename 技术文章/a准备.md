# 并发相关
https://zhuanlan.zhihu.com/p/136422134
面试官您好，我叫郭燕成，2019年毕业于天津科技大学。毕业后到现在一直是做java开发的工作。目前有大概6年的工作经验，现在的公司联创开元，是做煤矿智能化平台，包括水情监控，瓦斯，人员相关的项目。然后平常工作中用到的技术栈就是spring boot，cloud alibaba那一套，然后数据库mysql，sqlserver，前端vue，uni-app都了解一些。还有目标检测技术yolo，rag大语言模型知识库都做过相关的工作，这就是我的基本情况。

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

- **抽象类 (Abstract Class):**
    - 它是一个**类**，用 `abstract` 关键字修饰。它是对一系列本质上属于同一类事物的抽象（例如：动物、车辆、图形）。它包含了这些对象的共同特征（属性）和行为（方法）。
    - 它不能被实例化，只能被继承。
    - 核心思想是：**代码复用**和**定义模板**。它建立了“是一个（is-a）”的关系。
        
- **接口 (Interface):**
    - 它是一个**完全抽象的规范**，用 `interface` 关键字定义。它定义了一组对象应该具备的能力或契约，而不关心其具体实现。
    - 核心思想是：**定义行为规范**和**实现解耦**。它建立了“能做什么（can-do）”的关系。


# 3、spring全家桶、mybatis
## 3.1、bean生命周期
1、实例化：解析配置为BeanDefinition对象，通过反射创建实例
2、属性赋值：Spring 将值和bean的引用注入到bean对应的属性中
3、回调实现Aware接口的方法。BeanNameAware，BeanFactoryAware，ApplicationContextAware对应的方法
4、初始化（Initialization）：分别调用（1）BeanPostProcessor的前置处理器，（2）InitialzingBean的afterPropertiesSet（）方法，（3）调用init初始化方法（4）调用BeanPostProcessor的后置处理器
5、使用（In Use）：在初始化完成之后，Bean就可以被使用了。
6、销毁（Destruction）：当Spring容器关闭时，会销毁所有的Bean。在销毁Bean之前，（1）如果实现DisposableBean接口，Spring将调用它的destory()接口方法（2）如果bean使用destroy-method声明了销毁方法，该方法也会被调用

## 3.2、控制反转和依赖注入
控制反转(Inversion of Control, IoC)
控制反转是一种软件设计原则，它将传统程序流程的控制权从应用程序代码转移到了框架或容器中。在传统编程中，我们的代码直接调用依赖对象；而在IoC中，这种控制关系被"反转"了 - 框架或容器负责管理对象的生命周期和依赖关系

依赖注入(Dependency Injection, DI)
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

## 5.1、mybatis的一级缓存和二级缓存

一级缓存：在一次 SqlSession 中（数据库会话），程序执行多次查询，且查询条件完全相同，多次查询之间程序没有其他增删改操作，则第二次及后面的查询可以从缓存中获取数据，避免走数据库

>一级缓存失效场景：（1）不同的SqlSession对应不同的一级缓存（2）同一个SqlSession但是查询条件不同（3）同一个SqlSession两次查询期间执行了任何一次增删改操作（4）同一个SqlSession两次查询期间手动清空了缓存

二级缓存：namespace级别的缓存，二级缓存被同一个 `namespace` 下的多个 `SqlSession` 共享，是一个全局的变量。MyBatis 的二级缓存不适应用于映射文件中存在多表查询的情况

> 通常我们会为每个单表创建单独的映射文件，由于MyBatis的二级缓存是基于`namespace`的，多表查询语句所在的`namspace`无法感应到其他`namespace`中的语句对多表查询中涉及的表进行的修改，引发脏数据问题

缓存查询顺序：
- 先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用
- 如果二级缓存没有命中，再查询一级缓存
- 如果一级缓存也没有命中，则查询数据库

>一级缓存默认开启，二级缓存开启需要在mapper.xml里加cache标签


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

## 6.8、索引失效的情况

1.  or连接的条件不是每一个列都有索引，这时有索引的列也会失效 
2.  复合索引遵守“最左前缀”原则，在通过联合索引检索数据时，从索引中最左边的列开始，一直向右匹配，如果遇到范围查询(>、<、between、like等)，就停止后边的匹配
```
eg：建立复合索引index:(a,b,c) --- 实际上已经建立了三个联合索引(a)、(a,b)、(a,b,c)
select * from table where a = '1'  //走索引
select * from table where c = '1'  //不走索引
select * from table where c = '1' and b > 1 and a='2' //走ab（因为优化成abc的顺序，然后b是范围，导致后面的c停止匹配）
```

3. like查询是以%开头，索引失效（以%结尾，索引可以使用）

> B+树，索引是有序排列的。索引的排列顺序是根据比较字符串的首字母排序的，如果首字母相同，就根据比较第二个字母进行排序，以此类推。以%开头，首字母不确定

4. 存在索引列的数据类型隐形转换，则用不上索引（比如列类型是字符串，那一定要在条件中将数据使用引号引用起来,否则不使用索引）
5. 索引列使用函数或运算，用不上索引
6. 如果mysql估计使用全表扫描要比使用索引快,则不使用索引（比如数据量极少的表）

## 6.9、mysql常用函数

1. 字符串拼接CONCAT(str1, str2, ...)
```sql
SELECT CONCAT('Hello', ' ', 'World');
```

2. 替换字符串中的指定内容REPLACE(str, old_str, new_str) 
```sql
SELECT REPLACE('I like apples', 'apples', 'bananas');
```

3. 截取子字符串SUBSTRING(str, start, length) 
```sql
SELECT SUBSTRING('abcdef', 2, 3); -- 输出: 'bcd'（从第2个字符开始取3个）
```

4. 生成0-1中间的随机数RAND() 
```sql
SELECT RAND(); -- 输出: 如 0.752345
```

5. 返回当前日期和时间
```sql
SELECT NOW();
```

6. 计算两个日期相差的天数DATEDIFF(date1, date2) 
```sql
SELECT DATEDIFF('2023-10-25', '2023-10-20'); -- 输出: 5
```

7. 统计行数COUNT() 
```sql
SELECT COUNT(*) FROM users; -- 返回用户总数
```

8. 求和 / 平均值SUM() / AVG() 
```sql
SELECT AVG(salary) FROM employees;
```

9. 返回最大值/最小值MAX() / MIN() 
```sql
SELECT MAX(price) FROM products;
```

## 6.10、并发访问下事务产生的问题

当同时有多个用户在访问同一张表中的记录，每个用户在访问的时候都是一个单独的事务。事务在操作时的理想状态是：事务之间不应该相互影响，实际应用的时候会引发下面三种问题：

1. 脏读： 一个事务（用户）读取到了另一个事务没有提交的数据
2. 不可重复读：一个事务多次读取同一条记录，出现读取数据不一致的情况。一般因为另一个事务更新了这条记录而引发的。
3. 幻读：在一次事务中，多次读取到的条数不一致

## 6.11、innodb和myisam区别

1. nnodb支持事务，外键。myisam不支持
2. innodb支持行级锁和表级锁（默认行级锁），myisam仅支持表级锁
3. innodb使用聚簇索引，myisam使用非聚簇索引
4. innodb不存储行数，myisam存储行数(表锁没有并发修改的问题，所以维护成本低)

## 6.12、索引下推

索引下推是MySQL 5.6版本引入的一项优化技术，用于提高查询性能。核心思想是将WHERE条件过滤的部分下推到存储引擎层执行，而不是在服务器层进行过滤。服务层按照过滤后的数据再一回表扫描。目的就是减少回表次数
> 回表：当使用普通索引（非聚簇索引）查询时，MySQL需要先通过索引找到主键值，然后再根据主键值去聚簇索引中查找完整数据行的过程

适用于组合索引（复合索引）。WHERE条件中有一部分可以通过索引判断，另一部分不能

```sql
explain select * from employees where name like "张%" and age = 20
```

**无索引下推时**：
1. 存储引擎找到所有`name LIKE '张%'`的记录
2. 将这些记录返回给服务层
3. 服务层再过滤出`age = 20`的记录

**有索引下推时**：
1. 存储引擎找到`name LIKE '张%'`的记录
2. 同时检查这些记录的`age = 20`条件
3. 只返回同时满足两个条件的记录给服务层

>当一条sql使用索引下推技术后，在explain执行计划中，extra列中出现using index condition的信息


## 6.13、cout(1)和count(星)和count(字段)区别

1、count（1）和count（星）是一样的，不管是innodb还是myisam （其中myisam一个字段存储行数，innodb利用索引统计）
2、count（字段）会选择该字段的可用索引进行统计，如果没有则进行全部扫描，同时它会过滤掉null值

## 6.14、如何定位慢查询

1. 开启慢查询日志
```sql
-- 开启慢查询日志
SET GLOBAL slow_query_log = 'ON';
-- 设置慢查询阈值(秒)
SET GLOBAL long_query_time = 1;
-- 查看日志文件位置
SHOW VARIABLES LIKE 'slow_query_log_file';
```

2. EXPLAIN分析
```sql
EXPLAIN SELECT * FROM users WHERE username = 'test';
```

## 6.15、in和exist的区别

```sql
select    *      from   a     where    id    in  （select  id  from  b） ;    
select    *      from   a     where    id    exists  （select  id  from  b） ;   
``` 

使用in ,sql语句是先执行子查询，也就是先查询b表，再查a表，而使用exists是先查主表a ,再查子表b; 

对于主表数据较多时，我们使用in速度比exist更快，反之，从表b较大时，使用exist查询速度更快（都会使用索引）,

如果使用的是not in与not exists，直接使用not exists，因为not in 会进行全表扫描不走索引，not exists会走索引。 

## 6.16、explain分析

| **字段**            | **说明**                             | **常见值/示例**                                                                                                                                                   |
| ----------------- | ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **id**            | 查询标识符，表示 SELECT 的顺序                | 数字越大越先执行                                                                                                                                                     |
| **select_type**   | 查询类型                               | `SIMPLE`（简单查询，不包含子查询或 UNION）、`PRIMARY`（主查询包含子查询时的最外层查询）、`SUBQUERY`（子查询中的第一个 SELECT）、`DERIVED`（派生表（FROM 子句中的子查询））、`UNION`（UNION 中的第二个或后面的查询）、`UNION RESULT` 等 |
| **table**         | 查询涉及的表名                            | `users`、`<derived2>`（派生表）、`<union1,2>`（UNION 结果）                                                                                                             |
| **partitions**    | 查询涉及的分区（非分区表为 NULL）                | `p0`、`p1`、`NULL`                                                                                                                                             |
| **type**          | 访问类型（性能关键指标，从最优到最差排序）              | `system` > `const` > `eq_ref` > `ref` > `range` > `index` > `ALL`（全表扫描最差）                                                                                    |
| **possible_keys** | 可能使用的索引（查询优化器评估可用的索引）              | `PRIMARY`、`idx_name`、`NULL`（无可用索引）                                                                                                                           |
| **key**           | 实际使用的索引（若为 NULL 表示未使用索引）           | `PRIMARY`、`idx_email`、`NULL`                                                                                                                                 |
| **key_len**       | 使用的索引长度（字节数，越大说明索引利用率越高）           | `4`（INT 主键）、`767`（VARCHAR(255) UTF-8 索引）                                                                                                                     |
| **ref**           | 与索引比较的列或常量（显示索引如何被引用）              | `const`、`db.users.id`、`NULL`                                                                                                                                 |
| **rows**          | 预估需要扫描的行数（越小越好）                    | `1`（精确匹配）、`1000`（全表扫描）                                                                                                                                       |
| **filtered**      | 存储引擎返回数据后，经服务器层过滤的剩余百分比（100 表示未过滤） | `10.0`（仅 10% 数据符合条件）、`100.0`                                                                                                                                 |
| **Extra**         | 额外执行信息（重要优化提示）                     | `Using index`（覆盖索引）、`Using where`（额外过滤）、`Using filesort`（需排序）                                                                                                |
**关键字段1：type**

（1）system：system是const的特例，表示表中只有一行记录，这个几乎不会出现，也作为了解。
（2）const: 通过索引直接访问一行数据，主键或唯一索引进行等值比较时出现
```sql
explain select * from user where id =2;
```
（3）eq_ref: 当使用连接查询时，如果查询使用的是主键或唯一索引来查询
```sql
EXPLAIN SELECT * FROM users JOIN orders ON users.id = orders.user_id;
```
（4）ref：使用的是非唯一索引来匹配行时
```sql
explain select * from user where name = '张三'
```
（5）range：range使用索引来检索给定范围的行数据，一般是在where后面使用between、<>、in等查询语句就会出现range
（6）index：使用索引进行全索引扫描
```sql
explain select name from user
```
（7）all：不走索引，进行了全表扫描，这时候表示通常需要增加索引来进行优化了

**关键字段2：Extar**
（1）Using index：覆盖索引（无需回表）。
（2）Using where：WHERE 条件过滤数据。
（3）Using temporary：使用临时表（需优化，如 GROUP BY 无索引）。
（4）Using filesort：使用外部排序（需优化，如 ORDER BY 无索引）。
（5）Using join buffer：使用连接缓存（需调整 join_buffer_size）

## 6.17、innodb存储引擎为什么用B+而不是B或红黑树
1. 更低的树高度：
B+树的非叶子节点不存储数据，只存储键值，因此每个节点可以容纳更多的键
这使得B+树在相同数据量下比B树更"矮胖"，减少了磁盘I/O次数
2. 更稳定的查询性能：
B+树的所有数据都存储在叶子节点，查询任何数据都需要从根节点走到叶子节点
查询路径长度相同，性能稳定
3. 范围查询效率高：
B+树的叶子节点通过指针相连，形成有序链表
范围查询时只需找到起始节点，然后沿着链表遍历即可
B树进行范围查询时需要不断进行中序遍历，效率较低
4. 更高的磁盘利用率：
B+树的内部节点不存储数据，可以容纳更多键值。一次磁盘读取可以获取更多索引信息

>红黑树是二叉树，瘦高，不考虑

# 7、redis
## 7.1、redis工作模式
单机模式：是最简单的 Redis 工作模式。在单机模式下，Redis 只运行在单个节点上，数据存储在该节点的内存中。这种模式适用于小规模应用或开发环境

主从复制模式：通过将数据从主节点复制到一个或多个从节点来提高数据的可靠性和读取性能。主节点负责处理写入操作，从节点复制主节点的数据，并可以处理读取操作。主从复制模式适用于需要读取扩展和数据冗余的场景

哨兵模式：主从复制的基础上，引入了哨兵节点来监控主节点的状态。当主节点发生故障时，哨兵节点会自动将一个从节点升级为新的主节点，并将其他从节点重新配置为复制新的主节点。这种模式提供了故障转移和自动主节点切换的功能

cluster集群：即使使用哨兵，redis每个实例也是全量存储，每个redis存储的内容都是完整的数据。cluster是为了解决单机Redis容量有限的问题，将数据按一定的规则分配到多台机器，提高并发量

## 7.2、redis数据类型和使用场景

1、string
使用场景：存储简单的键值对、缓存对象（序列化数据）、计数器等。

> **Redis的key允许有多个单词形成层级结构，多个单词之间用” ：“隔开**

```
# 单值缓存
set num 1
# 对象缓存
set user:1 json
# 计数器
incr article:read:id
```
2、hash
使用场景：存储对象的属性和值，适合存储复杂的数据结构
```
# 基本操作
hmset user:1 name austin age 25 address guangzhou balance 6888
```
3、list
使用场景：存储有序的元素列表，支持在列表的两端进行插入和删除操作
```
# 基本操作
LPUSH  key  value [value ...]       // 将一个或多个值value插入到key列表的表头(最左边)
RPUSH  key  value [value ...]       // 将一个或多个值value插入到key列表的表尾(最右边)
LPOP  key                   // 移除并返回key列表的头元素
RPOP  key                   // 移除并返回key列表的尾元素
LRANGE  key  start  stop        // 返回列表key中指定区间内的元素，区间以偏移量start和stop指定
```
4、set
使用场景：存储唯一的、无序的元素集合（抽奖）。并支持集合间的交集、并集和差集等操作（共同好友）
```
SADD luckdraw:product:a 10001
SPOP luckdraw:product:a 1
```
5、zset
使用场景：存储有序的元素集合，每个元素都与一个分数相关联，可以根据分数进行范围查询和排序

```
zadd zsetofpost 89 post:1
```
6、bitmaps
不是实际的数据类型，而是基于 String 类型的位操作
使用场景：每日签到。因为Bit的值为0或者1，位图的每一位代表一天的签到，1表示已签，0表示未签。考虑到每月初需要重置连续签到次数，最简单的方式是按用户每月存一条签到数据（也可以每年存一条数据）。Key的格式为`u:sign:uid:yyyyMM`，Value则采用长度为4个字节（32位）的位图（最大月份只有31天）
```
# 用户1000在2023年2月7号签到
SETBIT u:sign:1000:202302 6 1 # 偏移量是从0开始，所以要把7减1
# 检查用户1000在2023年2月7号是否签到
GETBIT u:sign:1000:202302 6   # 偏移量是从0开始，所以要把7减1
# 统计用户1000在2月份签到次数
BITCOUNT u:sign:1000:202302
```

## 7.3、说说缓存穿透、击穿、雪崩的区别

缓存击穿：一份热点数据，在它缓存失效期间，大量的请求直接命中存储层。

> 解决方法：
 1.设置热点数据永不过期的策略。
 2.加互斥锁，加锁的方式，锁的对象就是key，这样，当大量查询同一个key的请求并发进来时，只能有一个请求获取到锁，然后获取到锁的线程查询数据库，然后将结果放入到缓存中，然后释放锁，此时，其他处于锁等待的请求即可继续执行，由于此时缓存中已经有了数据，所以直接从缓存中获取到数据返回，并不会查询数据库

缓存穿透：某些不存在的数据，被大量的查询访问，缓存层中没有这些数据的缓存，请求就直达存储层，造成宕机。

> 解决方法：
 1.缓存空对象：就是当数据库中查不到数据的时候，我缓存一个空对象，然后给这个空对象的缓存设置一个过期时间，这样下次再查询该数据的时候，就可以直接从缓存中拿到，从而达到了减小数据库压力的目的
 2.布隆过滤器：将所有的key都存在过滤器中，在访问缓存层的时候会首先访问过滤器，如果过滤器中不存在这个值，那么直接返回空值。

布隆过滤器：它实际上是一个位数组 + 一系列随机hash算法映射函数，主要用于判断一个元素是否在集合中。在初始化布隆过滤器时，会先将所有key进行n次hash运算，这样就可以得到n个位置，然后将这n个位置上的元素改为1。这样，就相当于把所有的key保存到了布隆过滤器中了。  当有新的请求时，先到布隆过滤器中查询是否存在：如果布隆过滤器中不存在该条数据则直接返回；如果布隆过滤器中已存在，才去查询缓存redis，如果redis里没查询到则穿透到Mysql数据库 (布隆过滤器可以添加元素，但是不能删除元素。因为删掉元素会导致误判率增加)

缓存雪崩：由于某些原因，中间层的缓存层不能提供服务，大量的请求直达数据存储层，导致宕机。

> 解决方法：
 1.设置多级缓存，在存储层之前加上一层本地缓存（guava）。
 2.构建高可用的redis缓存（主从，哨兵），部署多个redis实例，个别不能用，也不至于宕机。

## 7.4、redis持久化策略

1、rdb

把当前内存中的快照写入磁盘

（1）save：save指令执行会阻塞当前redis服务器，直到当前rdb过程执行完，可能造成长时间阻塞，线上环境不建议使用
（2）bgsave：调用fork函数生成子进程，解决了save的阻塞问题
（3）自动执行：（redis配置文件中配置）save 900 1   save 300 10   save 60 1000

2、aof

以日志的方式记录每次写命令，重启时再执行aof中的命令达到数据恢复的目的（是目前redis持久化的主流方式）

aof写数据策略：

（1）always：服务器每写入一个命令,就调用一次fdatasync（不会丢失数据）
（2）Everysec：服务器每一秒重调用一次fdatasync（数据同步），最多丢失1秒的数据
（3）NO：操作系统决定任何将缓冲区里面的命令写入磁盘里面，数据丢失量是不确定的

> 注：always策略持久化数据：先把写命令追加到aof buffer中，下一次进入事件循环循环后，再将buffer写到磁盘上。也就是说，这次写到磁盘上的内容是上一个事件循环产生的所以，即使设置为always，也会丢失一个循环的数据

3、混合持久化
Redis 4.0 后支持混合模式，结合两者优势：
AOF 文件包含全量 RDB 数据 + 增量 AOF 命令。
重启时先加载 RDB 快照，再执行增量 AOF 命令，恢复速度和数据安全性兼顾。

3、对比

|  | rdb | aof |
| --- | --- | --- |
| 占用存储空间 | 小（数据级） | 大（指令级） |
| 恢复速度 | 快 | 慢（需要执行指令） |
| 数据安全性 | 可能会丢失最后一次持久化后的数据 | 根据策略决定 |


# 8、mq
## 8.1、rabbitmq架构
![RabbitMQ架构](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202307241048618.png)
Producer（生产者）：生产者是消息的发送方，负责将消息发布到RabbitMQ的交换机（Exchange）。
broker（服务端）：接收客户端的连接，实现AMQP实体服务。
VHost：虚拟主机，用于逻辑隔离。一个虚拟主机里面可以有若干个Exchange和Queue
Exchange（交换机）：交换机是消息的接收和路由中心，它接受来自生产者的消息，并将消息路由到一个或多个与之绑定的队列（Queue）中。
Queue（队列）：队列是消息的存储和消费地，它保存着未被消费的消息，等待消费者（Consumer）从队列获取并处理消息。
Binding（绑定）：绑定是交换机和队列之间的关联关系，它定义了交换机将信息路由到了哪些队列中。
Consumer（消费者）：消费者是消息的接收方，负责从队列获取消息，并进行处理和消费。

## 8.2、rabbitmq交换机类型

| 交换机类型       | 匹配规则                    | 适用场景           |
| ----------- | ----------------------- | -------------- |
| **Direct**  | 精确匹配 `Routing Key`      | 单队列精确路由（如支付订单） |
| **Fanout**  | 忽略 Key，广播所有队列           | 事件通知、群发        |
| **Topic**   | 通配符匹配（`*`、`#`）          | 多条件分类（如日志分类）   |
| **Headers** | 绑定map，匹配消息 Headers 属性   | 复杂条件路由（如元数据过滤） |
| Default     | Routing Key = 队列名（隐式绑定） | 简单队列通信         |
## 8.3、rabbitmq工作模式

| 模式          | 交换机类型            | 核心规则             | 典型场景          |
| ----------- | ---------------- | ---------------- | ------------- |
| **简单模式**    | Default Exchange | 队列名=Routing Key  | 单任务队列         |
| **工作队列模式**  | Default Exchange | 多个消费者竞争消费        | 并行任务处理        |
| **发布/订阅模式** | Fanout Exchange  | 广播到所有队列          | 事件通知          |
| **路由模式**    | Direct Exchange  | 精确匹配 Routing Key | 条件过滤（如日志分级）   |
| **主题模式**    | Topic Exchange   | 通配符匹配（`*`、`#`）   | 多级分类路由（如订单状态） |

## 8.4、如何保证消息传递的可靠性

消息丢失的场景：
1. 生产者到mq server：可能网络，无法收到消息
> 消息从 producer 到 rabbitmq broker有一个 confirmCallback 确认模式。(无论成功失败都有返回)
> 在回调函数中处理失败的业务


2. mq server中的交换机到队列：肯能代码或配置方面的问题导致失败
>消息从 exchange 到 queue 投递失败有一个 returnCallback 退回模式。（失败时才会有返回）
>在回调函数中处理

3. mq server中存储的消息丢失：mq server宕机，但没有持久化消息
>在初始化队列的时候就有个参数是是否持久化（交换机，队列，消息都可以做持久化）

4. 消费端异常：消费者宕机或故障
>消费者端消息接收确认采用的是ack模式。自动ACK：消息一旦被接收，消费者自动发送ACK，队列删除消息。手动ACK：消息接收后，不会发送ACK，需要手动调用
 如果消息不太重要，丢失也没有影响，那么自动ACK会比较方便
 如果消息非常重要，不容丢失。那么最好在消费完成后手动ACK，否则接收消息后就自动ACK，RabbitMQ就会把消息从队列中删除

5. 消息补偿机制（兜底方案）
生产者端：记录消息日志，定期扫描未确认的消息并重发。消费者端：实现幂等性（避免重复消费）


```java
@Configuration
public class RabbitConfig {
 
    @Bean
    public RabbitTemplate createRabbitTemplate(ConnectionFactory connectionFactory) {
        RabbitTemplate rabbitTemplate = new RabbitTemplate();
        rabbitTemplate.setConnectionFactory(connectionFactory);
 
        //设置消息投递失败的策略，有两种策略：自动删除或返回到客户端。
        //我们既然要做可靠性，当然是设置为返回到客户端(true是返回客户端，false是自动删除)
        rabbitTemplate.setMandatory(true);
 
        rabbitTemplate.setConfirmCallback(new RabbitTemplate.ConfirmCallback() {
            @Override
            public void confirm(CorrelationData correlationData, boolean ack, String cause) {
                log.info("相关数据：" + correlationData);
                if (ack) {
                    log.info("投递成功,确认情况：" + ack);
                } else {
                    log.info("投递失败,确认情况：" + ack);
                    log.info("原因：" + cause);
                }
            }
        });
 
        rabbitTemplate.setReturnCallback(new RabbitTemplate.ReturnCallback() {
            @Override
            public void returnedMessage(Message message, int replyCode, String replyText, String exchange, String routingKey) {
               log.info("ReturnCallback:     " + "消息：" + message);
               log.info("ReturnCallback:     " + "回应码：" + replyCode);
               log.info("ReturnCallback:     " + "回应信息：" + replyText);
               log.info("ReturnCallback:     " + "交换机：" + exchange);
               log.info("ReturnCallback:     " + "路由键：" + routingKey);
            }
        });
 
        return rabbitTemplate;
    }
}
```

## 8.5、怎么避免重复消费问题
保证消费者的幂等性（调用方，对一个系统进行重复调用（参数全部相同），不论重复调用多少次，这些调用对系统的影响都是相同的效果）

如何保证幂等性？
使用代码的逻辑判断，判断消息状态是否已经被消费过了

> 使用数据库一个表来记录消息的状态（或者用redis来记录也可以）。每次消费之前，都查询判断消息的状态，是否已经被消费了。这个状态可以是id。例如，如果消息是订单，而且id是全局唯一的，那么只需要拿这个订单id来做判断即可。

## 8.6、死信队列和延时队列
死信队列是一种特殊的队列，用于存储无法被正常消费的消息（即“死信”）。当消息满足某些条件时，RabbitMQ 会自动将其路由到死信队列，而不是直接丢弃。

>消息成为死信的三种情况
消费者拒绝消息（basic.reject 或 basic.nack）且设置 requeue=false。
消息在队列中过期（通过 x-message-ttl 设置队列或消息的存活时间）。
队列达到最大长度（通过 x-max-length 限制队列消息数量，超出的消息会被丢弃或成为死信）。

延迟队列是指消息在发送到队列后，不会立即被消费，而是在指定的延迟时间后才被投递给消费者。RabbitMQ 本身没有直接提供延迟队列功能，但可以通过以下两种方式实现：
实现方案：死信队列 + TTL
利用消息或队列的 TTL特性，当消息过期后通过死信交换机路由到目标队列


# 9、并发编程
## 9.1、线程状态

 1. NEW（新建）：线程被创建但尚未启动（未调用 start() 方法）
 2. RUNNABLE（就绪）：线程正在 JVM 中执行或等待系统资源（如 CPU 时间片）
 3. BLOCKED（阻塞）：线程等待获取一个 同步锁（synchronized 锁）时进入阻塞状态
 4. WAITING（无限等待）：线程因调用以下方法进入等待状态（Object.wait()，Thread.join()），需要其他线程显式唤醒
 5. TIMED_WAITING（超时等待）线程在带超时时间的方法中等待：Thread.sleep(long)，Object.wait(long)
 6. TERMINATED（终止）：线程执行完毕或异常退出后进入终止状态


## 9.2、java线程创建的方式

1. 继承Thread类
```java
public class MyThread extends Thread {
    @Override
    public void run() {
        // 线程执行的代码
        System.out.println("线程运行中...");
    }
}
// 使用方式
MyThread thread = new MyThread();
thread.start();
```

2. 实现Runnable接口
```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        // 线程执行的代码
        System.out.println("Runnable线程运行中...");
    }
}
// 使用方式
Thread thread = new Thread(new MyRunnable());
thread.start();
// 或者使用Lambda表达式
Thread lambdaThread = new Thread(() -> {
    System.out.println("Lambda线程运行中...");
});
lambdaThread.start();
```

3. 实现Callable接口（带返回值）
```java
public class MyCallable implements Callable<String> {
    @Override
    public String call() throws Exception {
        // 线程执行的代码
        return "Callable线程返回值";
    }
}
// 使用方式
FutureTask<String> futureTask = new FutureTask<>(new MyCallable());
Thread thread = new Thread(futureTask);
thread.start();
// 获取返回值
String result = futureTask.get();
System.out.println(result);
```

4. 使用线程池（Executor框架）
```java
public static void main(String[] args) {
        // 创建线程池
        ExecutorService executor = Executors.newFixedThreadPool(5);
        // 提交任务
        executor.execute(() -> {
            System.out.println("线程池中的线程执行任务");
        });
        // 关闭线程池
        executor.shutdown();
    }
```

## 9.3、线程池参数
Java 线程池的核心是 java.util.concurrent.ThreadPoolExecutor 类

7大参数如下：
1、corePoolSize (核心线程数)
线程池中始终保持存活的线程数量，即使它们是空闲的。
除非设置了 allowCoreThreadTimeOut(true)，否则核心线程不会被回收。

2、maximumPoolSize (最大线程数)

线程池允许创建的最大线程数量。
当工作队列满了之后，线程池会创建新线程来执行任务，直到线程数达到此值。

3、keepAliveTime (线程空闲时间)
当线程数超过 corePoolSize 时，非核心空闲线程在等待新任务的最长时间，超过这个时间就会被回收。
如果设置了 allowCoreThreadTimeOut(true)，此策略也会应用于核心线程。

4、unit (时间单位)
keepAliveTime 的时间单位（如 TimeUnit.SECONDS）。

5、workQueue (工作队列)
用于保存等待执行的任务的阻塞队列。
常用的有：
ArrayBlockingQueue：有界队列，必须指定大小。
LinkedBlockingQueue：无界队列（默认容量为 Integer.MAX_VALUE），可能导致 OOM
SynchronousQueue：不存储元素的队列，每个插入操作必须等待另一个线程的移除操作。

6、threadFactory (线程工厂)
用于创建新线程的工厂。可以设置线程名、优先级、是否为守护线程等，便于排查问题。
通常使用 Executors.defaultThreadFactory()。

7、handler (拒绝策略)
当线程数达到最大值且工作队列已满时，如何处理新提交的任务。

内置策略：
AbortPolicy（默认）：直接抛出 RejectedExecutionException 
CallerRunsPolicy：由提交任务的调用者线程（如 main 线程）自己执行该任务。
DiscardPolicy：直接丢弃新任务，不抛异常。
DiscardOldestPolicy：丢弃队列中最老的一个任务，然后尝试再次提交新任务。

## 9.4、线程池工作流程

提交任务后首先判断核心线程是否已满，如果没有满，创建核心线程执行任务
如果已经满了，判断工作队列是否满，没有满的话放到工作队列等待，有线程空闲了就会从工作队列获取任务执行
工作队列满了，判断核心线程数是否达到最大，没有的话创建非核心线程执行，达到最大了则执行拒绝策略

## 9.5、Executors 工厂类
|工厂方法|说明|潜在问题|
|---|---|---|
|`newFixedThreadPool(int nThreads)`|创建固定大小的线程池（核心线程数=最大线程数），使用无界队列 (`LinkedBlockingQueue`)。|**队列无界**，可能堆积大量任务导致 OOM。|
|`newSingleThreadExecutor()`|创建只有一个线程的线程池，保证任务顺序执行，使用无界队列。|**队列无界**，可能堆积大量任务导致 OOM。|
|`newCachedThreadPool()`|创建可缓存的线程池（核心线程数为0，最大线程数为`Integer.MAX_VALUE`），使用 `SynchronousQueue`。|**线程数无界**，可能创建大量线程导致 CPU 和内存耗尽。|
|`newScheduledThreadPool(int corePoolSize)`|创建支持定时及周期性任务执行的线程池。||

## 9.6、如何向线程池提交任务

有两种方法：

1. **`execute(Runnable command)`**
    - 用于提交**没有返回值**的 `Runnable` 任务。
    - 无法判断任务是否执行成功。
    executor.execute(() -> System.out.println("Running a task"));
    
2. **`submit(Callable<T> task)`** 或 **`submit(Runnable task, T result)`**
    
    - 用于提交**有返回值**的 `Callable` 任务或 `Runnable` 任务。
    - 返回一个 `Future` 对象，可以通过它**获取任务执行结果**或**取消任务**。
    
```java
  Future<String> future = executor.submit(() -> {
        // 执行一些计算
        return "Task Result";
    });
```
  

# 10、jvm
## 10.1、jvm内存模型
**1、程序计数器**

当前线程所执行的字节码的行号指示器。在JVM的概念模型里，字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令

> 特点：
（1）.为了在线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器，独立存储，互不影响。所以，程序计数器是线程私有的内存区域
（2）.如果线程执行的是一个Java方法，计数器记录的是正在执行的虚拟机字节码指令的地址；如果线程执行的是一个Native方法，计数器的值为空
（3）.Java虚拟机规范中唯一一个没有规定任何OutOfMemoryError情况的区域

**2、虚拟机栈**

每个方法执行的同时会创建一个栈帧，栈帧用于存储局部变量表、操作数栈、动态链接、方法出口等信息。每个方法从调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中入栈到出栈的过程。栈是线程私有的，它的生命周期与线程相同

> 局部变量表：存放方法参数和方法内部定义的局部变量
  操作数栈：操作数栈可理解为java虚拟机栈中的一个用于计算的临时数据存储区
  动态链接：将这些符号引用转换为调用方法的直接引用

**3、本地方法栈**

本地方法栈与虚拟机栈所发挥的作用是非常相似的，它们之间的区别不过是虚拟机栈为虚拟机执行Java方法服务（也就是字节码），而本地方法栈为虚拟机使用到的Native方法服务（一个Native Method就是一个java调用非java代码的接口）

**4、堆**

Java堆的唯一目的就是存放对象实例，几乎所有的对象实例都在这里分配内存（特例：栈上分配策略）

> 从内存回收的角度看，由于现在收集器基本都采用分代收集算法，所以Java堆可以细分为：新生代、老生代。新生代又可分为Eden和Suvivor区

（1）新生代 ( Young ) 与老年代 ( Old ) 的比例的值为 1:2

> 通过参数 –XX:NewRatio 来指定

（2）Eden : from : to = 8 : 1 : 1

> 以通过参数 –XX:SurvivorRatio 来设定

（3）最大物理内存大小不超过192兆字节（MB）时默认最大堆大小是物理内存的一半，否则占用物理内存的四分之一


**5、方法区（线程共享）**

方法区是java虚拟机的一个模型规范，具体实现是永久代和元空间。方法区存储了每个类的信息（包括类的名称、方法信息、字段信息）、常量以及编译器编译后的代码等。（GC分代收集扩展至方法区，可以不必为方法区编写专门的内存管理，但带来的后果是容易碰到内存溢出的问题。元空间占用本地内存，也就是说，只要不碰触到系统内存上限，方法区会有足够的内存空间）

> jdk1.7前：采用永久代
  jdk1.7：字符串常量池被移到堆内存（1.7前在方法区）
  jdk8：废除了永久代。类的元信息会被放入本地内存（元空间）。将类的静态变量和字符串常量池放入到java堆

## 10.2、垃圾收集算法
**1、标记-清除:** 通过根节点，标记所有根节点开始的可达对象，清除未被标记对象。这种方法很简单，但是（1）产生内存碎片，造成新来的大对象（如数组）可能没有有效的内存空间（2）标记和清除的效率低

**2、标记-整理** 该算法主要是为了解决标记-清除，产生大量内存碎片的问题；将标记的对象移动到内存的一端，清除边界外的所有空间，这样就不会产生内存碎片了。

**3、复制算法**: 将内存分为一块较大的Eden和两块较小的survivor，每次使用Eden和其中一块survivo,gc时将Eden存活对象复制到suvivor1,suvivor2存活的对象没有达到分代年龄阈值时复制到suvivor1，达到分代年龄阈值复制到老年区，之后清除Eden和suvivor2的对象,交换两个suvivor的角色,后面以此类推

**4、分代收集** 现在的虚拟机垃圾收集大多采用这种方式，它根据对象的生存周期，将堆分为新生代和老年代。在新生代中，由于对象生存期短，每次回收都会有大量对象死去，那么这时就采用复制算法。老年代里的对象存活率较高，没有额外的空间进行分配担保，所以可以使用标记-整理 或者 标记-清除。

> 复制算法在对象存活率较高时，需要较多的复制操作，效率会降低，尤其是该算法会浪费掉一部分内存，所以不适合老年代


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


