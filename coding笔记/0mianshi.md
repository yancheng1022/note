# 0、自我介绍

面试官你好，我叫郭燕成，毕业于天津科技大学，毕业后一直从事java开发。目前有5年的工作经验。现在在一家医疗企业做物联网设备数字化系统的开发工作。然后关于离职的原因，就是想结束北漂，能够回去稳定下来吧。我的情况基本就是这样。

分配任务，进度跟进，汇报工作，对接客户这些工作
# 1、项目

## 1.1、项目技术架构

我们的项目云+系统就是接收仪器传来的实验数据，故障等信息。落地这些数据后，对数据做分析处理：比如实验流程的串联，实验结果波动分析，故障预警等。实现对远程设备的监管。用到的技术栈就是springboot，mybatis，dubbo，mysql，redis，mq，sharding-jdbc这些技术



![image.png|525](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202312091541151.png)

## 1.2、项目过程遇到的问题？怎么解决
业务方面的问题比较多，但说起来可能比较麻烦我就说一个技术方面的吧
有几张表数据量非常大，查询和插入开销较大，所以考虑分表。然后这块的工作是我独立做的
1.增加sharding的数据源，所以涉及到多个数据源，做了一个数据源动态切换
2.定时任务自动建表，该月27号创建下个月的分表，还需要考虑sharding配置的刷新，因为对于运行中的项目，sharing读取的分表是在一开始确定的，所以我这里通过反射去刷新shading的分表配置
3.历史数据重新分配


## 1.3、怎么做的登录
shiro
1. 提交身份凭证：用户在应用程序的登录页面或其他身份验证入口提交身份和凭证，例如用户名和密码
2. 创建 Subject 对象：应用程序根据用户提交的身份凭证创建一个 Subject 对象，代表当前与应用程序交互的用户
3. 提交身份凭证给认证器：Subject 对象将身份凭证提交给 Shiro 的认证器（Authenticator）进行验证
4. 认证器验证身份凭证：认证器对身份凭证进行验证，通常是通过比对凭证与存储在数据源中的用户信息进行匹配。认证器可以使用一个或多个 Realm 来获取用户信息并进行验证。Realm 对获取到的用户信息与提交的身份凭证进行比对验证，判断凭证是否有效
5. 结果处理：如果身份验证成功，认证器将成功的身份信息存储在 Subject 对象中，以便后续使用

# 2、java core

## 2.1、现象对象的特征
封装：我们编写的一个实体类就是对数据和数据操作的封装，我们编写的方法就是对实现细节的封装。封装就是隐藏一切可以隐藏的东西，对外提供最简单的编程接口（普通洗衣机和全自动洗衣机之间的差别）

继承：子类继承父类的特征和行为，使得子类对象（实例）具有父类的属性和方法。以降低代码编写的冗余度

多态：父类的引用指向子类的对象。它的意义是可以让我们不用关心某个对象到底是什么具体类型，就可以使用该对象的某些方法，多态就是提供父类调用子类代码的一个手段而已。java实现多态三个必要条件：继承，重写，向上转型（父类引用指向子类对象）

## 2.2、public,private,protected,default区别？

![image.png|500](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202312221054095.png)

## 2.3、float f=3.4;是否正确？

不正确。3.4 是双精度数，将双精度型（double）赋值给浮点型（float）属于

下转型（down-casting，也称为窄化）会造成精度损失，因此需要强制类型转换

float f =(float)3.4; 或者写成 float f =3.4F;

## 2.4、int和Integer区别

Java 是一个近乎纯洁的面向对象编程语言，但是为了编程的方便还是引入了基本数据类型，但是为了能够将这些基本数据类型当成对象操作，Java 为每一个基本数据类型都引入了对应的包装类型（wrapper class），int 的包装类就是 Integer，

从 Java 5 开始引入了自动装箱/拆箱机制，使得二者可以相互转换

> 自动拆箱：在Java 1.4及以前的版本中，我们必须手动调用包装类型的intValue()、doubleValue()等方法才能将其转换为基本数据类型。而在Java 1.5及以后的版本中，我们可以直接将包装类型赋值给基本数据类型变量，Java编译器会自动将包装类型转换为对应的基本数据类型
> 自动装箱：Java自动装箱（Autoboxing）是指Java编译器在需要的时候，会自动将原始数据类型转换为对应的包装类类型（Integer b = 3）

```java
class AutoUnboxingTest {
	public static void main(String[] args) {
		Integer a = new Integer(3);
		Integer b = 3;
		// 将 3 自动装箱成 Integer 类型
		int c = 3;
		System.out.println(a == b);// false 两个引用没有引用同一对象
		System.out.println(a == c);// true a 自动拆箱成 int 类型再和 c比较
	}
	// 如果整型字面量的值在-128 到 127 之间，那么不会 new 新的 Integer对象，而是直接引用常量池中的 Integer 对象，所以上面的面试题中 f3f4 的结果是 false
	public static void main(String[] args) {
		Integer f1 = 100, f2 = 100, f3 = 150, f4 = 150;
		System.out.println(f1 == f2); // true
		System.out.println(f3 == f4); // false
	}
}
```


## 2.5、最有效率的方法计算2乘以8

2 << 3（左移 3 位相当于乘以 2 的 3 次方，右移 3 位相当于除以 2 的 3 次方）

>位运算直接操作二进制位，而不涉及额外的类型转换或复杂的计算


## 2.6、在 Java 中，如何跳出当前的多重嵌套循环？

在最外层循环前加一个标记如 A，然后用 break A;可以跳出多重循环。（Java 中支持带标签的 break 和 continue 语句，作用有点类似于 C 和 C++中的 goto 语句，但是就像要避免使用 goto 一样，应该避免使用带标签的 break 和 continue，因为它不会让你的程序变得更优雅，很多时候甚至有相反的作用，所以这种语法其实不知道更好）

```java
public static void main(String[] args) {  
    int a = 0;  
    LABEL_1:  
    for (int i = 0; i < 10; i++) {  
        for (int j = 0; j < 10; j++) {  
            a = j * i;  
            if (a == 300) {  
                break LABEL_1;  
            }  
        }  
    }  
}
```
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
## 5.1、synchronized使用


# 6、java基础

## 6.1、用户自己写一个String类会发生什么
假设用户自己写了一个String类，就会加载不进内存。
原因：
基于JVM的双亲委派机制，类加载器收到了加载类的请求，会把这个请求委派给他的父类加载器。
而只有父类加载器自己无法完成加载请求时，子类才会自己加载。
这样用户自定义的String类的加载请求就会最终达到顶层的BootStrap ClassLoader启动类加载器，
启动类加载器加载的是系统中的String对象，而用户编写的java.lang.String不会被加载。

![image.png|425](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202312221514055.png)


# 7、mq

## 7.1、rabbitmq如何保证可靠性

消息丢失的场景：
1. 生产者到mq server：可能网络，无法收到消息
> 消息从 producer 到 rabbitmq broker有一个 confirmCallback 确认模式。(无论成功失败都有返回)
> 在回调函数中处理失败的业务


2. mq server中的交换机到队列：肯能代码或配置方面的问题导致失败
>消息从 exchange 到 queue 投递失败有一个 returnCallback 退回模式。（失败时才会有返回）
>在回调函数中处理

3. mq server中存储的消息丢失：mq server宕机，但没有持久化消息
>在初始化队列的时候就有个参数是是否持久化

4. 消费端异常：消费者宕机或故障
>消费者端消息接收确认采用的是ack模式。自动ACK：消息一旦被接收，消费者自动发送ACK，队列删除消息。手动ACK：消息接收后，不会发送ACK，需要手动调用
 如果消息不太重要，丢失也没有影响，那么自动ACK会比较方便
 如果消息非常重要，不容丢失。那么最好在消费完成后手动ACK，否则接收消息后就自动ACK，RabbitMQ就会把消息从队列中删除


## 7.2、rabbitmq避免重复消费
保证消费者的幂等性（调用方，对一个系统进行重复调用（参数全部相同），不论重复调用多少次，这些调用对系统的影响都是相同的效果）

如何保证幂等性？
1. 使用代码的逻辑判断，判断消息状态是否已经被消费过了

> 使用数据库一个表来记录消息的状态（或者用redis来记录也可以）。每次消费之前，都查询判断消息的状态，是否已经被消费了。这个状态可以是id。例如，如果消息是订单，而且id是全局唯一的，那么只需要拿这个订单id来做判断即可。

2. 使用token，要申请，一次有效性。

> 在创建订单的场景下。首先，先生成一个token，返回给客户端存起来，同时也在后端存起来（redis）。当他创建订单的时候，带着这个token来请求后端，后端判断redis里是否存在，如果存在，则操作成功，同时删除token（删除了之后，就算他重复多次调用，前边的判断不成立，这样子就不能多次操作了）


# 8、jvm

## 8.1、常用jvm启动参数

```
# 设置堆内存
-Xms4g  -Xmx4g  最小值和最大值
# 指定gc算法
-xx:+UseG1GC -xx:MaxGCPauseMillis=50 最大暂停时间50毫秒
# 打印GC日志
-XX:+PrintGCDetails 
# 指定元空间最大值
-XX:MaxMetaspaceSize=2g
# 堆内存溢出时自动dump
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/usr/local
```


## 8.2、CPU飙升怎么排查

1.先通过top命令找到消耗cpu很高的进程id
2.top -p 2732 单独监控该进程
3.在第二步的界面输入H，获取当前进程下的线程信息
4.找到cpu占用特别高的线程id
5.执行jstack 2732对当前进程做dump，输出所有的线程信息。同时将第四步得到的线程id转为16进制，在堆栈信息里去找对应的线程内容
6.最后解读线程信息，定位具体代码位置


# 9、mybatis