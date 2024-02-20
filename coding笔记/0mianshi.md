# 0、自我介绍

面试官你好，我叫郭燕成，毕业于天津科技大学，毕业后一直从事java开发。目前有5年的工作经验。现在在一家医疗企业做物联网数字化系统的开发工作.用到的技术栈就是springboot，dubbo，mybatisplus，redis，rabbitmq，shading-jdbc。然后关于离职的原因，就是想结束北漂，能够回去稳定下来吧。我的情况基本就是这样。

分配任务，进度跟进，汇报工作，对接客户这些工作
1d 1
# 1、项目

## 1.1、项目技术架构

我们的项目云+系统就是接收仪器传来的实验数据，故障等信息。落地这些数据后，对数据做分析处理：比如实验试剂消耗量统计，实验流程的串联，实验结果波动分析，仪器故障预警等。实现对远程设备的监管。用到的技术栈就是springboot，mybatis，dubbo，mysql，redis，mq，sharding-jdbc这些技术


![image.png|525](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202312091541151.png)

## 1.2、项目过程遇到的问题？怎么解决
业务方面的问题比较多，但说起来可能比较麻烦我就说一个技术方面的吧
刚开始的功能没有考虑分表，因为涉及到的仪器很少，后来效果好，有几张表数据量非常大，查询和插入开销较大，所以考虑分表。然后这块的工作是我独立做的
1.增加sharding的数据源，所以涉及到多个数据源，做了一个数据源动态切换
2.定时任务自动建表，该月27号创建下个月的分表，还需要考虑sharding配置的刷新，因为对于运行中的项目，sharing读取的分表是在一开始确定的，所以我这里通过反射去刷新shading的分表配置
3.历史数据重新分配


瞬时大数据量数据上报

仪器产生的文件数据是在第二天1点上报，仪器数量少时没有问题，但是随着接入仪器数量增多。数据接收效率太低：
（1）离散上线，离散间隔（5min）* (id % 离散间隔段100)
（2）集群设置（4台服务器，fair：最短响应时间）
（3）一个文件数据解析十几个流程，涉及到十几张表的插入操作。（将消息分组，用多个线程处理不同的消息类型。同时使用批量插入（手动获取sqlsession，开启批量提交））


## 1.3、怎么做的登录
shiro
1. 提交身份凭证：用户在应用程序的登录页面或其他身份验证入口提交身份和凭证，例如用户名和密码
2. 创建 Subject 对象：应用程序根据用户提交的身份凭证创建一个 Subject 对象，代表当前与应用程序交互的用户
3. 提交身份凭证给认证器：Subject 对象将身份凭证提交给 Shiro 的认证器（Authenticator）进行验证
4. 认证器验证身份凭证：认证器对身份凭证进行验证，通常是通过比对凭证与存储在数据源中的用户信息进行匹配。认证器可以使用一个或多个 Realm 来获取用户信息并进行验证。Realm 对获取到的用户信息与提交的身份凭证进行比对验证，判断凭证是否有效
5. 结果处理：如果身份验证成功，认证器将成功的身份信息存储在 Subject 对象中，以便后续使用

## 1.4、ERP的流程

ERP系统的业务流程：本质上包括了五大业务模块：生产、采购、销售、库存、财务，把多种数字化解决方案和经验模型结合在一起，从而解决了四大业务流程：生产流、采购流、销售流、仓储流：
【生产流】：生产计划 → 生产工单 → 生产领料/退料 → 生产报工 → 成品入库
【销售流】：客户 → 销售订单 → 出库 → 退换货 → 应收账款 → 对账 → 开票 → 收款
【采购流】：供应商 → 采购订单 → 入库 → 退换货 → 应付账款 → 对账 → 收票 → 付款
【仓储流】：仓库 → 其他出入库、调拨、盘点 → 库存


# 2、java core

## 2.1、面向对象的特征
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


## 2.7、hashcode和equals？

不对，如果两个对象 x 和 y 满足 x.equals(y) == true，它们的哈希码（hash code）应当相同。Java 对于 eqauls 方法和 hashCode 方法是这样规定的：(1)如果两个对象相同（equals 方法返回 true），那么它们的 hashCode 值一定要相同；(2)如果两个对象的 hashCode 相同，它们并不一定相同。当然，你未必要按照要求去做，但是如果你违背了上述原则就会发现在使用容器时，相同的对象可以出现在 Set 集合中

## 2.8、java是值传递还是引用传递

在Java中，传递参数时使用的是值传递（pass-by-value）机制。这意味着方法接收的是实际参数值的副本，而不是原始参数的引用。


对于基本数据类型（如int、float、boolean等），传递的是该值的副本，方法内对参数的修改不会影响原始变量的值。

对于引用类型（如对象、数组等），传递的是引用的副本，即对象的内存地址。这里需要注意的是，虽然传递的是引用的副本，但实际上还是按值传递。在方法内部，通过传递的引用副本，可以访问和修改原始对象的内容，但如果方法内部对引用本身进行修改（重新指向另一个对象），则不会影响原始引用

```java
StringBuilder sb = new StringBuilder("iphone"); 
void foo(StringBuilder builder) { 
builder = new StringBuilder("ipad"); 
} 
foo(sb); // sb 没有被改变，还是 "iphone"。
```


## 2.9、String 和 StringBuilder、StringBuffer 的区别？

1. 可变性
String类中使用字符数组保存字符串private final charr value[]，所以string对象是不可变的。StringBuilder与StringBuffer都继承自AbstractStringBuilder类，在AbstractStringBuilder中也是使用字符数组保存字符串，char[] value，这两种对象都是可变的
2. 线程安全性
String是不可变的，可以理解为常量，所以线程安全。StringBuffer加同步锁，所以线程安全，stringBuilder没加，线程不安全

3. 性能
每次对String改变都会生成一个新的String对象。StringBuffer和StringBuilder对对象本身操作。性能 StringBuilder > StringBuffer > String

## 2.10、char 型变量中能不能存贮一个中文汉字，为什么

char 类型可以存储一个中文汉字，因为 Java 中使用的编码是 Unicode，一个 char 类型占 2 个字节（16 比特），所以放一个中文（unicode下两字节）是没问题的

## 2.11、抽象类和接口的区别

1. 概念不同：抽象类是对根源的抽象，接口是对动作的抽象。抽象类表示这个对象是什么（例如男人，女人的抽象类是人） 接口表示的是这个对象能做什么（吃东西）
2. 一个类只能继承一个抽象类，可以实现多个接口
3. 抽象类可以定义构造器，可以有具体方法和抽象方法。而接口不能定义构造器而且其中的方法全部是抽象方法

## 2.12、如何实现对象克隆

1. 实现Cloneable接口并重写Object类中的clone（）方法
2. 实现 Serializable 接口，通过对象的序列化和反序列化实现克隆，可以实现真正的深度克隆，代码如下

>**浅拷贝**：基本数据类型复制值，引用数据类型复制地址，即拷贝出来的对象与被拷贝出来的对象中的引用的对象是同一个（java默认）：clone方法
 **深拷贝**：基本数据类型复制值，引用数据类型，创建一个新的对象，并复制其内容


```java

// 使用对象序列化来实现克隆
public class Room implements Serializable {
    Desk desk;
 
    public Room deepClone() throws Exception{
        //序列化
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        ObjectOutputStream objectOutputStream = new ObjectOutputStream(outputStream);
        objectOutputStream.writeObject(this);
        //反序列化
        ByteArrayInputStream inputStream = new ByteArrayInputStream(outputStream.toByteArray());
        ObjectInputStream objectInputStream = new ObjectInputStream(inputStream);
        Room roomClone = (Room)objectInputStream.readObject();
        return roomClone;
        
    }
}

```

## 2.13、类初始化顺序

```java
class A {
	static {
		System.out.print("1");
	}
	
	public A() {
		System.out.print("2");
	}
	
}
	
class B extends A{
	static {
		System.out.print("a");
	}
	
	public B() {
		System.out.print("b");
	}
}

public class Hello {
	public static void main(String[] args) {
		A ab = new B();
		ab = new B();
	}
}
```

结果：1a2b2b
初始化顺序：静态成员 - 父类构造器 - 非静态成员 - 子类构造器

## 2.14、异常体系


![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202310301636988.png)


Error：错误，无法被处理的

Exception：异常，能够被程序本身处理的，可以通过try…catch语句捕捉异常，或者是throws抛出异常。分为运行时异常和非运行时异常
- 运行时异常：就是RuntimeException，编译时不会检查出错误的。一般是由于逻辑错误引起的，程序员可以手动去解决的，比如判空等。
- 非运行时异常：也叫编译异常，就是Exception下除了RuntimeException以外的异常。是必须进行处理的异常，编译器会进行异常提醒的。如果不进行处理，程序编译不通过

## 2.15、hashmap原理

hashmap数据结构是数组+链表+红黑树，HashMap的主干是一个Entry数组。Entry是HashMap的基本组成单元，每一个Entry包含key-value键值对  
两个重要的方法put() get()  
（1）.put方法：调用key的hash方法得hash值，再与（数组长度-1）做与（&）运算，到这个元素在数组中的位置（即下标）如果该位置已经存在其它元素，那么在同一个位子上的元素将以链表的形式存放，通过equals方法依次比较链表中的key，相同则替换。不同则添加到表尾（1.8之前添加到表头）。

（2）.get方法：调用key的hash方法得到这个元素在数组中的位置（即下标），然后通过key的equals方法在对应位置的链表中找到需要的元素。

>（需要注意Jdk 1.8中对HashMap的实现做了优化,当链表长度大于8且数组长度超过64并之后,该链表会转为红黑树来提高查询效率,从原来的O(n)到O(logn)）
> 如果数组长度不足64，优先会进行扩容


## 2.16、各容器扩容机制

1、arrayList：如果没有指定大小，默认创建空数组，在第一次添加元素的时候，将容量扩容为默认值10，如果添加时，发现容量不够，则扩容为原来1.5倍（创建新数组并拷贝）
2、hashmap：第一次put的时候容量初始化为16，如果超过阈值（加载因子0.75乘当前数组长度）数组长度扩容为原来的两倍
# 3、spring
## 3.1、ioc

反转控制，将创建Bean及其依赖对象的工作交给IOC容器管理，业务代码只需要getBean就行了。在容器初始化（或@lazy实现懒加载）通过反射机制创建好对象，在使用时直接从容器中获取。

实现原理：工厂模式 + 反射机制


## 3.2、spring依赖注入方式

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

## 3.3、bean Factory和applicationContext

1. BeanFactory它定义了IOC的基本功能，主要定义了getBean方法，它的功能是返回特定的名称的Bean。ApplicationContext由BeanFactory派生而来，提供了更多面向实际应用的功能。（1）MessageSource, 提供国际化的消息访问 （2）资源访问，如URL和文件  （3）事件传播特性，即支持aop特性（4）载入多个（有继承关系）上下文 ，使得每一个上下文都专注于一个特定的层次，比如应用的web层

2. BeanFactroy采用延迟加载形式来注入Bean，即只有在使用到某个Bean时(调用getBean())，才对该Bean进行加载实例化，这样就不能发现一些存在的Spring的配置问题。而ApplicationContext则采用立即加载，它是在容器启动时，一次性创建了所有的Bean。这样，在容器启动时，我们就可以发现Spring中存在的配置错误


## 3.4、spring bean生命周期

1、 实例化（Instantiation）：当Spring容器接收到Bean的定义时，会使用反射机制创建一个Bean实例。
2、属性赋值（Populate Bean）： Spring 将值和bean的引用注入到bean对应的属性中
3、回调实现Aware接口的方法。BeanNameAware，BeanFactoryAware，ApplicationContextAware对应的方法。

> Spring的依赖注入的最大亮点就是你所有的Bean对Spring容器的存在是没有意识的。即你可以将你的容器替换成别的容器，例如Goggle Guice,这时Bean之间的耦合度很低。
> 但是在实际的项目中，我们不可避免的要用到Spring容器本身的功能资源，这时候Bean必须要意识到Spring容器的存在，才能调用Spring所提供的资源，这就是所谓的Spring Aware。其实Spring Aware本来就是Spring设计用来框架内部使用的，若使用了Spring Aware，你的Bean将会和Spring框架耦合。  

4、初始化（Initialization）：分别调用（1）BeanPostProcessor的前置处理器，（2）InitialzingBean的afterPropertiesSet（）方法，（3）调用init初始化方法（4）调用BeanPostProcessor的后置处理器
5、使用（In Use）：在初始化完成之后，Bean就可以被使用了。
6、销毁（Destruction）：当Spring容器关闭时，会销毁所有的Bean。在销毁Bean之前，（1）如果实现DisposableBean接口，Spring将调用它的destory()接口方法（2）如果bean使用destroy-method声明了销毁方法，该方法也会被调用

## 3.5、@Resource和@Autowired区别

@Autowired是spring注解，默认情况是按类型（byType）匹配。如果需要按名称（byName）可以使用@Qualifier与@Autowired结合 
@Resource是j2ee的注解，默认按byName模式自动注入。如果设置了type属性，则byType注入

## 3.6、@component和@bean区别

1. 用途不同：@component用于标识普通类，@bean是在配置类中声明和配置bean对象
2. 使用方式不同：@Component是类级别的注解，@Bean是修饰配置类中方法，将方法的返回值注入ioc容器

## 3.6、spring aop

spring aop是基于代理模式实现的，它通过动态代理技术，在运行时生成代理对象，从而实现对目标方法的拦截和增强。便于降低代码的耦合度，有利于未来的扩展和维护性.具体使用场景：接口日志，接口权限，事务管理

如果代理对象实现了某个接口，spring会使用jdk动态代理创建对象。反之使用CGLib动态代理生成一个被代理对象的子类作为代理

spring动态代理的实现有以下两种方式:
1.JDK 动态代理。它是通过反射来接收被代理的类，并且要求被代理的类必须实现一个或多个接口;JDK 动态代理的核心是java.lang.reflect 包中的 Proxy 类和InvocationHandler 接口

2.CGlib 动态代理，CGlib 全称 Code Generation Library，它是一个第三方代码生成类库、CGlib 可以在运行时动态生成一个子类对象从而实现对目标对象功能的扩展。CGlib 的底层是通过使用一个小而快的字节码处理框架 ASM，来转换字节码并生成新的类。

> JDK 动态代理和 CGlb 动态代理的主要区别是:使用JDK 动态代理的对象必须实现一个或多个接口。而 CGlib 动态代理的对象则无需实现接口。

>jdk，它是将原始对象放入代理对象内部，通过调用内含的原始对象来实现原始的业务逻辑
>cglib，它是通过生成原始对象的子类，子类复写父类的方法，从而实现对父类的增强

## 3.7、aop相关术语

（1）Join Point：连接点，表示一个程序在执行过程中的一个点，通常就是一个方法的执行
（2）Point Cut：切点，用来匹配一个或多个连接点
（3）Advice：通知，表示在切入点具体执行的逻辑。包括前置，后置，返回，异常，环绕通知
（4）切面：切点+通知
（5）织入：将切面应用到应用程序的过程

## 3.8、springmvc执行流程

1. 客户端发送请求到DispatcherServlet。
2. DispatcherServlet根据请求的URL找到对应的handler（controller）。返回对应的HandlerExecutionChain，其中包含处理请求的Controller以及拦截器。
4. DispatcherServlet调用处理器适配器handlerAdapter选择合适的处理器handler处理（controller，httprequesthandler，servlet）
6. 返回一个ModelAndView对象（jsp）。前后端分离在处理器直接返回json，然后返回一个null的modelAndView
7. DispatcherServlet根据返回的ModelAndView对象，调用对应的ViewResolver进行视图解析。
8. ViewResolver返回对应的View对象。然后结合之前返回的数据，进行视图渲染
9. DispatcherServlet将渲染后的视图返回给客户端

## 3.9、spring bean的作用域

- singleton：单例模式，在整个Spring IoC容器中，使用singleton定义的Bean将只有一个实例
- prototype：原型模式，每次通过容器的getBean方法获取prototype定义的Bean时，都将产生一个新的Bean实例
- request：对于每次HTTP请求，使用request定义的Bean都将产生一个新实例，即每次HTTP请求将会产生不同的Bean实例。只有在Web应用中使用Spring时，该作用域才有效
- session：对于每次HTTP Session，使用session定义的Bean豆浆产生一个新实例。同样只有在Web应用中使用Spring时，该作用域才有效
- globalsession：每个全局的HTTP Session，使用session定义的Bean都将产生一个新实例。典型情况下，仅在使用portlet context的时候有效。同样只有在Web应用中使用Spring时，该作用域才有效

## 3.10、spring bean是线程安全的吗

对于原型Bean,每次创建一个新对象，也就是线程之间并不存在Bean共享，自然是不会有线程安全的问题

对于单例Bean,所有线程都共享一个单例实例Bean,因此是存在资源的竞争

>怎样解决单例bean对象的线程安全问题
>1.在bean对象中尽量避免使用可变的成员变量
>2.在Bean对象中定义一个ThreadLocal成员变量，将需要的可变成员变量保存在ThreadLocal中

## 3.11、springBoot自动配置原理


1、通过@SpringBootApplication引入@EnableAutoConfiguration，负责启动自动配置功能
2、@EnableAutoConfiguration引入了@Import注解，导入一个ImportSelector
3、它会读取所有/META-INF/spring.factory文件，然后把扫描到的文件包装成properties对象
4、从properties中获取EnableAutoConfiguration的key里对应的所有的类
5、通过@condition注解加载所需要的自动配置类


## 3.12、springBoot启动流程

1、加载配置文件。读取application.properties配置文件，获取应用程序的配置信息
2、创建spring容器。创建一个spring容器，用于管理应用程序中的所有bean。并提供依赖注入，aop等功能
3、扫描注解。扫描@controller,@service,@repository等，将它们转化成springBean
4、自动配置：扫描mate-inf下的spring.factories,获取自动配置类，根据条件注入
5、启动应用程序，内嵌一个web容器，监听HTTP请求

## 3.13、拦截器和过滤器


1、使用范围不同：过滤器依赖于Servlet容器，拦截器不依赖与servlet容器是SpringMVC自带的
2、触发时机不同：Filter是在请求进入容器后，但在进入servlet之前进行预处理。拦截器 Interceptor 是在请求进入servlet后，在进入Controller之前进行预处理的
3、用途不同：过滤器主要用于对用户请求进行过滤和修改，防止xss攻击等。拦截器主要用于业务处理的增强，比如日志记录、身份验证等方面


## 3.14、防止xss攻击

跨站脚本攻击。常见的就是反射型xss攻击，过URL传递参数的功能，如网站搜索，跳转等。由于需要用户主动打开恶意的URL才能生效，攻击者往往会结合多种手段诱导用户点击。攻击者构造出特殊的URL，其中包含恶意代码.网站服务器端将恶意代码从URL取出，拼接在HTML返回给浏览器。恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户行为，调用目标网站接口执行攻击者指定的操作

使用过滤器，清除请求参数url中的html标签

## 3.15、CommandLineRunner和ApplicationRunner

我们在开发过程中会有这样的场景：需要在容器启动的时候执行一些内容，比如：读取配置文件信息，数据库连接，删除临时文件，清除缓存信息。在Spring Boot中提供了CommandLineRunner和ApplicationRunner，他们的执行时机为容器启动完成的时候

CommandLineRunner和ApplicationRunner的作用是相同的。不同之处在于CommandLineRunner接口的run()方法接收String数组作为参数，即是最原始的参数，没有做任何处理；而ApplicationRunner接口的run()方法接收ApplicationArguments对象作为参数，是对原始参数做了进一步的封装。

当程序启动时，我们传给main()方法的参数可以被实现CommandLineRunner和ApplicationRunner接口的类的run()方法访问，即可接收启动服务时传过来的参数。我们可以创建多个实现CommandLineRunner和ApplicationRunner接口的类。为了使他们按一定顺序执行，可以使用@Order注解

## 3.16、spi机制

SPI（Service Provider Interface），是JDK内置的一种 服务提供发现机制。简单来说，就是我们可以定义一个标准的接口，然后第三方的库里面可以实现这个接口。很灵活的让接口和实现分离。那么程序在运行的时候，会根据配置信息动态加载第三方实现的类库，从而完成功能的动态扩展机制

其原理是在META-INF/services目录下创建一个以接口全限定名为名称的文件，文件内容为实现接口的全限定名。当框架需要使用该服务时，通过ClassLoader加载META-INF/services目录下的配置文件，然后通过反射机制实例化服务实现类。
## 3.17、自定义stater


SpringBoot中的starter是一种非常重要的机制，能够抛弃以前繁杂的配置，将其统一集成进starter，应用者只需要在maven中引入starter依赖，Spring Boot就能自动扫描各个jar包下classpath路径的spring.factories文件，加载自动配置类信息，加载相应的bean信息并启动相应的`默认配置`

1、创建starter项目

SpringBoot官方命名方式
格式：spring-boot-starter-{模块名}
举例：spring-boot-starter-web
自定义命名方式
格式：{模块名}-spring-boot-starter
举例：mystarter-spring-boot-starter


2、添加依赖

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-autoconfigure</artifactId>
   <optional>2.2.9.RELEASE</optional>
</dependency>
```

3、编写bean

```java
//@Component
@EnableConfigurationProperties(MyBean .class)
@ConfigurationProperties(prefix = "mybean")
public class MyBean {
	private int id;private String name;//省略setter和getter
}
```

4、编写自动配置类

```java
@Configuration
public class MyBeanAutoConfiguration {
	@Bean
	public MyBean myBean(){
		return new MyBean();
	}
}
```


5、编写spring.factories

```txt
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\ com.blue.config.MyBeanAutoConfiguration
```


6、打包

```txt
mvn clean install
```

7、测试

```java
<dependency>
	<groupId>com.blue</groupId>
	<artifactId>mybean-spring-boot-starter</artifactId>
	<version>1.0-SNAPSHOT</version>
</dependency>

@RunWith(SpringRunner.class)
@SpringBootTest(classes = TestApplication.class)
class TestApplicationTests {
	@Autowiredprivate 
	MyBean ba;
	
	@Testvoid contextLoads() {
		System.out.println(ba.getName());
	}
}
```

8、实现热插拔

```java
// 1. 新增标记类
public class ConfigMarker {
}

// 2.定义@EnableMyBean注解
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Import({ConfigMarker.class})
public @interface EnableMyBean{}

// 3.改造配置类
@Configuration
@ConditionalOnBean(ConfigMarker.class)
public class MyBeanAutoConfiguration {
	@Beanpublic 
	MyBean myBean(){
		return new MyBean();
	}
}

// 在测试启动类上开启MyBean功能
@EnableMyBean
@SpringBootApplication
public class TestApplication {
	public static void main(String[] args) {
		SpringApplication.run(TestApplication.class, args);
	}
}
```


## 3.18、maven命令

clean：清除已编译的文件，即删除target
package：打包，包含构建，编译等过程
install：将项目打成jar包，并保存到本地maven仓库
compile：对当前项目进行编译操作
deploy：依次执行clean compile package，并上传到远程仓库

## 3.19、spring如何解决循环依赖


# 4、mybatis

## 4.1、实现批量插入

```java
sqlsession sqlsession = sqlsessionfactory.opensession(executortype.batch);
try {
	namemapper mapper = sqlsession.getmapper(namemapper.class);
for (string name: names) {
	mapper.insertname(name);
}
	sqlsession.commit();
} catch (Exception e) {
	e.printStackTrace();
	sqlSession.rollback();
	throw e;
}
finally {
	sqlsession.close();
}
```

## 4.2、获取自动生成主键值

usegeneratedkeys=”true” keyproperty=”id”

```java
<insert id=”insertname” usegeneratedkeys=”true” keyproperty=”id”>
	insert into names (name) values (#{name})
</insert>

name name = new name();
name.setname(“fred”);
int rows = mapper.insertname(name);

// 完成后,id 已经被设置到对象中
system.out.println(“rows inserted = ” + rows);
system.out.println(“generated key value = ” + name.getid());

```

## 4.3、mapper如何传递多个参数

1.#{0}
```xml
<!-- ,#{0}代表接收的是 dao 层中的第一个参数，#{1}代表 dao 层中第二参数-->
<select id="selectUser"resultMap="BaseResultMap">
	select * fromuser_user_t whereuser_name = #{0} and user_area=#{1}
</select>
```

2.使用@param注解



## 4.4、mybatis一对一，一对多

一对一：association (学生-班级)

```java
public class Student {
	private int id;
	private String name;
	private ClassStu cls;
}

	<resultMap type="domain.Student" id="clastu">
        <id column="sid" property="id"/>
        <result column="sname" property="name"/>
        <!-- 一位学生对应一个班级，所以选用association标签 -->
        <association property="cls" javaType="domain.ClassStu">
            <id column="cid" property="id"/>
       		<result column="cname" property="name"/>
        </association>
    </resultMap>
    
    <select id="select01" resultMap="clastu">
        select 
        	class.id as cid,
        	class.name as cname,
        	student.id as sid,
        	student.name as sname
	        from class inner join student on class.id = student.cid
    </select>
```

一对多：collection（班级-学生）

```java
public class ClassStu {
	private int id;
	private String name;
	private List<Student> list;
}

	<resultMap type="domain.ClassStu" id="clastu2">
        <id column="cid" property="id"/>
       	<result column="cname" property="name"/>
        <collection property="list" ofType="domain.ClassStu">
            <id column="sid" property="id"/>
        	<result column="sname" property="name"/>
        </collection>  
    </resultMap>
```


## 4.5、延迟加载

就是在需要用到数据时才进行加载，不需要用到数据时就不加载数据。延迟加载也称懒加载。 先从单表查询，需要时再从关联表去关联查询，⼤⼤提⾼数据库性能，因为查询单表要比关联查询多张表速度要快。一对多，多对多：通常情况下采用延迟加载（**延迟加载是基于嵌套查询来实现的**）

>关联查询：
>SELECT orders., user.username FROM orders, USER WHERE orders.user_id = user.id
延迟加载相当于：
 SELECT orders.,
 (SELECT username FROM USER WHERE orders.user_id = user.id)username FROM orders

mybatis默认没有开启延迟加载，需要手动开启

1.局部延迟加载
在**association**和**collection**标签中都有⼀个**fetchType属性**，通过修改它的值，可以修改局部的加载策略。

```xml
<!-- 开启⼀对多 延迟加载 -->
<resultMap id="userMap" type="user">
    <id column="id" property="id"></id>
    <result column="username" property="username"></result>
    <result column="password" property="password"></result>
    <result column="birthday" property="birthday"></result>
<!--
fetchType="lazy" 懒加载策略
fetchType="eager" ⽴即加载策略
-->
    <collection property="orderList" ofType="order" column="id"
        select="com.lagou.dao.OrderMapper.findByUid" fetchType="lazy">
    </collection>
</resultMap>
<select id="findAll" resultMap="userMap">
    SELECT * FROM `user`
</select>
```

2.全局延迟加载

```properties
mybatis.configuration.lazyLoadingEnabled=true
```


## 4.6、mybatis一级缓存，二级缓存

一级缓存：在一次 SqlSession 中（数据库会话），程序执行多次查询，且查询条件完全相同，多次查询之间程序没有其他增删改操作，则第二次及后面的查询可以从缓存中获取数据，避免走数据库

一级缓存失效场景：（1）不同的SqlSession对应不同的一级缓存（2）同一个SqlSession但是查询条件不同（3）同一个SqlSession两次查询期间执行了任何一次增删改操作（4）同一个SqlSession两次查询期间手动清空了缓存

二级缓存：namespace级别的缓存，二级缓存被同一个 `namespace` 下的多个 `SqlSession` 共享，是一个全局的变量。MyBatis 的二级缓存不适应用于映射文件中存在多表查询的情况

> 通常我们会为每个单表创建单独的映射文件，由于MyBatis的二级缓存是基于`namespace`的，多表查询语句所在的`namspace`无法感应到其他`namespace`中的语句对多表查询中涉及的表进行的修改，引发脏数据问题

缓存查询顺序：
- 先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用
- 如果二级缓存没有命中，再查询一级缓存
- 如果一级缓存也没有命中，则查询数据库
- SqlSession关闭之后，一级缓存中的数据会写入二级缓存

一级缓存默认开启，二级缓存开启需要在mapper.xml里加cache标签


## 4.7、mybatis插件原理

Mybatis的插件相当于拦截器，我们可以针对Executor，StatementHandler，ParameterHandler，ResultSetHandler这四种接口的插件。通过jdk动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这四种接口对象方法时，执行插件类的增强方法，进行方法的拦截处理

>Executor：执行CURD操作；
 StatementHandler：处理sql语句预编译，设置参数等相关工作；
 ParameterHandler：设置预编译参数用的；
 ResultSetHandler：处理结果集

具体实现：（1）实现mybatis的Interceptor接口，并重写intercept（）方法 （2）设置插件的签名，指定mybatis要拦截哪些方法 （4）注册插件，在配置文件中配置自己编写的插件类


```java
/**
 * Mybatis分页插件 具体实现
 */

@Intercepts(@Signature(
        type = StatementHandler.class,
        method = "prepare",
        args = {Connection.class, Integer.class}
))
@Slf4j
@Component
public class MyPagePlugin implements Interceptor {


    /**
     * 核心业务
     * <p>
     * 1、拿到原始sql
     * 2、修改原始sql，增加分页
     * 3、执行jdbc查询总数
     */

    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        // 从invocation获取statementHandler对象
        StatementHandler statementHandler = (StatementHandler) invocation.getTarget();
        BoundSql boundSql = statementHandler.getBoundSql();
        // 原始sql
        String sql = boundSql.getSql();
        log.info("原始sql===>>>{}", sql);

        // 获取分页参数
        Object parameterObject = boundSql.getParameterObject();

        // 获取MappedStatement对象 (对应是就是我们写sql的xml文件)
        MetaObject metaObject = SystemMetaObject.forObject(statementHandler);
        MappedStatement mappedStatement = (MappedStatement) metaObject.getValue("delegate.mappedStatement");
        // 获取mapper接口中方法名称
        String methodName = mappedStatement.getId();
        // 拦截方法名为ByPage结尾的方法 只拦截涉及到分页的方法
        if (methodName.matches(".*ByPage$")) {
            Map<String, Object> parameterMap = (Map<String, Object>) parameterObject;
            PageInfo pageInfo = (PageInfo) parameterMap.get("pageInfo");
            // 页大小
            int pageSize = pageInfo.getPageSize();
            // 当前页数
            int currentPage = pageInfo.getCurrentPage();
            // 查总数
            String countSql = "select count(0) from (" + sql + ") t";
            log.info("查总数sql===>>>{}", countSql);

            // 执行jdbc操作
            Connection connection = (Connection) invocation.getArgs()[0];
            PreparedStatement countStatement = connection.prepareStatement(countSql);
            ParameterHandler parameterHandler = (ParameterHandler) metaObject.getValue("delegate.parameterHandler");
            parameterHandler.setParameters(countStatement);
            ResultSet resultSet = countStatement.executeQuery();
            if (resultSet.next()) {
                pageInfo.setTotalNumber(resultSet.getInt(1));
                resultSet.close();
                countStatement.close();
            }
            // 改造sql limit
            String pageSql = initPageSql(sql, pageInfo);
            log.info("分页sql===>>>{}", pageSql);
            metaObject.setValue("delegate.boundSql.sql", pageSql);
        }
        return invocation.proceed();
    }

    @Override
    public Object plugin(Object target) {
        return Plugin.wrap(target, this);
    }

    @Override
    public void setProperties(Properties properties) {

    }

    /**
     * @param sql 原始sql
     * @param pageInfo 分页对象
     * @return 分页sql
     */
    private String initPageSql(String sql, PageInfo pageInfo) {
        int totalNumber = pageInfo.getTotalNumber();
        if (totalNumber <= 0) {
            return sql;
        }
        int pageSize = pageInfo.getPageSize();
        int currentPage = pageInfo.getCurrentPage();

        if (currentPage <= 0) {
            currentPage = 1;
        }
        if (pageSize <= 0) {
            pageSize = 10;
        }

        int offset = (currentPage - 1) * pageSize;

        StringBuilder sb = new StringBuilder();
        sb.append(sql).append(" limit ").append(offset).append(",").append(pageSize);
        return sb.toString();
    }
}

在mybatis-config.xml中注册自己写的插件

<configuration>
    <!-- 引入 自己写的MyBatis 分页插件 -->
    <plugins>
        <plugin interceptor="cn.kinggm520.util.MyPagePlugin"/>
    </plugins>
</configuration>



```


## 4.8、@Transactional原理

基于数据库事务和aop机制实现的
spring会创建一个代理对象作，利用事务管理器创建一个数据库连接，并且修改数据库连接的autocommit属性为false，禁止连接自动提交
如果抛异常，则调用conn.rollback方法回滚，否则调用commit（）提交

## 4.9、@Transactional属性

| **参 数 名 称** | **功 能 描 述** |
| ---- | ---- |
| readOnly | 该属性用于设置当前事务是否为只读事务，设置为true表示只读，false则表示可读写，默认值为false。例如：`@Transactional(readOnly=true)` 注意是一次执行多次查询来统计某些信息，这时为了保证数据整体的一致性，要用只读事务 |
| rollbackFor | rollbackFor 该属性用于设置需要进行回滚的异常类数组，当方法中抛出指定异常数组中的异常时，则进行事务回滚。例如：指定单一异常类：@Transactional(rollbackFor=RuntimeException.class)指定多个异常类：@Transactional(rollbackFor={RuntimeException.class,Exception.class}) |
| **propagation** | 该属性用于设置事务的传播行为。例如：`@Transactional(propagation=Propagation.NOT_SUPPORTED, readOnly=true)` |
| **isolation** | 该属性用于设置底层数据库的事务隔离级别，事务隔离级别用于处理多事务并发的情况，通常使用数据库的默认隔离级别即可，基本不需要进行设置 |
| timeout | 该属性用于设置事务的超时秒数，默认值为-1表示永不超时 事物超时设置：`@Transactional(timeout=30)` ，设置为30秒 |

1、propagation：事务传播行为，指的就是当一个事务方法被另一个事务方法调用时，这个事务与事务应该如何运行

|**事务传播行为类型**|**说明**|
|---|---|
|PROPAGATION_SUPPORTS|支持事务。若当前没有事务以非事务方式执行；若当前有事务，加入此事务中|
|PROPAGATION_NOT_SUPPORTED|不支持事务。若当前存在事务，把当前事务挂起，然后运行方法|
|PROPAGATION_NEVER|不使用事务。若当前方法存在事务，则抛出IllegalTransactionStateException异常，否则继续使用无事务机制运行|
|PROPAGATION_MANDATORY|强制使用事务。若当前有事务，就使用当前事务；若当前没有事务，抛出IllegalTransactionStateException异常|
|**PROPAGATION_REQUIRED**|需要事务（**默认**）。若当前无事务，新建一个事务；若当前有事务，加入此事务中|
|**PROPAGATION_REQUIRES_NEW**|新建事务。无论当前是否有事务，都新建事务运行|
|**PROPAGATION_NESTED**|嵌套。如果当前存在事务，则在嵌套事务内执行；如果当前没有事务，则执行与PROPAGATION_REQUIRED类似的操作|


2、isolation ：事务隔离级别，默认的隔离级别，使用数据库默认的事务隔离级别，下面四个与JDBC的隔离级别相对应

| **级别** | **名字** | **隔离级别** | **脏读** | **不可重复读** | **幻读** | **数据库默认隔离级别** | 解释 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 读未提交 | read uncommitted | 是 | 是 | 是 |  | 事务A可以读取到事务B未提交的数据 |
| 2 | 读已提交 | read committed | 否 | 是 | 是 | Oracle和SQL Server | 事务A只能读取其它事务已提交的数据（避免了脏读） |
| 3 | 可重复读 | repeatable read | 否 | 否 | 是 | MySQL | 保证在同一个事务中多次读取同样数据的结果是一样的 |
| 4 | 串行化 | serializable | 否 | 否 | 否 |  | 事务串行化顺序执行 |


## 4.10、@Transactional失效场景

1.@Transactional 注解应该只被应用在 public 修饰的方法上(注意)。 如果在 protected、private 或者 package-visible 的方法上使用 该注解，它也不会报错(IDEA会有提示)， 但事务并没有生效

>Spring事务的实现都是依靠AOP，本质上也是依靠代理来实现。事务在spring中的实现其实就是生成bean对象的代理对象。在bean进行创建出实例时， 如果是有事务注解的方法，就会被进行增强，最终形成代理类。
>在spring中，有两种动态代理的方式，一种是jdk，它是将原始对象放入代理对象内部，通过调用内含的原始对象来实现原始的业务逻辑，而另一种是cglib，它是通过生成原始对象的子类，子类复写父类的方法，从而实现对父类的增强。jdk中，如果是private的方法，显然是无法访问的，而在cglib中，也是同样。所以总结来说private方法不能被继承，final方法不能被重写，static方法和继承不相干，所以它们3个的事务不起作用。Spring选择让protected方法和package方法不支持事务，所以只有public方法支持事务



2.@Transactional是基于动态代理的(注意)，需要一个类调用另一个类，类内调用会失效

> 当在类的内部调用被@Transactional注解修饰的方法时，实际上是通过类的实例直接调用方法，而不是通过代理对象。这样做会绕过代理对象，从而导致@Transactional注解失效

3.事务@Transactional由spring控制时，它会在抛出异常的时候进行回滚。如果自己使用try-catch捕获处理了，是不生效的

## 4.11、声明式事务和编程式事务

Spring 事务管理分为**编码式和声明式**的两种方式

编程式事务管理： 利用TransactionTemplate模板通过编程的方式实现事务管理,而无需关注资源获取、复用、释放、事务同步及异常处理等操作

声明式事务管理： 建立在AOP之上的。其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务（使用**isolation**属性声明事务的隔离级别,使用**propagation**属性声明事务的传播机制）

> 声明式事务管理不需要入侵代码，更快捷而且简单，推荐使用

## 4.12、pageHelper原理

PageHelper是MyBatis的一个插件，内部实现了一个PageInterceptor拦截器。Mybatis会加载这个拦截器到拦截器链中。在我们使用过程中先使用PageHelper.startPage这样的语句在当前线程上下文中设置一个ThreadLocal变量，再利用PageInterceptor这个分页拦截器拦截，从ThreadLocal中拿到分页的信息，如果有分页信息拼装分页SQL（limit语句等）进行分页查询，最后再把ThreadLocal中的东西清除掉。


# 5、java并发编程 

## 5.1、死锁的必要条件

1. 互斥条件：一个资源一次只能被一个进程使用
2. 请求与保持条件：一个进程因请求资源而阻塞时，对已获得资源保持不放
3. 不剥夺条件：进程获得的资源，在未完全使用完之前，不能强行剥夺
4. 循环等待条件：若干进程之间形成一种头尾相接的环形等待资源关系
## 5.2、死锁的实现

```java
/**
 * 实现一个死锁
 * 如果把lock(target, owner);放到上面则不会死锁
 */
public class DeadLock {
    public static void main(String[] args) throws InterruptedException {
        final Object owner = new Object();
        final Object target = new Object();
        //开启一个新线程
        new Thread(() -> {
            try {
                lock(owner, target);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        //主线程
        lock(target, owner);
    }
    public static void lock(Object owner, Object target) throws InterruptedException {
        synchronized (owner) {
            Thread.sleep(1000);
            synchronized (target) {
                System.out.println("success");
            }
        }
    }
}
```

## 5.3、什么是阻塞队列？实现原理？

阻塞队列（BlockingQueue）是一个支持两个附加操作的队列。这两个附加的操作是：在队列为空时，获取元素的线程会阻塞等待队列变为非空。当队列满时，存储元素的线程会阻塞等待队列可用。
阻塞队列常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程。阻塞队列就是生产者存放元素的容器，而消费者也只从容器里拿元素

主要的两个实现ArrayBlockingQueue 和 LinkedBlockingQueue 

1. 区别

（1）内部实现：ArrayBlockingQueue 使用数组；LinkedBlockingQueue 使用单链表
（2）锁的个数：ArrayBlockingQueue只有一把锁（最多只允许一个线程，生产者或消费者二选一）；	LinkedBlockingQueue 有两把锁：takeLock、putLock（可以允许两个线程同时执行，一个生产者，一个消费者）
（3）支持公平锁：ArrayBlockingQueue 支持；LinkedBlockingQueue 不支持，因为有两把锁，没法实现

## 5.4、java线程状态

| 状态   | 说明 |
| --- | --- |
| NEW | 初始状态:线程被创建，但还没有调用start()方法 |
| RUNNABLE | 运行状态:Java线程将操作系统中的就绪和运行两种状态笼统的称作"运行" |
| BLOCKED | 阻塞状态:表示线程阻塞于锁 |
| WAITING | 等待状态:表示线程进入等待状态，进入该状态表示当前线程需要等待其他线程做出一些特定动作(通知或中断) |
| TIMEWAITING | 超时等待状态:该状态不同于WAITIND，它是可以在指定的时间自行返回的 |
| TERMINATED | 终止状态:表示当前线程已经执行完毕 |

![线程状态](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202307230930532.jpg)


## 5.5、线程创建方式

1、继承Thread类

```java
// 创建线程对象
Thread t = new Thread() {
    public void run() {
        // 要执行的任务
    }
};
// 启动线程
t.start();
```

2、实现 Runnable接口

```java
Runnable runnable = new Runnable() {
    public void run(){
        // 要执行的任务
    }
};
// 创建线程对象
Thread t = new Thread( runnable );
// 启动线程
t.start();
```

java8可用lambda精简

```java
// 创建任务对象
Runnable task2 = () -> log.debug("hello");

// 参数1 是任务对象; 参数2 是线程名字，推荐
Thread t2 = new Thread(task2, "t2");
t2.start();
```

3、FutureTask 配合 Thread 

FutureTask 能够接收 Callable 类型的参数，用来处理有返回结果的情况
通过FutureTask的get方法拿到线程的返回值。

```java
// 创建任务对象
FutureTask<Integer> task3 = new FutureTask<>(() -> {
    log.debug("hello");
    return 100;
});

// 参数1 是任务对象; 参数2 是线程名字，推荐
new Thread(task3, "t3").start();

// 主线程阻塞，同步等待 task 执行完毕的结果
Integer result = task3.get();
log.debug("结果是:{}", result);
```


## 5.6、如何停止一个正在运行的线程

1、使用退出标志退出线程
2、使用interrupt方法中断线程

使用interrupt()方法来中断线程是分两种情况的：

- 线程处于阻塞状态：如使用了sleep()，同步锁的wait()，socket中的receiver()，accept()等方法时，会使线程处于阻塞状态。当调用线程interrupt()方法时，会抛出InterruptException异常。阻塞中的那个方法抛出此异常，通过代码可以捕获此异常，然后跳出循环状态，从而让我们有机会结束这个线程的执行。并不是只要调用interrupt()方法，线程就会结束，实际上是不正确的，一定要先捕获InterruptException异常之后通过break来跳出循环，才能正常结束run()方法。

- 线程未处于阻塞状态：使用isInterrupted()判断线程的中断标志来退出循环。当使用interrupt()方法时，中断标志就会为true，和使用自定义的标志来控制循环是一样的道理。

```java
public class ThreadTest extends Thread {
	public void run() {
		while(!isInterrupted()) {
			try {
				Thread.sleep(5*1000);
			} catch(InterruptedException e) {
				e.printStackTrace();
				break;//捕获到异常之后，执行break跳出循环
			}
		}
	}
}
```

## 5.7、乐观锁和悲观锁

悲观锁：在每次去拿数据的时候，都会认为数据会被别人改变，线程只有占有了锁，才能去操作共享变量，每次只有一个线程占锁成功，获取锁失败的线程，都得停下来等待。java中Synchronized和ReentrantLock都是悲观锁，在数据库中也经常用到这种锁机制，如行锁，表锁，读写锁等

乐观锁：认为数据一般不会产生冲突，其核心思想是无需加锁，每次只有一个线程能成功修改共享变量，其它失败的线程不需要停止，不断重试直至成功。代表 Atomic原子类

## 5.8、concurrentHashmap原理

**JDK1.8前**
ConcurrentHashMap使用分段锁技术，数据结构：ReentrantLock+segement+hashEntry。一个segement中包含一个hashentry数组（hashentry结构类似hashmap：数组+链表）。

元素查询：使用二次hash，第一次定位到segement，第二次hash定位到元素所在链表的头部

锁：segement继承了reentrantLock，锁定操作的segement，其它segement不受影响，并发度为segement个数


> ConcurrentHashMap 与HashMap和Hashtable 最大的不同在于：put和 get 两次Hash到达指定的HashEntry，第一次hash到达Segment,第二次到达Segment里面的Entry,然后在遍历entry链表


**JDK1.8**

在JDK8中，ConcurrentHashMap的底层数据结构与HashMap一样，也是采用“数组+链表+红黑树”的形式。同时，它又采用锁定头节点的方式降低了锁粒度，以较低的性能代价实现了线程安全

1. 初始化数组或头节点时，ConcurrentHashMap并没有加锁，而是CAS的方式进行原子替换（原子操作，基于Unsafe类的原子操作API）。 
2. 插入数据时会进行加锁处理，但锁定的不是整个数组，而是槽中的头节点。所以，ConcurrentHashMap中锁的粒度是槽，而不是整个数组，并发的性能很好。 
3. 扩容时会进行加锁处理，锁定的仍然是头节点。并且，支持多个线程同时对数组扩容，提高并发能力。每个线程需先以CAS操作抢任务，争抢一段连续槽位的数据转移权。抢到任务后，该线程会锁定槽内的头节点，然后将链表或树中的数据迁移到新的数组里。 
4. 查找数据时并不会加锁，所以性能很好。另外，在扩容的过程中，依然可以支持查找操作。如果某个槽还未进行迁移，则直接可以从旧数组里找到数据。如果某个槽已经迁移完毕，但是整个扩容还没结束，则扩容线程会创建一个转发节点，在这个节点里面记录的是新的 ConcurrentHashMap 的引用，从新数组中找到目标数据。

## 5.9、CopyOnWriteArrayList原理

1. 首先CopyOnWriteArrayList的内部也是通过数组来实现的，在向CopyOnWriteArrayList添加元素时，会复制一个新的数组，写操作在新的数组上进行，读操作在原数组上进行
2. 写数据时会加ReentLocak锁，防止并发写入丢失数据的问题
3. 写操作结束后会把原数组指向新数组
4. CopyOnWriteArrayList允许在写操作时来读取数据，大大提高了读的性能，因此适合读多写少的场景。但是CopyOnWriteArrayList比较占用内存，同时可能读到的数据不是实时最新的数据，所以不适合实时性要求很高的场景


## 5.10、volatile保证可见性和有序性

1. **保证可见性（缓存一致性协议MESI）**

首先cpu会根据共享变量是否带有Volatile字段，来决定是否使用**MESI协议**保证缓存一致性。
如果有Volatile，汇编层面会对变量加上Lock前缀，当一个线程修改变量的值后，会马上经过store、write等原子操作修改主内存的值（如果不加Lock前缀不会马上同步），为什么监听到修改会马上同步呢？cpu**总线嗅探机制**监听到这个变量被修改，就会把其他线程的变量副本由共享S置为无效I，当其他线程在使用变量副本时，发现其已经无效，就回去主内存中拿一个最新的值

> **M 修改** (Modified) 代表该缓存行中的内容被修改了，并且该缓存行只被缓存在该CPU中 
**E 独享、互斥** (Exclusive) E代表该缓存行对应内存中的内容只被该CPU缓存.该缓存可以在任何其他CPU读取该缓存对应内存中的内容时变成S状态。或者本地处理器写该缓存就会变成M状态。
**S 共享** (Shared) 当多个线程都拿到了共享变量，此时为共享状态.当有一个CPU修改该缓存行对应的内存的内容时会使该缓存行变成 I 状态
**I 无效** (Invalid) 线程丢弃了自己工作内存中的变量，为无效状态


> 涉及到的指令
lock(锁定)：将一个变量标识为被一个线程独占状态
store(存储)：作用于工作内存的变量,将变量传输到主内存中
write(写入)：将store入主内存的变量,放入到主内存的变量中


2. **保证有序性（禁止指令重排优化）**

多线程环境下，有序性问题产生的主要原因就是执行重排优化，而Volatile的另一个作用就是禁止指令重排优化。具体是通过对Volatile修饰的变量增加内存屏障来完成的。内存屏障的主要工作原理为：通过在指令间插入一条内存屏障并禁止cpu对Volatile修饰的变量进行重排序


## 5.11、CAS原理和问题

CAS是所有原子类的底层原理，乐观锁主要采用CAS算法。
CAS，比较并交换，是JDK提供的非阻塞原子性操作，CAS的思想很简单：三个参数，一个当前内存位置V、旧的预期值A、即将更新的值B，当且仅当预期值A和内存位置的值相同时，将内存值修改为B并返回true，否则什么都不做，并返回false。

> 1. CAS利用了处理器的CMPXCHG指令，该指令操作的内存区域就会加锁，导致其他处理器不能同时访问它，保证原子性
> 2. CAS 必须借助 volatile 才能读取到共享变量的最新值来实现【比较并交换】的效果


产生的问题？
1.ABA问题
多线程环境，线程1读取了数据A，然后线程2将数据A变为B再变为A，线程1进行cas操作时就会认为数据没有被修改过，其实数据是被修改过的。

**解决方案：**
使用AtomicStampReference 
AtomicStampReference在cas的基础上增加了一个标记stamp，使用这个标记可以用来觉察数据是否发生变化，给数据带上了一种实效性的检验

2.开销问题
CAS自旋操作，会不断的轮询内存位置，直到成功。消耗大量CPU资源

## 5.12、wait（）和sleep（）的区别

1. 所属的类型不同 
- wait()是Object类的实例方法,调用该方法的线程将进入WTING状态。 
- sleep()是Thread类的静态方法,调用该方法的线程将进入TIMED_WTING状态

2. 对锁的依赖不同 
- wait()依赖于synchronized锁,通过监视器进行调用,调用后线程会释放锁。
- sleep()不依赖于任何锁,所以在调用后它也不会释放锁。

3. 返回的条件不同 
- 调用wait()进入等待状态的线程,需要由notify()/notifyAll()唤醒,从而返回。 
- 调用sleep()进入超时等待的线程,需要在超时时间到达后自动返回。

## 5.13、ThreadLocal变量原理

每个Thread 维护一个 ThreadLocalMap 映射表，这个映射表的 key 是 ThreadLocal实例本身，value 是真正需要存储的 Object。
也就是说 ThreadLocal 本身并不存储值，它只是作为一个 key 来让线程从 ThreadLocalMap 获取 value。值得注意的是图中的虚线，表示 ThreadLocalMap 是使用 ThreadLocal 的弱引用作为 Key 的，弱引用的对象在 GC 时会被回收

## 5.14、ThreadLocal内存泄漏

![](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202401041020818.png)

由于ThreadLocalMap的key是弱引用，而Value是强引用。这就导致了一个问题，ThreadLocal在没有外部对象强引用时，发生GC时弱引用Key会被回收，而Value不会回收。
当线程没有结束，但是ThreadLocal已经被回收，则可能导致线程中存在`ThreadLocalMap<null, Object>`的键值对，造成内存泄露。（ThreadLocal被回收，ThreadLocal关联的线程共享变量还存在）

如果在线程池中使⽤ThreadLocal也会造成内存泄漏，因为当ThreadLocal对象使⽤完之后，应该要把设置的key，value，也就是Entry对象进⾏回收，但线程池中的线程不会回收，⽽线程对象是通过强引⽤指向ThreadLocalMap，ThreadLocalMap也是通过强引⽤指向Entry对象，线程不被回收，Entry对象也就不会被回收，从⽽出现内存泄漏，解决办法是，在使⽤了ThreadLocal对象之后，⼿动调⽤ThreadLocal的remove⽅法，⼿动清楚Entry对象


4. **怎么避免内存泄露？**

1.Threadlocal自身做了一些处理，在每次调用ThreadLocal的get、set、remove方法时都会执行一个方法，该方法检测整个Entry[]表中对key为null的Entry一并擦除，重新调整索引
2.程序员自身，在代码逻辑中使用完ThreadLocal，都要调用remove方法，及时清理

## 5.15、ThreadLocal为什么使用弱引用

引用的ThreadLocal的对象被回收了，但是ThreadLocalMap还持有ThreadLocal的强引用，如果没有手动删除，ThreadLocal不会被回收，导致Entry内存泄漏。如果是使用弱引用，此时引用数为0，会被回收，这时就会出现key为null的entry，对应的value在下一次ThreadLocalMap调用set,get,remove的时候会被清除，避免了entry中value的内存泄漏

## 5.16、线程池参数和原理

**构造方法**

通过new ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue workQueue,ThreadFactory threadFactory,RejectedExecutionHandler handler)自定义创建

> 1. corePoolSize：核心线程池的大小，如果核心线程池有空闲位置，新的任务就会被核心线程池新建一个线程执行，执行完毕后不会销毁线程，线程会进入缓存队列等待再次被运行。
> 2. maximunPoolSize：线程池能创建最大的线程数量。如果核心线程池和缓存队列都已经满了，新的任务进来就会创建救急线程来执行。但是数量不能超过maximunPoolSize，否侧会采取拒绝接受任务策略，我们下面会具体分析。
> 3. keepAliveTime：救急线程线程能够空闲的最长时间，超过时间，线程终止。这个参数默认只有在线程数量超过核心线程池大小时才会起作用。只要线程数量不超过核心线程大小，就不会起作用。
> 4. unit：空闲线程存活时间单位 创建一个新线程时使用的工厂，可以用来设定线程名、是否为daemon线程等等
> 5. workQueue：缓存队列，用来存放等待被执行的任务。
> 6. threadFactory 线程工厂
> 7. handler：拒绝策略
（1）abortPolicy：抛出异常（默认）
（2）discardPolicy：放弃本次任务
（3）discardoldestPolicy：放弃队列中最早的任务，本任务取代
（4）callerrunPolicy：让调用者运行任务


**工作原理**

如果当前线程池中正在执行的线程数目小于corePoolSize，则每来一个任务，就会创建一个线程去执行这个任务；
如果当前线程池中正在执行任务的的线程数目>=corePoolSize，则每来一个任务，会尝试将其添加到任务缓存队列当中，若添加成功，则该任务会等待空闲线程将其取出去执行；若添加失败（一般来说是任务缓存队列已满），则会尝试创建新的线程(救急线程)去执行这个任务；
如果线程池中的线程数量大于 corePoolSize时，如果某线程空闲时间超过keepAliveTime，线程将被终止，直至线程池中的线程数目不大于corePoolSize；
如果当前线程池中的线程数目达到maximumPoolSize，则会采取任务拒绝策略进行处理
## 5.17、线程池类型

根据上面的ThreadPoolExecutor这个构造方法，JDK Executors类中提供了众多工厂方法来创建各种用途的线程池

1. **newFixedThreadPool**

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}
```

> 特点：
> - 核心线程数 == 最大线程数（没有救急线程被创建），因此也无需超时时间 
> - 阻塞队列是无界的，可以放任意数量的任务 
> 
评价：
> 适用于任务量已知，相对耗时的任务


2. **newCachedThreadPool**

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```

> 特点 
> - 核心线程数是 0，最大线程数是 Integer.MAX_VALUE，救急线程的空闲生存时间是 60s，意味着 
>    - 全部都是救急线程（60s 后可以回收）
>    - 救急线程可以无限创建 
> - 队列采用了 SynchronousQueue 实现特点是，它没有容量，没有线程来取是放不进去的（一手交钱、一手交货）
> 
评价：
> 整个线程池表现为线程数会根据任务量不断增长，没有上限，当任务执行完，空闲 1分钟后释放线程
> 适合任务数比较密集，但每个任务执行时间较短的情况


3. newSingleThreadExecutor

```java
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}
```
使用场景： 
希望多个任务排队执行。线程数固定为 1，任务数多于 1 时，会放入无界队列排队。
任务执行完毕，这唯一的线程也不会被释放。 

## 5.18、线程池状态

1. RUNNING状态：线程池创建后，初始状态为RUNNING。
2. SHUTDOWN状态：当调用线程池的shutdown()方法时，线程池进入SHUTDOWN状态。此时线程池不再接受新的任务，但会执行已经提交的任务。当所有任务都执行完毕后，线程池会转换到TIDYING状态。
3. STOP状态：当调用线程池的shutdownNow()方法时，线程池进入STOP状态。此时线程池不再接受新的任务，并且会中断正在执行的任务。当所有任务都执行完毕后，线程池会转换到TIDYING状态。
4. TIDYING状态：当线程池处于SHUTDOWN或STOP状态时，所有任务都执行完毕后，线程池会进入TIDYING状态。在此状态下，线程池会进行清理工作，如关闭线程池中的所有线程等。当清理工作完成后，线程池会转换到TERMINATED状态。
5. TERMINATED状态：线程池处于TERMINATED状态时，表示线程池已经完全终止，所有任务已经执行完毕并且清理工作也已经完成。此时线程池不再能接受新的任务。

## 5.19、使用线程池

jdk提供了ThreadPoolExecutor，spring提供了 ThreadPoolTaskExecutor

1、配置线程池
```java
@Configuration  
public class ThreadPoolConfig  
{  
    // 核心线程池大小  
    private int corePoolSize = 50;  
    // 最大可创建的线程数  
    private int maxPoolSize = 200;  
    // 队列最大长度  
    private int queueCapacity = 1000;  
    // 线程池维护线程所允许的空闲时间  
    private int keepAliveSeconds = 300;  
  
    @Bean(name = "threadPoolTaskExecutor")  
    public ThreadPoolTaskExecutor threadPoolTaskExecutor()  
    {  
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();  
        executor.setMaxPoolSize(maxPoolSize);  
        executor.setCorePoolSize(corePoolSize);  
        executor.setQueueCapacity(queueCapacity);  
        executor.setKeepAliveSeconds(keepAliveSeconds);  
        // 线程池对拒绝任务(无线程可用)的处理策略  
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());  
        return executor;  
    }  
}
```


2、在实现方法上加@Async注解异步调用。或使用submit，execute手动提交任务

```java
@Slf4j
@Service
public class AsyncService {
	public static Object object = new Object();
	@Async("taskExecutor")
	public String test(String message){
		synchronized (object){
			log.info("message={}",message);
		}try {
			Thread.sleep(1000);
		} catch (InterruptedException e) {
			e.printStackTrace();
			log.error("error");
		}
		return message;
	}
}

@RestController
@Slf4j
public class AsyncController {
	@Autowiredprivate AsyncService asyncService;
	@GetMapping("/test")public String test(){
		long s = System.currentTimeMillis();
		int count = 500;
		for (int i=0;i<=count;i++){
			asyncService.test("index="+i);
		}
		long e = System.currentTimeMillis();
		log.info("haoshi={}",(e-s)/1000+"秒");return "s";
	}
}
```

```java
@Autowired  
private ThreadPoolTaskExecutor executor;

executor.submit(() -> {  
    try {  
        Thread.sleep(1000);  
    } catch (InterruptedException e) {  
        e.printStackTrace();  
    }   
    log.info("Offline，{}，{}处理完成，发送over", cloudToLinkInfoOverDO.getId(), cloudToLinkInfoOverDO.getAck());  
});
```


## 5.20、线程池execute和submit的区别

提交任务的类型：
execute和submit都属于线程池的方法，execute只能提交Runnable类型的任务
submit既能提交Runnable类型任务也能提交Callable类型任务。

异常：
execute会直接抛出任务执行时的异常，可以用try、catch来捕获，和普通线程的处理方式完全一致
submit会吃掉异常，可通过Future的get方法将任务执行时的异常重新抛出。


## 5.21、ReentrantReadWriteLock原理

主要针对读多写少的场景，内部维护一个读锁和写锁。读写互斥，读读共享

1、原理
ReentrantReadWriteLock还是基于AQS实现的，还是对state进行操作，拿到锁资源就去干活，如果没有拿到，依然去AQS队列中排队。将 state 的 高 16 位和低 16 位拆开表示读写锁。其中高 16 位表示读锁，低 16 位表示写锁。读锁，允许共享；写锁，是独占锁。适合在读多写少的场景中使用

2、重入性
ReentrantReadWriteLock依然是可重入的，写锁的重入和ReentrantLock没什么区别，依然是对state进行+1操作。但是读锁是共享锁，对高16位进行加1，同一时间有多条读线程持有资源，无法确定每个线程读锁重入次数，每个读操作线程，都有一个ThreadLocal记录锁重入次数

3、避免写锁饥饿问题
当有线程持有读锁资源，再来一个读线程，依然可以获取到读锁。这样会导致队列中写操作线程一直无法获取到锁。所以实现了当有线程持有读锁资源时，并且队列中有写锁线程排队，那么新来的读线程也需要到队列中去等待

## 5.22、AQS原理

1. 基本概念

AbstractQueuedSynchronizer抽象的队列式同步器。是除了java自带的synchronized关键字之外的锁机制。这个类在java.util.concurrent.locks包。AQS定义了一套多线程访问共享资源的同步器框架，许多同步类实现都依赖于它，如常用的ReentrantLock/Semaphore/CountDownLatch...

2. 原理 

它维护了一个volatile int state（代表共享资源）和一个FIFO双向队列（CLH队列，多线程争用资源被阻塞时会进入此队列），核心思想是，如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，如果被请求的共享资源被占用，那么就将暂时获取不到锁的线程加入到等待队列（entrylist）中。
AQS定义两种资源共享方式：Exclusive（独占，只有一个线程能执行，如ReentrantLock）和Share（共享，多个线程可同时执行，如Semaphore/CountDownLatch）
自定义同步器在实现时只需要实现共享资源state的获取与释放方式即可，至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS已经在顶层实现好了

3. 实现

自定义同步器在实现时只需要实现共享资源state的获取与释放方式即可，至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS已经在顶层实现好了。自定义同步器实现时主要实现以下几种方法：

>**isHeldExclusively()**：该线程是否正在独占资源。只有用到condition才需要去实现它。
**tryAcquire(int)**：独占方式。尝试获取资源，成功则返回true，失败则返回false。
**tryRelease(int)**：独占方式。尝试释放资源，成功则返回true，失败则返回false。
**tryAcquireShared(int)**：共享方式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。
**tryReleaseShared(int)**：共享方式。尝试释放资源，如果释放后允许唤醒后续等待结点返回true，否则返回false。


以ReentrantLock为例，state初始化为0，表示未锁定状态。A线程lock()时，会调用tryAcquire()独占该锁并将state+1。此后，其他线程再tryAcquire()时就会失败，直到A线程unlock()到state=0（即释放锁）为止，其它线程才有机会获取该锁。当然，释放锁之前，A线程自己是可以重复获取此锁的（state会累加），这就是可重入的概念。但要注意，获取多少次就要释放多么次，这样才能保证state是能回到零态的

## 5.23、semaphore原理

Semaphore（信号量）是用来控制同时访问特定资源的线程数量，它通过协调各个线程，以保证合理的使用公共资源（Semaphore可以用于做流量控制，特别是公用资源有限的应用场景，比如数据库连接）

Semaphore的实现原理主要是通过内部类Sync来实现的，内部类Sync是AQS的子类，主要是通过重写AQS的共享式获取和释放同步状态方法来实现的

1、把初始令牌数量赋值给同步队列的state状态，state的值就代表当前所剩余的令牌数量。
2、 semaphore.acquire(); 当前线程会尝试去同步队列获取一个令牌，获取令牌的过程也就是使用原子的操作去修改同步队列的state ,获取一个令牌则修改为state=state-1。state<0,令牌数量不足，加入阻塞队列。>=0则获取成功
3、semaphore.release() ，释放令牌的过程也就是把同步状态的state修改为state=state+1的过程。释放令牌成功之后，同时会唤醒同步队列的所有阻塞节共享节点线程

## 5.24、countDownLatch原理

CountDownLatch允许一个或多个线程等待其他线程完成操作。await（）线程进入阻塞队列。用来等待计数归0，countDown（）用来让计数减少一。

1、初始化CountDownLatch实际就是设置了AQS的state为计数的值
2、调用CountDownLatch的countDown方法时实际就是调用AQS的relase方法，每调用一次就自减一次state值
3、调用await方法实际就调用AQS的共享式获取同步状态state，当AQS的state值为0时，await方法才会执行成功，否则就会一直处于死循环中不断重试

## 5.25、CyclicBarrier原理

CyclicBarrier可以理解为一个循环同步屏障，定义一个同步屏障之后，当一组线程都全部达到同步屏障之前都会被阻塞，直到最后一个线程达到了同步屏障之后才会被打开，其他线程才可继续执行（王者荣耀10人）

1、创建CyclicBarrier时定义了CyclicBarrier对象需要达到的线程数count
2、每当一个线程执行了await方法时，需要先通过ReentrantLock进行加锁操作，然后对count进行自减操作，操作成功则判断当前count是否为0；
3、如果当前count不为0则调用Condition的await方法使当前线程进入等待状态；
4、如果当前count为0则表示同步屏障已经完全，此时调用Condition的signalAll方法唤醒之前所有等待的线程，并开启循环的下一次同步屏障功能；
5、唤醒其他线程之后，其他线程继续执行剩余的逻辑。


## 5.26、synchronized原理

synchronized可以修饰静态方法、普通方法、代码块。 能够保证同一个时刻只有一个线程执行该段代码，保证线程安全。 在执行完或者出现异常时自动释放锁。

synchronized关键字的底层原理是Java中的锁机制。在Java中，每个对象都有一个监视器锁（monitor）。当一个线程想要访问一个被synchronized修饰的方法或代码块时，它会尝试获取这个对象的监视器锁。如果这个锁没有被其他线程占用，那么这个线程就可以获取这个锁，并执行synchronized修饰的方法或代码块。如果这个锁已经被其他线程占用，那么这个线程就会进入阻塞状态（waitset，entrylist），直到它能够获取这个锁为止
  
## 5.27、synchronized锁升级 
Java中每个对象都拥有对象头，对象头由Mark World 、指向类的指针、以及数组长度三部分组成，本文，我们只需要关心Mark World 即可，Mark World 记录了对象的HashCode、分代年龄和锁标志位信息

| 锁状态 | 存储内容 | 锁标记 |
| ---- | ---- | ---- |
| 无锁 | 对象的hashCode、对象分代年龄、是否是偏向锁（0） | 01 |
| 偏向锁 | 偏向线程ID、偏向时间戳、对象分代年龄、是否是偏向锁（1） | 01 |
| 轻量级锁 | 指向栈中锁记录的指针 | 00 |
| 重量级锁 | 指向互斥量（重量级锁）的指针 | 10 |


偏向锁：线程执行同步代码或方法前，线程只需要判断对象头的Mark Word中线程ID与当前线程ID是否一致，如果一致直接执行同步代码或方法

轻量级锁：当前线程持有的锁是偏向锁的时候，被另外的线程所访问，偏向锁就会升级为轻量级锁（适用于两个或以上的线程交替获取锁）。当线程尝试获取这把锁的时候，会创建锁记录（Lock Record），包含一个锁记录的结构可以用来存储对象的Mark Word。尝试用 CAS 替换 Object 的 Mark Word，将 Mark Word 的值存入锁记录，并将锁记录的地址存入Object的Mark Word 。如果CAS成功，对象的Mark Word将会存储Lock Record 地址 和 锁状态 00。如果失败尝试自适应自旋（自适应自旋锁，次数并非一成不变。根据获取锁的成功率来决定是否能有更长的等待时间）

重量级锁：多个线程并发在同一个对象同步时，为了避免无用的自旋消耗cpu，轻量级锁会升级为重量级锁。重量级锁原理参考2.27

## 5.28、使用多线程时，创建多少个线程合适

1、cpu密集型程序：一个完整的请求，I/O可能很短时间完成，但是CPU还有很多运算处理
（1）单核情况下：多个线程实际处理的时间和一个线程是差不多的，加上上下文切换的时间，时间可能更长
（2）多核情况下：多个线程并行处理，时间可能更短

2、I/O密集型程序：与CPU密集型程序相对，一个完整请求，CPU运算操作完成之后还有很多I/O操作要做， 也就是说I/O操作占比很大部分
因为I/O的时间长，只要能合理的利用I/O阻塞时间进行cpu运算，可以提升效率

总结：
cpu密集型：线程数 = cpu核心数
I/O密集型：线程数 = cpu核心数 * （1 + （I/O耗时/CPU耗时））

## 5.29、Future和CompletableFuture

Future是Java5新加的一个接口，它提供了一种异步并行计算的功能。如果主线程需要执行一个很耗时的计算任务，我们就可以通过future把这个任务放到异步线程中执行。主线程继续处理其他任务，处理完成后，再通过Future获取计算结果

```java
public class FutureTest {

    public static void main(String[] args) throws ExecutionException, InterruptedException {

        ExecutorService executorService = Executors.newFixedThreadPool(10);

        UserInfoService userInfoService = new UserInfoService();
    
        long userId =666L;
        long startTime = System.currentTimeMillis();

        //调用用户服务获取用户基本信息
        FutureTask<UserInfo> userInfoFutureTask = new FutureTask<>(new Callable<UserInfo>() {
            @Override
            public UserInfo call() throws Exception {
                return userInfoService.getUserInfo(userId);
            }
        });
        executorService.submit(userInfoFutureTask);

        Thread.sleep(300); //模拟主线程其它操作耗时

        UserInfo userInfo = userInfoFutureTask.get();//获取个人信息结果

        System.out.println("总共用时" + (System.currentTimeMillis() - startTime) + "ms");
    }
}
```

但是Future对于结果的获取，不是很友好，只能通过阻塞或者轮询的方式得到任务的结果。Future.get() 就是阻塞调用，在线程获取结果之前get方法会一直阻塞

因此，JDK8设计出CompletableFuture。CompletableFuture，它针对`Future`做了改进，可以传入回调对象，当异步任务完成或者发生异常时，自动调用回调对象的回调方法
```java
public class FutureTest {

   public static void main(String[] args) throws Exception {
        // 创建异步执行任务:
        CompletableFuture<Double> cf = CompletableFuture.supplyAsync(Main::fetchPrice);
        // 如果执行成功:
        cf.thenAccept((result) -> {
            System.out.println("price: " + result);
        });
        // 如果执行异常:
        cf.exceptionally((e) -> {
            e.printStackTrace();
            return null;
        });
        // 主线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭:
        Thread.sleep(200);
    }
}

```

- supplyAsync执行CompletableFuture任务，支持返回值
- runAsync执行CompletableFuture任务，没有返回值
## 5.30、notify和join

wait()方法：是让当前线程进入等待状态，同时，wait()也会让当前线程释放它所持有的锁。“直到其他线程调用此对象的 notify() 方法或 notifyAll() 方法”，当前线程被唤醒

join()方法：把指定的线程加入到当前线程，可以将两个交替执行的线程合并为顺序执行。比如在线程B中调用了线程A的Join()方法，直到线程A执行完毕后，才会继续执行线程B

>join底层也是通过wait使线程B进入阻塞状态

## 5.31、强引用 软引用 弱引用 虚引用

1. 强引用，以new关键字创建的引用都是强引用，被强引用引用的对象永远都不会被回收。
2. 软引用：以SoftRererenc引用对象，被软引用引用的对象只有在内存空间不足时会被垃圾回收。

软引用主要用于实现缓存，当内存不足时，可以回收不常用的对象

3. 弱引用，以WeakReference引用对象，被弱引用引用的对象一定会被回收，它只能存活到下一次垃圾回收。（弱引用不会增加引用数量）

```java
WeakReference<Obejct> wf = new WeakReference<Obejct>(new Object);
```

4. 虚引用：以PhantomReference引用对象，一个对象被引用引用后不会有任何影响，也无法通过该引用来获取该对象，只是其在被垃圾回收时会收到一个系统通知（作用是用来跟踪它所引用的对象被垃圾收集器回收的活动）

Java4种引用的级别由高到低依次为：

强引用 > 软引用 > 弱引用 > 虚引用 

## 5.31、qps和tps

qps：每秒处理请求个数
tps：每秒处理请求个数

若在一秒内，用户请求了百度首页并看到了首页全貌，这样就完成了一个事务（TPS=1），但其实向服务端发起了N多次请求（QPS=N）

# 6、jvm

## 6.1、常用jvm启动参数

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


## 6.2、CPU飙升怎么排查

1.先通过top命令找到消耗cpu很高的进程id
2.top - H -p 2732 找到具体的线程id
3.执行jstack 2732对当前进程做dump，输出所有的线程信息。同时将第2步得到的线程id转为16进制，在堆栈信息里去找对应的线程内容
6.最后解读线程信息，定位具体代码位置


## 6.3、jvm如何调优（参数+工具）

jvm经过这么多年的发展和验证，大部分情况下不需要进行调优。通常我们jvm的参数配置大多还是遵循jvm的官方建议，例如：-xx:NewRatio=2（年轻代：老年代=1:2），-XX：SurvivorRatio=8（Eden:suvivor=8:1），堆内存设置为物理内存的3/4左右等

JVM 日常调优总结起来就是：首先通过 jps 命令查看当前进程，然后根据 pid 通过 jinfo 命令查看和修改 jvm 参数，通过 jstat 命令查看 class 的加载信息以及 GC 信息，通过 jstack 命令查看线程堆栈信息，通过 jmap 命令查看堆内存信息。用以上的命令查看可以解决一些问题，但是对于 GC 日志以及 dump 文件不便于分析，因此下面介绍几个常用的图形化界面工具

```text
jps
jinfo 26530 # jvm参数
jstat -class 26530 # 类加载数量和空间占用
jstat -gc 26530 # gc情况
jstack 26530 # 线程堆栈信息
jmap 26530 # 堆内存信息
jmap -dump:format=b,live,file=test.txt  26530 # dump日志
```

1、jconsole：查看 java 应用程序的运行概览、监控堆信息，可以监控Java应用程序的内存使用情况、线程状态、CPU占用率等
2、jvisualvm：jvisualvm 是一个免费的Java虚拟机监视、分析和调试工具，可以监控Java应用程序的内存使用情况、线程状态、CPU占用率，可以监控 java进程的 CPU、堆、类以及线程等情况

## 6.4、jvm内存模型

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

## 6.5、垃圾收集算法

**1、标记-清除:** 通过根节点，标记所有根节点开始的可达对象，清除未被标记对象。这种方法很简单，但是（1）产生内存碎片，造成新来的大对象（如数组）可能没有有效的内存空间（2）标记和清除的效率低

**2、标记-整理** 该算法主要是为了解决标记-清除，产生大量内存碎片的问题；将标记的对象移动到内存的一端，清除边界外的所有空间，这样就不会产生内存碎片了。

**3、复制算法**: 将内存分为一块较大的Eden和两块较小的survivor，每次使用Eden和其中一块survivo,gc时将Eden存活对象复制到suvivor1,suvivor2存活的对象没有达到分代年龄阈值时复制到suvivor1，达到分代年龄阈值复制到老年区，之后清除Eden和suvivor2的对象,交换两个suvivor的角色,后面以此类推

**4、分代收集** 现在的虚拟机垃圾收集大多采用这种方式，它根据对象的生存周期，将堆分为新生代和老年代。在新生代中，由于对象生存期短，每次回收都会有大量对象死去，那么这时就采用复制算法。老年代里的对象存活率较高，没有额外的空间进行分配担保，所以可以使用标记-整理 或者 标记-清除。

> 复制算法在对象存活率较高时，需要较多的复制操作，效率会降低，尤其是该算法会浪费掉一部分内存，所以不适合老年代


## 6.6、如何判断对象是否可以被回收

1、引用计数法

在对象里面添加一个引用计数器，每当有一个对象引用它时，计数器值就加一；每当引用失效，计数器的值就减一；任何时刻计数器为零的对象就是不可能再被使用的

> 缺点：可能会造成循环引用，导致无法回收，例如：上面对象objectA和objectB都有字段instance，objectA.instance = objectB，objectB.instance = objectA,除此之外，这两个对象再无其他任何引用，实际上这两个对象不可能被访问，但是他们互相引用对方，导致引用计数器不为0，就无法回收它们


2、可达性分析法

设立若干根对象（GC Root Object），当任何一个根对象到某一个对象均不可达时，认为这个对象可以被回收

> 哪些对象可以被作为根对象？
> - 虚拟机栈（Java Stack）中的局部变量和参数（它们是线程私有的，因此可以作为根对象）
> - 方法区中静态变量常量（生命周期与应用程序的生命周期相同）
>
为什么这些对象可以作为根对象？
> GC Root 需要确保引用所指的对象都是活着的,而当前线程栈帧中的对象和方法区中的对象，在这一时刻是存活的。

## 6.7、垃圾收集器

常见的垃圾收集器：

新生代（高吞吐量）：serial(复制),ParNew（复制）,Parallel Scavenge（复制）
老年代：Serial Old（标记整理）, CMS（标记清除）, Parallel Old（标记整理）
新生代和老年代（jdk9后）：G1,ZGC

一般组合：

Serial New + Serial Old
Parallel New + CMS
Parallel Scavenge +  Parallel Old


**1、Serial（串行）收集器**

Serial收集器是最基本、发展历史最悠久的收集器，曾经（在JDK1.3.1之前）是虚拟机新生代收集的唯一选择。它是一种单线程收集器，不仅仅意味着它只会使用一个CPU或者一条收集线程去完成垃圾收集工作，更重要的是其在进行垃圾收集的时候需要暂停其他线程

> 优点：简单高效，拥有很高的单线程收集效率
  缺点：收集过程需要暂停所有线程
  算法：复制算法应用

2. **收集过程**

![image (23).png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202310222242409.png)


**2、ParNew 收集器**

概念：可以把这个收集器理解为Serial收集器的多线程版本

> 优点：在多CPU时，比Serial效率高。
  缺点：收集过程暂停所有应用程序线程，单CPU时比Serial效率差。
  算法：复制算法


![image (24).png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202310222242011.png)


**3、Parallel Scavenge 收集器**

Parallel Scavenge收集器是一个新生代收集器，它也是使用复制算法的收集器，又是并行的多线程收集器，看上去和ParNew一样，但是Parallel Scanvenge更关注系统的吞吐量 。
可设置参数：-XX:MaxGCPauseMillis控制最大的垃圾收集停顿时间， -XX:GC Time Ratio直接设置吞吐量的大小

> 吞吐量 = 运行用户代码的时间 / (运行用户代码的时间 + 垃圾收集时间)
  比如虚拟机总共运行了120秒，垃圾收集时间用了1秒，吞吐量=(120-1)/120=99.167%。
  若吞吐量越大，意味着垃圾收集的时间越短，则用户代码可以充分利用CPU资源，尽快完成程序的运算任务。


**4、serial old**

Serial Old收集器是Serial收集器的老年代版本，也是一个单线程收集器，不同的是采用"标记-整理算法"，运行过程和Serial收集器一样。

**5、CMS**

 **特点**最短回收停顿时间，
 **回收算法**标记-清除
 **回收步骤：**
   （1）初始标记：标记GC Roots直接关联的对象，速度快
   （2）并发标记：GC Roots Tracing过程，耗时长，与用户进程并发工作
   （3）重新标记：修正并发标记期间用户进程运行而产生变化的标记，好事比初始标记长，但是远远小于并发标记
   （4）并发清除：清除标记的对象
**缺点**：

	对CPU资源非常敏感，CPU少于4个时，CMS岁用户程序的影响可能变得很大，由此虚拟机提供了“增量式并发收集器”；无法回收浮动垃圾；采用标记清除算法会产生内存碎片，不过可以通过参数开启内存碎片的合并整理。

![cms垃圾收集器](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202310222243378.png)

> 三色标记算法：黑（已扫描完） 灰（没有完全扫描）白（没有被引用，表示垃圾）


**6、Parallel old**

Parallel Old收集器是Parallel Scavenge收集器的老年代版本，使用多线程和"标记-整理算法"进行垃圾回收，吞吐量优先。
**回收算法**：标记-整理
**适用场景**：为了替代serial old与Parallel Scanvenge配合使用。


**7、G1**

G1将整个JVM堆划分成多个大小相等的独立区域regin，跟踪各个regin里面的垃圾堆积的价值大小，在后台维护一个优先列表，每次根据允许的收集时间，优先回收最大的regin，当然还保留有新生代和老年代的概念，但新生代和老年代不在是物理隔离了，他们都是一部分regin集合。内存“化整为零”的思路：在GC根节点的枚举范围汇总加入remembered set 即可保证不对全堆扫面也不会遗漏。

   （1）初始标记：标记GC Roots直接关联的对象
   （2）并发标记：对堆中对象进行可达性分析，找出存活对象，耗时长，与用户进程并发工作
   （3）重新标记：修正并发标记期间用户进程继续运行而产生变化的标记
   （4）筛选回收：对各个regin的回收价值进行排序，然后根据期望的GC停顿时间制定回收计划

![G1垃圾收集器](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202310222244637.png)


 
**8、ZGC**

ZGC（Z Garbage Collector）是一款由Oracle公司研发的，以低延迟为首要目标的一款垃圾收集器。在JDK 11新加入，还在实验阶段，主要特点是：回收TB级内存（最大4T），停顿时间不超过10ms。
**优点**：低停顿，高吞吐量，ZGC收集过程中额外耗费的内存小
**缺点**：浮动垃圾
目前使用的非常少，真正普及还是需要写时间的。

## 6.8、类加载过程


**1、加载阶段**
（1）通过一个类的全限定名来获取定义此类的二进制字节流（2）将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构。在内存中生成一个代表这个类的java.lang.Class对象，作为方法区这个类的各种数据的访问入口

**2、链接阶段**

（1）**验证**： 确保Class文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全

> **文件格式验证**：验证字节流是否符合Class文件格式的规范，如：是否以模数0xCAFEBABE开头、主次版本号是否在当前虚拟机处理范围内等等。
  **元数据验证**：对字节码描述的信息进行语义分析，以保证其描述的信息符合Java语言规范的要求；如：这个类是否有父类，是否实现了父类的抽象方法，是否重写了父类的final方法，是否继承了被final修饰的类等等。
  **符号引用验证**：确保解析动作能正确执行；如：通过符合引用能找到对应的类和方法，符号引用中类、属性、方法的访问性是否能被当前类访问等等
（验证阶段是非常重要的，但不是必须的。可以采用-Xverify:none参数来关闭大部分的类验证措施）


（2）**准备**：为类的静态变量分配内存，并将其赋默认值

> 为类变量分配内存并设置类变量初始值，这些内存都将在方法区中分配。对于该阶段有以下几点需要注意：
只对**static**修饰的静态变量进行内存分配、赋默认值（如0、0L、null、false等）。
对**final**的静态字面值常量直接赋初值（赋初值不是赋默认值，如果不是字面值静态常量，那么会和静态变量一样赋默认值）


（3）**解析**：将常量池中的符号引用替换为直接引用（内存地址）的过程

> 符号引用：就是一组符号来描述目标，可以是任何字面量。属于编译原理方面的概念如：包括类和接口的全限定名、字段的名称和描述符、方法的名称和描述符（eg:java.lang.String）。
  直接引用：就是直接指向目标的指针、相对偏移量或一个间接定位到目标的句柄。如指向方法区某个类的一个指针


**3、初始化阶段**

为类的静态变量赋初值

> 赋初值两种方式：
（1）定义静态变量时指定初始值。如 private static String x="123";
（2）在静态代码块里为静态变量赋值。如 static{ x="123"; }
 注意：只有对类的主动使用才会导致类的初始化。


**初始化顺序：**

初始化顺序：静态成员 - 父类构造器 - 非静态成员 - 子类构造器


## 6.9、双亲委派机制？怎么打破？


概念：
当某个类加载器需要加载某个.class文件时，它首先把这个任务委托给他的上级类加载器，递归这个操作，如果上级的类加载器没有加载，自己才会去加载这个类

好处：
（1）避免重复加载，通过委托去向上面问一问，加载过了，就不用再加载一遍
（2）保证核心api定义的类型不会被随意篡改，比如自己定义一个java.lang.String，顶级加载器系统类加载器加载时会加载核心包下的String类而不是自定义的。保证了核心类的安全

![双亲委派机制](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202311010834246.png)



自定义加载器的话，需要继承 `ClassLoader` 。如果我们不想打破双亲委派模型，就重写 `ClassLoader` 类中的 `findClass()` 方法即可，无法被父类加载器加载的类最终会通过这个方法被加载。但是，如果想打破双亲委派模型则需要重写 `loadClass()` 方法。为什么是重写 `loadClass()` 方法打破双亲委派模型呢？双亲委派模型的执行流程已经解释了：

>类加载器在进行类加载的时候，它首先不会自己去尝试加载这个类，而是把这个请求委派给父类加载器去完成（调用父加载器 `loadClass()`方法来加载类）。

# 7、数据库
## 2.1、数据库优化方案

[11.5、sql优化的方案有哪些？](coding笔记/mysql.md#11.5、sql优化的方案有哪些？)

1.表结构优化。对经常使用的查询条件加索引
2.查询语句优化。使用explain分析工具

关注字段：
1、**type** 连接类型，访问类型，表示MySQL在访问表时所采取的方式

性能：性能： system/const > eq_ref > ref >  range > index >  all 


（1）system：system是const的特例，表示表中只有一行记录，这个几乎不会出现，也作为了解。
（2）const: 通过索引直接访问一行数据，主键或唯一索引进行等值比较时出现

```sql
explain select * from user where id =2;
```

（3）eq_ref: 当使用连接查询时，如果查询使用的是唯一索引来匹配每个行

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


（7）all：进行了全表扫描，这时候表示通常需要增加索引来进行优化了


2、**key**：实际使用的索引，表示MySQL在执行查询时所使用的索引

3、**extra**：额外信息

> （1）Using index：select操作中使用了覆盖索引(Covering Index)，避免回表查询
> （2）Using where：使用了额外的 `WHERE` 条件进行过滤。通常就是查询条件没使用到索引，或不满足索引使用条件。
> （3）Using temporary：执行查询时使用了临时表来处理结果集，通常发生在排序操作或多个连接的情况下
> （4）Using filesort：将用外部排序而不是索引排序，这个其实也是让order by后面的字段走索引就行了

## 2.2、索引分类

1、从逻辑功能上划分：主键索引、唯一索引、普通索引、全文索引

>主键索引：每个表中唯一行的索引。主键索引要求主键列中的每个值都必须唯一且不能为空值
唯一索引：列的值在全表必须是唯一的，可以为空
普通索引：查询中经常出现的列，提升查询性能
全文索引：使用like '%xxx%'进行模糊查询时，字段的索引就会失效。因此，在数据量大的情况下，通过此种方式查询的效率极低。这个时候，就可通过全文索引（Full-Text Search）来进行优化。全文索引（Full-Text Search）是将存储于数据库中的整本书或整篇文章中的任意信息查找出来的技术。它可以根据需要获得全文中有关章、节、段、句、词等信息，也可以进行各种统计和分析

从物理实现划分：聚簇索引、非聚簇索引

聚簇索引：数据存储与索引放到一块，索引结构的叶子结点保存了行数据
非聚簇索引：数据与索引分开存储，索引结构的叶子结点指向了数据对应的位置

## 2.3、innodb和myisam索引的区别

innodb使用聚簇索引，主键索引叶子结点存储完整的行数据，辅助索引叶子结点存储主键值，辅助索引访问数据总是需要二次查找，去主键索引中检索

myisam使用非聚簇索引，主键索引和辅助索引没什么区别，叶子结点分别存储主键值和对应的字段值，并且都有一个指向真正表数据的引用。都需要回原始表获取数据



![image.png|358](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202401091923809.png)



![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202401091924383.png)


## 2.4、覆盖索引

**覆盖索引（covering index）** 指一个查询语句的执行只需要从辅助索引中就可以得到查询记录，而不需要回表，去查询聚集索引中的记录。可以称之为实现了索引覆盖
从执行计划看，Extra的信息为**using index** ，即用到了索引覆盖。

比如

select id name from table where name ='ls'

这个就不会执行回表查询

select id name ,sex from table where name ='ls'

这个就要执行回表查询，因为sex在name索引上根本查不到。

那么怎么解决呢？就是name 与 sex 可以建立联合索引。这就是索引覆盖

## 1.6、索引设计原则

基本的原则就是：查询更快，占用空间更小，代码先行，索引后上

**适合建索引的场景**
1. 选择合适的列，经常用于查询，连接，过滤，排序的列作为索引列
2. 覆盖索引，覆盖索引可以减少回表次数，根据业务情况尽可能使用覆盖索引
3. 避免过长的索引：长字符串采用前缀索引，前缀索引长度不建议超过20

**不适合建索引的场景**
1. 频繁更新的字段不适合建立索引（需要重建索引）
2. 数据重复且发布比较均匀的的字段不适合建索引（唯一性太差的字段不适合建立索引），例如性别，真假值

## 1.7、索引失效的情况

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


## 1.8、三大范式

1. **第一范式（1NF）**：保证每列的原子性。如果数据库表中的所有字段值都是不可分解的原子值，就说明该数据库满足了第一范式
> 比如用户表里的名字字段，在中国可能直接存完整的姓名就可以了。但在国外姓和名常常需要分开使用，所以需要分成姓和名两个字段存储。所以是否满足原子性需要根据实际需求来确定


2.  **第二范式（2NF）**：满足第一范式前提，不能存在局部依赖。比如有联合主键有两个列，不能存在这样的属性，它只依赖于其中一个列，这就是不符合第二范式 

3.  **第三范式（3NF）**：消除传递依赖，每列都直接依赖于主键
> 假设存在关系模式主键1: 课程编号; 列1: 教师名; 列2: 教师家庭地址。显然满足第一范式和第二范式，但是教师家庭地址传递依赖于教师名，所以不满足第三范式


>所谓反范式化，是一种对范式化设计的数据库的**性能优化策略**，通过在表中增加冗余或重复的数据来提供数据库的读取性能。没有冗余的数据库不一定是最好的数据库，有时为了提高查询效率，就必须降低范式标准，适当保留冗余数据。具体操作就是在一个表中增加别一个表的冗余字段，减少了两个表查询时的关联，从而提高查询效率。

## 1.9、B树和B+树的区别

1.层级更低(更加矮胖),IO次数更少。由于 B+ 树的中间节点不含有实际数据，只有子树的最大数据和子树指针，因此磁盘页中可以容纳更多节点元素，也就是说同样数据情况下，B+ 树会 B 树更加“矮胖”，因此查询效率更快。
2.每次都需要查询到叶子节点，查询性能稳定。B树这时就能体现出优势，由于出现频率较高的树，在B树中往往在上层(非叶子结点),查找到该结点就会成功并结束查询，相对较快。而B+树由于非叶子结点关键字只是代表索引，因此在B+树中，无论查找成功与否，都是走了一条从根到叶子节点的路径。
3.B+树范围查询更加方便。 需要查询某个范围内的数据时，由于 B+ 树的叶子节点是一个有序链表，只需在叶子节点上遍历即可，不用像 B 树那样挨个中序遍历比较大小

## 1.10、mysql中有哪些锁

mysql数据库并发操作：
读读不需要加锁
写写需要加锁
读写使用mvcc实现（innodb）


1、全局锁

使用了全局锁后，整个数据库就处于只读状态了（读读共享，读写互斥，写写互斥），无论是执行对于数据的操作:insert、delete、update还是对于表结构的增删改查，都会被**阻塞**。全局锁主要用于做全库逻辑备份。

```sql
flush tables with read lock # 开启全局锁
unlock tables # 关闭全局锁
```

2、表锁

myisam中：对表的读操作，会自动加读锁，对表的写操作，自动加写锁

> 因为myisam没有事务，没有办法保证表数据的一致性

innodb中：非必要不使用表锁，主要是通过行锁来实现 

```sql
lock tables user read; # 手动添加表读锁
unlock tables # 关闭锁
```

3、行锁

行锁主要由innodb存储引擎提供。innodb支持两种类型的行锁：共享锁（s锁）和排它锁（x锁）

共享锁：也称为读锁，它允许一个事务读取一行事务，当一行数据被共享锁锁定，其它事务可以读取这行数据，但不能对其进行修改
排它锁：排它锁也称为写锁，它允许一个事务读取和修改一行数据，当一行数据被排它锁锁定时，其它事务不能读取也不能修改这行数据

在实际使用中，innodb还提供一种间隙锁的特性，它不仅锁定一个具体的行，还锁定前后的间隙，可以防止其它事务插入新的行到已锁定行的前后，从而可以解决一些并发问题。

## 1.11、mysql行锁实现

innodb通过给索引记录加锁的方式实现行级锁

具体来说，innodb实现了三种行锁的算法：记录锁、间隙锁和netx-key锁（临界锁）

记录锁是针对索引记录的锁定
间隙锁是锁定索引记录之间的间隙
next-key锁相当于记录锁+记录之前的一个间隙锁

## 1.12、并发访问下事务产生的问题

当同时有多个用户在访问同一张表中的记录，每个用户在访问的时候都是一个单独的事务。事务在操作时的理想状态是：事务之间不应该相互影响，实际应用的时候会引发下面三种问题：

1. 脏读： 一个事务（用户）读取到了另一个事务没有提交的数据
2. 不可重复读：一个事务多次读取同一条记录，出现读取数据不一致的情况。一般因为另一个事务更新了这条记录而引发的。
3. 幻读：在一次事务中，多次读取到的条数不一致

## 1.13、事务隔离级别

为了尽量避免这些问题的发生。通过数据库本身的功能去避免，设置不同的隔离级别

| **级别** | **名字** | **隔离级别** | **脏读** | **不可重复读** | **幻读** | **数据库默认隔离级别** | 解释 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 读未提交 | read uncommitted | 是 | 是 | 是 |  | 事务A可以读取到事务B未提交的数据 |
| 2 | 读已提交 | read committed | 否 | 是 | 是 | Oracle和SQL Server | 事务A只能读取其它事务已提交的数据（避免了脏读） |
| 3 | 可重复读 | repeatable read | 否 | 否 | 是（innodb除外） | MySQL | 保证在同一个事务中多次读取同样数据的结果是一样的 |
| 4 | 串行化 | serializable | 否 | 否 | 否 |  | 事务串行化顺序执行 |

> 隔离级别越高，安全性就越高，性能越低

innodb在可重复度的隔离级别下可以解决幻读，靠的就是mvcc机制



## 1.12、mvcc原理

多版本并发控制，数据库隔离级别读已提交、可重复读 都是基于MVCC实现的，相对于加锁简单粗暴的方式，它用更好的方式去处理读写冲突，能有效提高数据库并发性能。【注意】：只有快照读才会使用MVCC，当前读使用行锁+间隙锁（临键锁Next-Key Locks）实现、

>快照读：普通的select语句
>当前读：delete、 update 、insert、select for update

它的实现依赖于三个概念：版本链和快照读和ReadView


1. 版本链：多个事务并行操作某一行数据时，不同事务对该行数据的修改会产生多个版本，然后通过回滚指针（roll_pointer），连成一个链表，这个链表就称为版本链(最新记录+undo-log)

> undo log，**回滚日志**，用于记录数据被修改前的信息。在表记录修改之前，会先把数据拷贝到undo log里，如果事务回滚，即可以通过undo log来还原数据. 可以这样认为，当delete一条记录时，undo log 中会记录一条对应的insert记录

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202311101605938.png)


2. 快照读：读取的是记录数据的可见版本，不加锁，普通的select语句都是快照读

> 当前读：读取的是记录数据的最新版本，显式加锁的都是当前读。如update，delete，select..for update
> 【注意】：只有快照读才会使用MVCC，当前读使用行锁+间隙锁（临键锁Next-Key Locks）实现

3. RedaView：读视图，其实就是一个数据结构，包含四个属性：当前活跃事务编号集合，最小活跃事务编号，预分配事务编号（当前最大事务编号+1），创建者事务编号

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202311101605005.png)


> 读已提交：在每次执行快照读的时候生成ReadView
> 可重复读：（同一事务）只在第一次使用快照读的时候生成ReadView，后续复用（解决不可重复读）,（但两次快照读之间存在当前读，也会重新生成，所以存在幻读的问题）

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202311101606136.png)


> 流程：
> 1. 在快照读时生成一个ReadView（注意在读已提交下，每次快照读都生成一个ReadView）
> 2. 遍历版本链，判断是否符合ReadView规则（这个过程其实就是找它最近一次事务提交的数据版本）
> 3. 返回符合规则的数据

## 1.13、mysql死锁的原因和处理办法

死锁的关键在于：两个(或以上)的Session加锁的顺序不一致。

```sql
# session1关闭自动提交
select * from user where id in (2,4,5) for update # 会加写锁
# session2
select * from user where id in (3,4,5) fot update # 锁等待...
```

处理死锁：

```sql
show processlist
kill + 线程id
```
## 1.14、undo log、redo log、bin log的作用

1、undo log
Undo Log称为回滚日志，回滚记录到某个特定的版本，用来保证事务的原子性和一致性


2、redo log
Redo Log称为重做日志，提供写入操作，恢复提交事务修改页的操作，用来保证事务的持久性

3、bin log
binlog是逻辑日志，记录内容是语句的原始逻辑，类似于“给 ID=2 这一行的 c 字段加 1”，属于MySQL Server层。不管用什么存储引擎，只要发生了表数据更新，都会产生 binlog 日志 
数据库的数据备份、主备、主主、主从都离不开binlog，需要依靠binlog来同步数据，保证数据一致性


## 1.15、小表驱动大表

小表驱动大表是指在关联查询中，将小表作为驱动表，大表作为被驱动表。这种方式通常能够提高查询性能和效率

```sql
select * from main_table m inner join vice_table v where m.id = v.id and m.id > 10 and n.id <35;

# 假设m表为驱动表，那么就会先去查询m表。在根据条件m.id>10获取到满足条件的第一条记录后(假设是一条id为11的记录)，马上去和被驱动表v做匹配，此时v表的查询条件就是：v.id<35 and v.id=11;

# 不难发现，v.id=11 这个条件，是在获取到驱动表记录后才产生的，因此可以说是一个“被驱动”产生的条件。从中也可以看出，由于这个“被驱动”产生的条件一直在随着驱动表搜索出来的内容在发生变化，因此被驱动表必然需要被多次访问
```

假设m表作为驱动表记录数为A,n作为被驱动表记录数为B，假设A<B,那么:

从访问表的次数来看：m表访问1次，n表访问A次
从访问记录数来看：m表访问记录A条，n表访问记录A*B次

如果m表和n表的位置反过来，显然：

表的访问次数：n表访问1次，m表访问B次
记录数的访问：n表访问记录B条，m表访问记录B*A次

如果仅仅从两个次数上来看，不难看到，当记录数少的m表作为驱动表时，两个“次数”均小一些。
此外，如果驱动表记录数更大，意味着该表要更长期的占用内存，对内存空间的挑战显然不小。而如果是被驱动表记录数大，我们还可以选择分批载入内存，多次磁盘IO虽然说损耗性能，但至少内存大小不成问题

## 1.16、innodb和myisam区别

1、innodb支持事务，外键。myisam不支持
2、innodb支持行锁，myisam不支持
3、innodb使用聚簇索引，myisam使用非聚簇索引
4、innodb不存储行数，myisam存储行数

## 1.17、mysql架构

Server 层负责建立连接、分析和执行 SQL；
存储引擎层负责数据的存储和提取，默认为InnoDB

![|725](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202401261116175.png)

服务层包含：连接器，解析器，执行器

连接器：与mysql客户端建立TCP连接。校验客户端用户名密码
解析器：进行词法和语法分析，判断是否满足sql语法
执行器：预处理阶段判断是否存在对应的表和字段，优化阶段就是确定合适索引的过程，执行阶段会从存储引擎读数据，返回客户端


## 1.18、索引下推

索引下推的下推其实就是指将部分上层（服务层）负责的事情，交给了下层（引擎层）去处理.存储引擎根据联合索引按照条件过滤。按照过滤后的数据再一回表扫描。目的就是减少回表次数

```sql
explain select * from employees where name like "yc%" and age = 18
```

mysql5.6版本前：这个查询只能匹配到yc开头的索引，然后拿这些索引对应的主键，到主键索引找对应的记录，再对比age是否满足
5.6以后使用了索引下推，匹配到yc开头的索引后，同时还会在索引里过滤age字段，最后拿着过滤完的主键id再回表查询


>当一条sql使用索引下推技术后，在explain执行计划中，extra列中出现using index condition的信息

## 1.19、cout(1)和count(星)和count(字段)区别

1、count（1）和count（星）是一样的，不管是innodb还是myisam （其中myisam一个字段存储行数，innodb利用索引统计）
2、count（字段）会选择该字段的可用索引进行统计，如果没有则进行全部扫描，同时它会过滤掉null值
# 8、mq

## 8.1、rabbitmq如何保证可靠性

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


## 8.2、rabbitmq避免重复消费
保证消费者的幂等性（调用方，对一个系统进行重复调用（参数全部相同），不论重复调用多少次，这些调用对系统的影响都是相同的效果）

如何保证幂等性？
1. 使用代码的逻辑判断，判断消息状态是否已经被消费过了

> 使用数据库一个表来记录消息的状态（或者用redis来记录也可以）。每次消费之前，都查询判断消息的状态，是否已经被消费了。这个状态可以是id。例如，如果消息是订单，而且id是全局唯一的，那么只需要拿这个订单id来做判断即可。

2. 使用token，要申请，一次有效性。

> 在创建订单的场景下。首先，先生成一个token，返回给客户端存起来，同时也在后端存起来（redis）。当他创建订单的时候，带着这个token来请求后端，后端判断redis里是否存在，如果存在，则操作成功，同时删除token（删除了之后，就算他重复多次调用，前边的判断不成立，这样子就不能多次操作了）

## 8.3、死信队列

死信场景：
1. 消息被消费方否认确定。并且此时requeue属性被设置为false
2. 消息在队列的存活时间超过设置的TTL时间
3. 消息队列的消息数量超过最大队列长度

如果配置了死信队列，消息会被丢进死信队列，如果没有，消息会被丢弃

```java
 @Bean
    public Queue DirectQueue1(){
        Map<String,Object> args = new HashMap<>();
        args.put("x-message-ttl",5000);//这里一定是int值
        //指定进入的死信交换机
        args.put("x-dead-letter-exchange","dead_direct_exchange");
        //direct模式需要配置,fanout模式是不需要配置的
        args.put("x-dead-letter-routing-key","dead");
        return new Queue("ttl_direct_queue",
                true,false,false,args);
    }
```

为每个死信队列配置一个死信交换机，这里同一个项目的死信交换机就共用一个


## 8.6、延迟队列

TTL 是 RabbitMQ 中一个消息或者队列的属性，表明一条消息或者该队列中的所有消息的最大存活时间，单位是毫秒。目前有两种方法可以设置消息的 TTL。第一种方法是通过队列属性设置，队列中所有消息都有相同的过期时间。第二种方法是对消息本身进行单独设置，每条消息的 TTL 可以不同。如果两种方法一起使用，则消息的 TTL 以两者之间较小的那个数值为准。消息在队列中的生存时间一旦超过设置的 TTL 值时，就会变成“死信”。

>如果设置了队列的 TTL 属性，那么一旦消息过期，就会被队列丢弃（如果配置了死信队列则会被丢到死信队列中），而第二种方式，消息即使过期，也不一定会被马上丢弃，因为消息是否过期是在即将投递到消费者之前判定的，如果当前队列有严重的消息积压情况，则已过期的消息也许还能存活较长时间
## 8.4、rabbitmq工作模式

**1、简单模式**
一个生产者将消息发送到一个队列中，一个消费者从这个队列中获取消息并进行处理。这种模式仅适用于单个生产者和单个消费者的场景

![简单模式](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202307241557273.png)


**2、工作队列模式**
一个生产者将消息发送到一个队列中，多个消费者从这个队列中获取消息并进行处理。这种模式可以提高消息的处理效率

> 对于任务过重或任务较多情况使用工作队列可以提高任务处理的速度

![工作队列模式](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202307241632591.png)

**3、发布订阅模式**
一个生产者将消息发送到一个交换机中，交换机将消息广播到所有绑定的队列中，多个消费者可以分别从这些队列中获取消息并进行处理。这种模式适用于需要将消息广播到多个消费者的场景

![发布订阅模式](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202307241633780.png)


**4、路由模式**
一个生产者将消息发送到一个交换机中，交换机根据消息的Routing Key将消息路由到对应的队列中，多个消费者可以从这些队列中获取消息并进行处理。这种模式适用于需要根据消息的路由键进行精确匹配的场景

![路由模式](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202307241647936.png)


## 8.5、交换机类型

**1、Direct Exchange（直连交换机）**

根据Routing Key(路由键)进行投递到不同队列。如果路由键不匹配，那么就不会发送到任何队列中去。

![直连交换机](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202307241551705.png)


**2、Fanout Exchange（广播交换机）**

该类型的交换机会将⼀条消息⼴播到绑定到该交换机的所有队列上，不论你设置的路由键是什么

> 如果想让多个消费者消费到数据必须不指定queues，指定交换机


**3、Topic Exchange（主题交换机）**

将路由键和某模式进行匹配。此时队列需要绑定要一个模式上。符号“#”匹配一个或多个词，符号“\*”匹配不多不少一个词。因此“abc.#”能够匹配到“abc.def.ghi”，但是“abc.\* ” 只会匹配到“abc.def”。

![主题交换机](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202307241552123.png)


**4、Headers Exchanges（头交换机）**

与routingKey无关，匹配机制是匹配消息头中的属性信息。在绑定消息队列与交换机之前声明一个map键值对，通过这个map对象实现消息队列和交换机的绑定。当消息发送到RabbitMQ时会取到该消息的headers与Exchange绑定时指定的键值对进行匹配；如果完全匹配则消息会路由到该队列，否则不会路由到该队列()

> 匹配规则x-match有下列两种类型：
x-match = all ：表示所有的键值对都匹配才能接受到消息
x-match = any ：表示只要有键值对匹配就能接受到消息

![image (6).png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202307241557662.png)


## 8.6、rabbitmq如何保证消息的顺序性

出现顺序错乱的场景：

一个queue，有多个consumer去消费，这样就会造成顺序的错误，consumer从MQ里面读取数据是有序的，但是每个consumer的执行时间是不固定的，无法保证先读到消息的consumer一定先完成操作，这样就会出现消息并没有按照顺序执行，造成数据顺序错误


解决方案：
拆分成多个queue，每个queue一个consumer，就是多一些queue而已，确实是麻烦点；
# 9、dubbo

## 1.1、dubbo调用流程

1. 服务启动，包括服务提供者和消费者的启动，封装服务调用链路。
2. 服务提供者在启动时，向注册中心注册自己提供的服务。
3. 服务消费者在启动时，向注册中心订阅自己所需的服务。
4. 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
5. 服务消费者，从提供者地址列表中，基于服务路由信息、负载均衡规则，选一台提供者进行调用。
6. 服务消费者和提供者，在内存中累计调用次数和调用时间，定时发送一次统计数据到监控中心。
7. 服务提供方停止服务或者服务调用方关闭JVM的时候，会将provider及consumer进行销毁处理。

## 1.2、dubbo和springCloud区别
两者都是现在主流的分布式框架，但却存在不少差异：

- **生态环境不同：** SpringCloud定位为微服务架构下的一站式解决方案（网关，分布式配置，服务跟踪）；Dubbo 是 SOA 时代的产物，它的关注点主要在于服务的调用和治理
- **调用方式：** SpringCloud是采用Http协议做远程调用；Dubbo默认使用的是Dubbo协议进行远程调用
- **组件差异比较多**，例如SpringCloud注册中心一般用Eureka，而Dubbo用的是Zookeeper

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202311141619063.png)


## 1.3、RPC和HTTP的区别

RPC，远程过程调用。RPC 和 HTTP 就不是一个层级的东西，所以严格意义上这两个没有可比性，也不应该来作比较。http可以理解为RPC的一种实现

RPC可以用 HTTP 来传输，也可以基于 TCP 自定义协议传输

## 1.4、dubbo支持协议

**1、dubbo 默认协议：**

- 单一 TCP 长连接，Hessian 二进制序列化和 NIO 异步通讯
- 不适合传送大数据包的服务

**2、rmi 协议：**

- 采用 JDK 标准的 java.rmi.* 实现，采用阻塞式短连接和 JDK 标准序列化方式
- 对传输数据包不限，消费者和传输者个数相当

**3、hessian 协议：**

- 底层 Http 通讯，Servlet 暴露服务，Dubbo 缺省内嵌 Jetty 作为服务器实现
- 通讯效率高于 WebService 和 Java 自带的序列化
- 适用于传输数据包较大，提供者比消费者个数多，提供者压力较大

**4、http 协议：**

- 基于 http 表单的远程调用协议，短连接，json 序列化
- 对传输数据包不限，不支持传文件

**5、webservice 协议：**

- 基于 Apache CXF 的 frontend-simple 和 transports-http 实现，短连接，SOAP文本序列化
- 可与原生 WebService 服务互操作
- 适用于系统集成、跨语言调用

**6、thrift 协议：**

- 对 thrift 原生协议的扩展添加了额外的头信息
- 使用较少，不支持传 null 值

## 1.5、zookeeper概念

zk = 数据模型 + watch机制

zookeeper是一个开源的分布式协调服务。主要功能有服务命名，配置管理，分布式锁，分布式通知观察，分布式高可用（zab）

dubbo中我们将它作为注册中心就是用到了它的服务命名，配置管理，和分布式通知观察的功能
具体流程就是：（1）在Dubbo启动时，服务提供者会将自己的地址信息注册到Zookeeper的指定节点上。（2）Dubbo中的服务消费者会在Zookeeper上监听服务提供者节点。通过注册监听，消费者可以实时获取节点的变化，包括服务上线、下线或者地址变更 （3）当服务提供者上线、下线或地址发生变化时，Zookeeper会发送通知给监听者（即服务消费者）。这样，消费者就能够得知最新的服务提供者列表，并进行相应的调整（4）基于收到的服务提供者列表，Dubbo可以实现负载均衡策略，以选择合适的服务提供者来处理请求

## 1.6、zk是如何保存数据的
zk的数据是保存在节点上的，节点就是znode，多个znode之间构成一颗树的目录结构

对于znode，包含四个部分：data（保存数据）、acl（读写权限）、stat（当前znode的元数据创建时间，修改时间，版本号等）、child（当前节点的子节点）

## 1.7、dubbo负载均衡策略

1. Random（随机）：随机选择一个可用的服务提供者进行调用。每个服务提供者有相同的权重。（默认）
    
2. RoundRobin（轮询）：按照轮询的方式依次选择可用的服务提供者进行调用。每个服务提供者有相同的权重。
    
3. LeastActive（最少活跃数）：选择活跃数最少的服务提供者进行调用，即当前正在处理的请求最少的提供者。活跃数指的是正在处理请求的数量。
    
4. ConsistentHash（一致性哈希）：根据请求的参数或者自定义的Key进行哈希计算，然后选择哈希值对应的服务提供者。这样可以保证同一Key的请求始终路由到同一提供者。
    
5. LruCache（最近最少使用）：根据调用的历史记录进行缓存，优先选择最近最少使用的服务提供者进行调用。
    
6. WeightedRandom（加权随机）：根据权重随机选择一个可用的服务提供者进行调用。权重越高的服务提供者被选中的概率越大。
    
7. WeightedRoundRobin（加权轮询）：按照轮询的方式选择可用的服务提供者进行调用，但每个服务提供者的选择次数会根据权重进行调整。
    
8. Sticky（粘滞会话）：粘滞会话策略会将同一消费者的请求始终路由到同一提供者，除非该提供者不可用。这样可以保持会话的一致性

## 1.8、zookeeper选举机制

zab协议，半数机制，虽然在配置文件中并没有指定Master和Slave。但是，Zookeeper 工作时，是有一个节点为 Leader，其他则为 Follower，Leader是通过内部的选举机制临时产生的

1、Server1 先投票，投给自己，自己为 1 票，没有超过半数，根本无法成为 leader，顺水推舟将票数投给了 id 比自己大的 Server2
2、Server2 也把自己的票数投给了自己，再加上 Server1 给的票数，总票数为 2 票，没有超过半数，也 无法成为 leader，也学习 Server1，顺水推舟，将自己所有的票数给了 id 比自己大的 Server3
3、Server3 得到了 Server1 和 Server2 的两票，再加上自己投给自己的一票。3 票超过半数，顺利成为 leader
4、Server4 和 Server5 都投给自己，但是无法改变 Server3 的票数，只好听天由命，承认 Server3 是 leader

## 1.9、zookeeper节点类型
| 类型 | 描述 |
| ---- | ---- |
| 持久节点 | 节点创建后，会一直存在，不会因客户端会话失效而删除 |
| 持久顺序节点 | 基本特性与持久节点一致，创建节点的过程中，ZooKeeper 会在其名字后自动追加一个单调增长的数字后缀，作为新的节点名 |
| 临时节点 | 客户端会话失效或连接关闭后，该节点会被自动删除 |
| 临时顺序节点 | 基本特性与临时节点一致，创建节点的过程中，ZooKeeper 会在其名字后自动追加一个单调增长的数字后缀，作为新的节点名 |


## 1.10、zookeeper集群角色

leader:zookper集群的核心，处理客户端的写请求
follower：可以直接处理客户端的读请求，写请求会转发到leader。同时参与leader的选取
observer：观察者，提供读服务，不参与leader的选取

## 1.11、zookeeper怎么保证主从节点状态一致

zab，zookeeper原子广播协议。保证各个server之间的同步。分三个阶段

1. 发现（选举 Leader 过程）
2. 同步（选出 Leader 后，Follower 和 Observer 需进行数据同步）
3. 广播（同步之后，集群对外工作响应请求，并进行消息广播，实现数据在集群节点的副本存储）

## 1.12、zookeeper四种状态

LOOKING ：寻找 Leader 状态 当服务器处于这种状态时，表示当前没有Leader，需要进入选举流程 
FOLLOWING ：从机状态，表明当前服务器角色是Follower 
OBSERVING ：观察者状态，表明当前服务器角色是Observer 、
LEADING ：领导者状态，表明当前服务器角色是Leader

## 1.13、数据变更通知（Watch机制）

zk的订阅发布也就是watch机制，是一个轻量级的设计。因为它采用了一种推拉结合的模式。一旦服务端感知主题变了，那么只会发送一个事件类型和节点信息给关注的客户端，而不会包括具体的变更内容，所以事件本身是轻量级的，这就是所谓的“推”部分。然后，收到变更通知的客户端需要自己去拉变更的数据，这就是“拉”部分

watch具体流程：

- 首先，是客户端通过指定命令比如`exists`、`get`，对特定路径增加watch
- 然后服务端收到请求以后，用HashMap保存这个客户端会话以及对应关注的节点路径，同时客户端也会使用HashMap  
    存储指定节点和事件回调函数的对应关系。
- 当服务端指定被watch的节点发生变化后，就会找到这个节点对应的会话，把变化的事件和节点信息发给这个客户端。
- 客户端收到请求以后，从ZkWatcherManager里面对应的回调方法进行调用，完成事件变更的通知。

# 10、SpringCloud

## 10.1、项目中springCloud用的哪些组件

|  | spring cloud官方 | spring cloud netflix | spring cloud alibaba |
| --- | --- | --- | --- |
| 服务注册发现 | - | Eureka | nacos |
| 分布式配置 | spring cloud config | Archaius | nacos |
| 服务熔断 | - | Hystrix | Sentinel |
| 服务调用 | Open Fegin | Fegin | Dubbo RPC |
| 服务路由 | spring cloud gateway | zuul | dubbo proxy |
| 分布式消息 | rabbitmq | - | rocketmq |
| 负载均衡 | - | ribbon | dubbo LB |
| 分布式事务 | - | - | Seata |

## 10.2、eureka自我保护机制

默认情况下，如果eureka server在一定时间内（默认90秒）没有接收到某个微服务实例的心跳，eureka server将会移除掉该实例（eureka客户端每隔60秒会发送一次心跳包）

但是当网络分区发生故障，微服务和eureka server之间无法通信，但是微服务本身是正常的，此时不应该移除这个服务，所以就引入了自我保护机制

Eureka服务端会检查最近15分钟内所有Eureka 实例正常心跳占比，如果低于85%就会触发自我保护机制。触发了保护机制，Eureka将暂时把这些失效的服务保护起来，不让其失效

> 自我保护机制清除：
> （1）心跳次数高于预期值
> （2）自我保护机制被禁用

## 10.3、hystrix原理

正常hystrix是关闭状态
当失败率达到一定的时候（默认10秒内超过50%的请求失败）会自动开启断路器，在熔断器打开期间，任何到该接口的请求均不可用，同时在断路器打开5s后断路器会处于半开状态，此时断路器允许放行一个请求到该服务接口，如果执行成功，断路器关闭，执行失败则重新打开


## 10.4、分布式锁实现

**1、redis实现**

在分布式的环境下,会发生多个server并发修改同一个资源的情况,这种情况下,由于多个server是多个不同的JRE环境,而Java自带的锁局限于当前JRE,所以Java自带的锁机制在这个场景下是无效的,那么就需要我们自己来实现一个分布式锁

1. 通过`set...nx...`命令,将加锁、过期命令编排到一起,把他们变成原子操作。完整命令：set 订单id  random-value nx ex seconds

> 其实目前通常所说的Setnx命令，并非单指Redis的setnx key value这条命令。
> 一般代指Redis中对set命令加上nx参数进行使用

> （1）nx  ex 是set指令的两个参数： ex过期时间    nx只有key不存在时设置新的key/value。至于添加的value值无所谓可以是任意值(根据业务需求)，只要保证多个线程使用的是同一个key，所以多个线程添加时只会有一个线程添加成功，就只会有一个线程能够获取到锁。而释放锁锁只需要将锁删除即可。
> （3）设置过期时间（EX）作用：如果客户端忘记解锁,那么这种情况就很有可能造成死锁
> （4）NX的作用：避免重复获取锁


2. 解锁的时候进行判断,通过get获取value。判断是自己持有的锁才能释放,否则不能释放。另外判断,释放这两步需要保持原子性，所以通过Lua脚本将两个命令编排在一起,而整个Lua脚本的执行是原子的

> if redis.call("get",KEYS[1]) == ARGV[1] then return redis.call("del",KEYS[1]) else return 0 end

> **这里为什么要用原子操作？**
> 主要是怕误将其他客户端的锁解开。比如客户端A加锁，一段时间之后客户端A解锁，在进入unlock后执行jedis.del()之前，锁突然过期了，此时客户端B尝试加锁成功，然后客户端A再执行del()方法，则将客户端B的锁给解除

```java
import redis.clients.jedis.Jedis;

public class RedisLock {
    private Jedis jedis;

    public RedisLock(Jedis jedis) {
        this.jedis = jedis;
    }

    /**
     * 尝试获取锁
     * @param lockKey 锁的名称
     * @param requestId 请求标识，用于释放锁
     * @param expireTime 锁的过期时间
     * @return 是否成功获取锁
     */
    public boolean tryLock(String lockKey, String requestId, int expireTime) {
        String result = jedis.set(lockKey, requestId, "NX", "EX", expireTime);
        if ("OK".equals(result)) {
            return true;
        }
        return false;
    }

    /**
     * 释放锁
     * @param lockKey 锁的名称
     * @param requestId 请求标识，用于判断是否是同一个客户端
     * @return 是否成功释放锁
     */
    public boolean releaseLock(String lockKey, String requestId) {
        String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
        // jedis.eval是Jedis客户端提供的一个用于执行Lua脚本的方法
        Long result = (Long) jedis.eval(script, 1, lockKey, requestId);
        if (result == 1) {
            return true;
        }
        return false;
    }
}

```

> 另外可以通过Redisson框架，它的底层原理其实也是这个setnx


**2、zookeeper实现**

ZooKeeper 分布式锁是基于 **临时顺序节点** 来实现的，锁可理解为 ZooKeeper 上的一个节点，当需要获取锁时，就在这个锁节点下创建一个临时顺序节点。当存在多个客户端同时来获取锁，就按顺序依次创建多个临时顺序节点，但只有排列序号是第一的那个节点能获取锁成功，其他节点则按顺序分别监听前一个节点的变化，当被监听者释放锁时，监听者就可以马上获得锁。

而且用临时顺序节点的另外一个用意是如果某个客户端创建临时顺序节点后，自己意外宕机了也没关系，ZooKeeper 感知到某个客户端宕机后会自动删除对应的临时顺序节点，相当于自动释放锁。

## 10.5、如何保证分布式事务一致性？

1. 首先是设计方案尽可能规避分布式事务的场景（相似的业务放在一起，不要过度的拆分）
2. 根据业务场景，选择使用柔性事务（ap）还是强事务(cp)
    如果可以允许消息存在一段时间不一致，只要保证最终一致性，可以用本地消息表来做。如果要保证一致性，可以用2pc，具体实现方案有阿里的seata
3. 本地消息表（柔性事务）
不去同步的调用，先将要请求的消息插入到本地的消息表中，消息状态为正在处理，起一个定时任务去查询消息表，将正在处理的消息发送到消息队列，B中消息处理完后，向一个return队列发送一个成功的消息，A订阅了该消息队列，收到成功的消息后将状态该为处理完毕。（被调用方应保证幂等性，如库存系统在减库存前先查流水表，看该订单是否扣过库存，扣过就不执行）

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202311150941880.png)

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202311150942538.png)


## 11.6、CAP理论

一个分布式系统最多只能同时满足一致性（Consistency）、可用性（Availability）和分区容错性（Partition tolerance）这三项中的两项

- **一致性** ：数据在多个副本之间能够保持一致的特性。
- **可用性**：系统提供的服务一直处于可用的状态，每次请求都能获得正确的响应。
- **分区容错性**：分布式系统在遇到任何网络分区故障的时候，仍然能够对外提供满足一致性和可用性的服务。

> 分区：在分布式系统中，不同的节点分布在不同的子网络中，由于一些特殊的原因，这些子节点之间出现了网络不通的状态，但他们的内部子网络是正常的。从而导致了整个系统的环境被切分成了若干个孤立的区域，这就是分区

分布式系统，是避免不了分区的，分区容错性是一定要满足的。所以权衡原则就是：cp和ap

ZooKeeper 保证的是 CP。 任何时刻对 ZooKeeper 的读请求都能得到一致性的结果，但是， ZooKeeper 不保证每次请求的可用性比如在 Leader 选举过程中或者半数以上的机器不可用的时候服务就是不可用的。

Eureka 保证的则是 AP。 Eureka 在设计的时候就是优先保证 A （可用性）。在 Eureka 中不存在什么 Leader 节点，每个节点都是一样的、平等的。因此 Eureka 不会像 ZooKeeper 那样出现选举过程中或者半数以上的机器不可用的时候服务就是不可用的情况。Eureka 保证即使大部分节点挂掉也不会影响正常提供服务，只要有一个节点是可用的就行了。只不过这个节点上的数据可能并不是最新的。

Nacos 不仅支持 CP 也支持 AP

## 11.7、base理论三个特性

CAP 是分布式系统设计理论，BASE 是 CAP 理论中 AP 方案的延伸

**1.基本可用**
假如系统出现了不可预知故障，允许损失部分可用性，当然也不能完全不可用。
损失的这部分可用性指的是什么？
- **响应时间上的损失**：正常情况下的搜索引擎0.5秒即返回给用户结果，而基本可用的搜索引擎可以在2秒作用返回结果。
- **功能上的损失**：在一个电商网站上，正常情况下，用户可以顺利完成每一笔订单。但是到了大促期间，为了保护购物系统的稳定性，部分消费者可能会被引导到一个降级页面。
 
**2.软状态**
软状态指允许系统中的数据存在中间状态（**CAP 理论中的数据不一致**），并认为该中间状态的存在不会影响系统的整体可用性，即允许系统在不同节点的数据副本之间进行数据同步的过程存在延时。

**3.最终一致性**
最终一致性强调的是系统中所有的数据副本，在经过一段时间的同步后，最终能够达到一个一致的状态。因此，最终一致性的本质是需要系统保证最终数据能够达到一致，而不需要实时保证系统数据的强一致性。

# 11、redis

## 11.1、redis工作模式


单机模式：是最简单的 Redis 工作模式。在单机模式下，Redis 只运行在单个节点上，数据存储在该节点的内存中。这种模式适用于小规模应用或开发环境

主从复制模式：通过将数据从主节点复制到一个或多个从节点来提高数据的可靠性和读取性能。主节点负责处理写入操作，从节点复制主节点的数据，并可以处理读取操作。主从复制模式适用于需要读取扩展和数据冗余的场景

哨兵模式：主从复制的基础上，引入了哨兵节点来监控主节点的状态。当主节点发生故障时，哨兵节点会自动将一个从节点升级为新的主节点，并将其他从节点重新配置为复制新的主节点。这种模式提供了故障转移和自动主节点切换的功能

cluster集群：即使使用哨兵，redis每个实例也是全量存储，每个redis存储的内容都是完整的数据。cluster是为了解决单机Redis容量有限的问题，将数据按一定的规则分配到多台机器，提高并发量

## 11.2、redis数据类型和使用场景

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

6、bitmap

使用场景：每日签到。因为Bit的值为0或者1，位图的每一位代表一天的签到，1表示已签，0表示未签。考虑到每月初需要重置连续签到次数，最简单的方式是按用户每月存一条签到数据（也可以每年存一条数据）。Key的格式为`u:sign:uid:yyyyMM`，Value则采用长度为4个字节（32位）的位图（最大月份只有31天）

```
# 用户1000在2023年2月7号签到
SETBIT u:sign:1000:202302 6 1 # 偏移量是从0开始，所以要把7减1
# 检查用户1000在2023年2月7号是否签到
GETBIT u:sign:1000:202302 6   # 偏移量是从0开始，所以要把7减1
# 统计用户1000在2月份签到次数
BITCOUNT u:sign:1000:202302
```


## 11.3、说说缓存穿透、击穿、雪崩的区别

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
 1.设置多级缓存，在存储层之前加上一层本地缓存。
 2.构建高可用的redis缓存，部署多个redis实例，个别不能用，也不至于宕机。

## 11.4、redis持久化策略

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


3、对比

|  | rdb | aof |
| --- | --- | --- |
| 占用存储空间 | 小（数据级） | 大（指令级） |
| 恢复速度 | 快 | 慢（需要执行指令） |
| 数据安全性 | 可能会丢失最后一次持久化后的数据 | 根据策略决定 |


# 12、nginx

## 12.1、nginx作用

1、正向代理
nginx不仅能做反向代理，实现负载均衡，还能用作正向代理来进行上网等功能
正向代理：位于客户端和原始服务器之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并制定目标（原始服务器），然后代理向原始服务器转发请求并将获得的内容返回给客户端，客户端才能使用正向代理。我们平时说的代理就是指正向代理【**代理客户端，服务端不知道实际发起请求的客户端**】

> 例子：A向C借钱，由于一些情况不能直接向C借钱，于是A想了一个办法，他让B去向C借钱，这样B就代替A向C借钱，A就得到了C的钱，C并不知道A的存在，B就充当了A的代理人的角色

2、反向代理
反向代理，以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求的客户端，此时代理服务器对外表现为一个反向代理服务器【**代理服务端，客户端不知道实际提供服务的服务端**】

> 例子：A向B借钱，B没有拿自己的钱，而是悄悄地向C借钱，拿到钱之后再交给A,A以为是B的钱，他并不知道C的存在

3、负载均衡

增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负载均衡 

4、动静分离
为了加快网站的解析速度，可以把 动态页面 和 静态页面 由不同的服务器来解析，加快解析速度。降低原来单个服务器的压力

> 静态资源请求（如html、css、图片等）由静态资源服务器处理，动态资源请求（如 jsp页面、servlet程序等）由 tomcat 服务器处理，tomcat 本身是用来处理动态资源的，同时 tomcat 也能处理静态资源，**但是 tomcat 本身处理静态资源的效率并不高**，而且还会带来额外的资源开销。利用 nginx 实现动静分离的架构，能够让 tomcat 专注于处理动态资源，静态资源统一由静态资源服务器处理，从而提升整个服务系统的性能 

## 12.2、负载均衡方案

1、轮询（默认）
每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动剔除
```shell


http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    upstream webservers{
      # weight 多台机器，可以配置权重值，权重高的服务将会优先被访问
      server  192.168.9.134:8081;
      server  192.168.9.134:8082;
    }
 
    server {
        listen       80;
        server_name  localhost;

        location / {
             #转发到负载服务上
            proxy_pass http://webservers/api/;
         }
    }
}
```

2、weight
weight 代表权重默认为 1,权重越高被分配的客户端越多。指定轮询几率，weight权重大小和访问比率成正比。用于后端服务器性能不均衡的情况。
```shell

    upstream webservers{
      server  192.168.9.134:8081 weight=8;
      server  192.168.9.134:8082 weight=2;
    }
```

3、ip_hash
每个请求按访问 ip 的 hash 结果分配，这样每个访客固定访问一个后端服务器
> 使用nginx+ip_hash这种策略代理，很好解决了同一用户访问同一个应用，session不共享的问题,实现session共享的问题

```shell
    upstream webservers{
      ip_hash;
      server  192.168.9.134:8081;
      server  192.168.9.134:8082;
    }
```

4、fair

按后端服务器的响应时间来分配请求，响应时间短的优先分配。
```shell
upstream webservers{
        server 192.168.9.134:8081;
        server 192.168.9.134:8082;
        fair;
}
```

5、url_hash

按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效
> 相同的url会被分配到同一个节点，主要为了提高缓存命中率

```shell
upstream webservers{
    hash &request_uri;
    server 192.168.9.134:8081;
    server 192.168.9.134:8082;
}
```

6、least_conn
按节点连接数分配，把请求优先分配给连接数少的节点。该策略主要为了解决，各个节点请求处理时间长短不一造成某些节点超负荷的情况
```shell
upstream webservers{
    least_conn;
    server 192.168.9.134:8081;
    server 192.168.9.134:8082;
}
```


# 13、linux

## 13.1、linux体系结构

用户空间(User Space) ：用户空间又包括用户的应用程序(User Applications)、C 库(C Library) 。
内核空间(Kernel Space) ：内核空间又包括系统调用接口(System Call Interface)、内核(Kernel)、平台架构相关的代码(Architecture-Dependent Kernel Code) 。

>为什么 Linux 体系结构要分为用户空间和内核空间的原因？
>现代 CPU 实现了不同的工作模式，不同模式下 CPU 可以执行的指令和访问 的寄存器不同。
>Linux 从 CPU 的角度出发，为了保护内核的安全，把系统分成了两部分。


## 13.2、linux文件系统

在 Linux 系统中有一个重要的概念：一切都是文件

/bin： 存放二进制可执行文件(ls,cat,mkdir等)，常用命令一般都在这里
/sbin: 存放二进制可执行文件，只有root才能访问。这里存放的是系统管理员使用的系统级别的管理命令和程序。如ifconfig等
/etc： 存放系统管理和配置文件
/home： 存放所有用户文件的根目录，是用户主目录的基点，比如用户user的主目录就是/home/user，可以用~user表示
/usr ： 用于存放系统应用程序
/opt： 额外安装的可选应用程序包所放置的位置。一般情况下，我们可以把tomcat等都安装到这里
/dev： 用于存放设备文件（网卡，光驱，键盘等）
/boot： 存放用于系统引导时使用的各种文件；
/lib ： 存放着和系统运行相关的库文件
/tmp： 用于存放各种临时文件，是公用的临时文件存储点
/var： 用于存放运行时需要改变数据的文件，也是某些大文件的溢出区，比方说各种服务的日志文件（系统启动日志等。）等；

## 13.3、什么是硬链接和软链接

**inode**即是（目录、档案）**文件**在一个**文件系统**中的**唯一标识**，需要访问这个文件的时候必须先找到并读取这个 文件的 inode

软连接：可以看成是 Windows 中的快捷方式, 可以让你快速链接到目标文件或目录, 它找到的是原文件名, 通过原文件名再找到真实的数据, 它自身有inode

它透过文件系统的 **inode** 来产生新的**文件名**, 注意不是产生新的文件, 换而言之, 就是一个文件使用了多个别名, 这个别名与源文件名都指向源文件的 **inode**

```shell
ln -s [源文件/目录路径] [软连接放置的路径及名字]
ln -s /root/a.txt /tmp

ln [源文件路径] [硬连接放置的路径及名字]
ln ./a.txt /tmp/a.txt_hard 
```

![image.png|525](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202401231442092.png)

删除源文件, 软连接失效, 硬链接无影响(还可以访问内容)
删除源文件和硬链接, 整个文件会被真正的删除

## 13.4、查看文件内容有哪些命令

vi 文件名 ：编辑方式查看，可修改；
cat 文件名 ：显示全部文件内容；
more 文件名 ：分页显示文件内容；（空格显示下一页）
less 文件名 ：与 more 相似，更好的是可以往前翻页；（b向上翻页，d向下翻页）
tail 文件名 ：仅查看尾部，还可以指定行数；（tail -n 100 a.txt）
head 文件名 ：仅查看头部,还可以指定行数；

## 13.5、vi编辑器使用

三种模式
命令模式：控制屏幕光标的移动，字符、字或行的删除。无法编辑
插入模式：在命令行模式下按i进入插入模式，这样就可以开始输入文字
底行模式：保存或退出vi。在命令行模式下按：进入底行模式

## 13.5、linux常见命令

一、常用的基本命令

1、关机开机

关机

```plaintext
shutdown-h now  立刻关机shutdown-h 3  3分钟后关机poweroff立刻关机
```

重启

```plaintext
shutdown-r now  立刻重启shutdown-r 3  3分钟后重启reboot立刻重启
```

2、帮助命令

```plaintext
--help命令shutdown --help：ifconfig  --help：查看网卡信息
```

```plaintext
man命令（命令说明书） man shutdown注意：man shutdown打开命令说明书之后，使用按键q退出
```

二、 目录操作命令

1、目录切换

```plaintext
cd /  切换到根目录cd /usr  切换到根目录下的usr目录cd ../  切换到上一级目录 或者  cd ..cd ~   切换到home目录cd -   切换到上次访问的目录
```

2、目录查看

```plaintext
ls查看当前目录下的所有目录和文件
ls-a  查看当前目录下的所有目录和文件（包括隐藏的文件）
ls-l 或 ll   列表查看当前目录下的所有目录和文件（列表查看，显示更多信息）
ls/dir   查看指定目录下的所有目录和文件   如：ls /usr
```

3、目录操作

3.1、创建目录

```plaintext
mkdir qq  在当前目录下创建一个名为qq的目录mkdir /usr/qq  在指定目录下创建一个名为qq的目录
```

3.2、删除目录或文件

删除文件：

```plaintext
rm文件 删除当前目录下的文件rm-f 文件 删除当前目录的的文件（不询问）
```

删除目录：

```plaintext
rm-r qq  递归删除当前目录下的qq目录rm-rf qq  递归删除当前目录下的qq目录（不询问）
```

全部删除：

```
rm -rf * 将当前目录下的所有目录和文件全部删除rm -rf /* 将根目录下的所有文件全部删除（慎用！）
```


3.3、目录修改

1、重命名目录

```plaintext
mv当前目录  新目录例：mv  qq  aa  将目录qq改为aa
```

2、剪切目录

```plaintext
命令：mv  目录名称  目录的新位置
例：将/usr/lib目录下的qq目录剪切到/usr目录下面   
mv  /usr/lib/qq  /usr
```

3、拷贝目录

```plaintext
cp -r  目录名称  目录拷贝的目标位置  -r代表递归
例：将/usr/lib目录下的qq目录复制到  /usr目录下面   
cp  /usr/lib/qq   /usr
```

3.4、搜索目录

```plaintext
命令：find  目录  参数  文件名称
例：查找/usr/lib目录下的所有以a开头的目录或文件
find  /usr/lib  -name  'a*'
```

三、文件操作命令

1、文件操作

1.1、新建文件

```plaintext
命令：touch  文件名
例：在当前文件目录创建一个名为ai.exe文件     touch ai.exe
```

1.2、删除文件

```plaintext
命令：rm -rf  文件名
例：删除ai.exe文件   rm -rf ai.exe
```

2、修改权限

```
# 读写执行权限修改 所有者，所属组，其它 4-2-1
chmod 777 a.txt
# 修改所有者
chown user file
# 修改所属组
chgrp root file
```

3、压缩和解压

```
# z：压缩 c：打包 v：过程 f：指定文件名 x：解压
tar -zcvf filename.tar.gz directory_name # 压缩
tar -xvf xxx.tar.gz -C /usr #（解压： -C代表解压的位置）
```


## 13.6、文件中字符串替换

1、sed -i 's/old_string/new_string/g' file.txt

s表示替换操作，old_string是要被替换的旧字符串，new_string是要替换成的新字符串，/g表示全局替换

2、replace old new -- file.txt

## 13.7、查找文件包含某某关键字的内容

greap "hello" a.txt


# 14、前端

## 14.1、vue常见指令

1、v-text
v-text 指令，会把该元素下面的所有内容替换掉。

<div v-text="hello vue">hello world</div>
现实结果是：hello vue

2、v-html
v-html 指令，会用一个HTML标签字符串，替换该元素下面的所有内容。

但是，不建议使用v-html指令，因为它会导致被恶意者进行XSS攻击的潜在风险。

<div v-html="'<span style=&quot;color:red&quot;>hello vue</span>'">
 
  hello world
 
</div>
现实结果是：字体颜色为红色的 hello vue

3、v-show
v-show 指令，控制元素的显示隐藏，元素存在并占据空间。

元素隐藏时，相当于给该元素添加了 CSS 样式：display:none;

<div v-show="show">hello vue</div>
 
<button @click="show = !show">changeShow</button>
4、v-if/v-esle-if/v-else
（1）、v-if
v-if指令，控制元素是否加载。

v-esle-if/v-else指令不能单独使用，必须配合v-if一起使用。

<div v-if="number===1">hello vue {{number}}</div>
 
<div v-else-if="number===2">hello world {{number}}</div>
 
<div v-else>hello someone {{number}}</div>
（2）、v-if 与 v-show
v-if：有更高的切换开销；
v-show：有更高的初始化开销。
若需要频繁的切换则使用 v-show 比较好，否则使用 v-if 比较好。

5、v-for
v-for 指令，for循环，基于源数据多次渲染元素或模板块。

v-for 既可以渲染一个数组，也可以渲染一个对象。

（1）、v-for 渲染一个数组
<div v-for="(item, idx) in [1, 2, 3]" :key="idx">
 
    {{item}}
 
</div>
// 渲染的结果：
// 1
// 2
// 3
（2）、 v-for 渲染一个对象
<div v-for="(val, key) in {one: 1, two: 2}" :key="key">
 
    {{key}}: {{val}}
 
</div>
// 渲染的结果：
// one: 1
// two: 2
（3）、v-if 与 v-for
同时使用 v-if 和 v-for 是不推荐的，因为这样二者的优先级不明显。请查看风格指南获得更多信息。

当 v-if 与 v-for 一起使用时：

在 vue2 中 v-for 比 v-if 有更高的优先级。这意味着 v-if 将分别重复运行于每个 v-for 循环中。
在 vue3 中 v-if 比 v-for 有更高的优先级。这意味着 v-if 的条件将无法访问到 v-for 作用域内定义的变量别名。
vue3 官网之 v-for 与 v-if

（4）、v-for 之 key
①、为什么需要给 v-for 设置 key？

这牵扯到 vue 的 vnode 的 Diff 算法的特点，请参见此文。

②、在 v-for 中直接用 index 作为 key 的值有什么不好？


# 15、算法

## 15.1、两数之和(hash表)

给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标

思路：最简单的暴力解法使用两层遍历，时间复杂度O(n²)，空间复杂度O（1）。考虑使用空间换时间 -> hash表

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> hashtable = new HashMap<Integer, Integer>();
        for (int i = 0; i 《 nums.length; ++i) {
            if (hashtable.containsKey(target - nums[i])) {
                return new int[]{hashtable.get(target - nums[i]), i};
            }
            hashtable.put(nums[i], i);
        }
        return new int[0];
    }
}
```


## 15.2、三数之和（排序+双指针）

给你一个整数数组 nums ，判断是否存在三元组 [nums[i], nums[j], nums[k]] 满足 i != j、i != k 且 j != k ，同时还满足 nums[i] + nums[j] + nums[k] == 0 。请
你返回所有和为 0 且不重复的三元组。

```java

class Solution {  
    public List<List<Integer>> threeSum(int[] nums) {  
        //数组从小到大排序  
        Arrays.sort(nums);  
        int len = nums.length;  
  
        List<List<Integer>> res = new ArrayList<>();  
  
        for(int i = 0; i < len -2; i++){  
            //如果最小的数大于0则不可能三个数的和为0, 直接退出  
            if(nums[i] > 0){  
                break;  
            }  
  
            //如果和上一个数相同，则有重复解。  
            //即当a = d时，a + b + c = d + b + c = 0, 寻找下一个不同的数（简称：去重）  
            if(i > 0 && nums[i] == nums[i-1]){    //i > 0是因为可能i = 0时去重出现异常  
                continue;  
            }  
  
            //取另外两个数，判断三数之和是否为0  
            int j = i + 1, k = len - 1;  
            int sum;  
  
  
            while(j < k){  
                sum = nums[i] + nums[j] + nums[k];  
  
                //如果三数之和大于0，则说明最大数nums[k]太大了，k向左移，同时去重  
                if(sum > 0){  
                    while(j < k && nums[k] == nums[--k]);  
                    //如果三数之和小于0，则说明中间数nums[j]太小了，j向右移，同时去重  
                }else if(sum < 0){  
                    while(j < k && nums[j] == nums[++j]);  
                    //如果三数之和等于0, 则把三数添加到答案  
                }else{  
                    res.add(new ArrayList<Integer>(Arrays.asList(nums[i], nums[j], nums[k])));  
  
                    //nums[j]增大, nums[k]减小, 依旧可能满足三数之和为0，继续遍历去重  
                    while(j < k && nums[k] == nums[--k]);  
                    while(j < k && nums[j] == nums[++j]);  
                }  
            }  
  
        }  
        return res;  
    }  
}
```

## 15.3、合并两个有序数组

给你两个按非递减顺序排列的整数数组nums1和nums2，另有两个整数m和n ，分别示 nums1 和 nums2 中的元素数目。请你 合并 nums2 到 nums1 中，使合并后的数组同样按 非递减顺序排列。

思路：简单方法，先合并再排序。但这没有利用到两个数组已经排序的性质，可以使用双指针法

```java
class Solution {  
    public void merge(int[] nums1, int m, int[] nums2, int n) {  
        int p1 = 0, p2 = 0;  
        int[] sorted = new int[m + n];  
        int cur;  
        while (p1 < m || p2 < n) {  
            if (p1 == m) {  
                cur = nums2[p2++];  
            } else if (p2 == n) {  
                cur = nums1[p1++];  
            } else if (nums1[p1] 《 nums2[p2]) {  
                cur = nums1[p1++];  
            } else {  
                cur = nums2[p2++];  
            }  
            sorted[p1 + p2 - 1] = cur;  
        }  
        for (int i = 0; i != m + n; ++i) {  
            nums1[i] = sorted[i];  
        }  
    }  
}
```


## 15.4、环形链表（快慢指针）

给你一个链表的头节点 `head` ，判断链表中是否有环。

思路：定义两个指针，一快一慢。慢指针每次只移动一步，而快指针每次移动两步。初始时，慢指针在位置 head，而快指针在位置 head.next。这样一来，如果在移动的过程中，快指针反过来追上慢指针，就说明该链表为环形链表。否则快指针将到达链表尾部，该链表不为环形链表

```java
public class Solution {  
    public boolean hasCycle(ListNode head) {  
        if (head == null || head.next == null) {  
            return false;  
        }  
        ListNode slow = head;  
        ListNode fast = head.next;  
        while (slow != fast) {  
            if (fast == null || fast.next == null) {  
                return false;  
            }  
            slow = slow.next;  
            fast = fast.next.next;  
        }  
        return true;  
    }  
}

```


## 15.5、二分查找

给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。

定义两个指针，一快一慢。慢指针每次只移动一步，而快指针每次移动两步。初始时，慢指针在位置 head，而快指针在位置 head.next。这样一来，如果在移动的过程中，快指针反过来追上慢指针，就说明该链表为环形链表。否则快指针将到达链表尾部，该链表不为环形链表

```java
class Solution {  
    public int search(int[] nums, int target) {  
        int left = 0, right = nums.length - 1;  
        while (left <= right) {  
            int mid = (right - left) / 2 + left;  
            int num = nums[mid];  
            if (num == target) {  
                return mid;  
            } else if (num > target) {  
                right = mid - 1;  
            } else {  
                left = mid + 1;  
            }  
        }  
        return -1;  
    }  
}

```

## 15.6、爬楼梯（动态规划）

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。
每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

思路：
本问题其实常规解法可以分成多个子问题，爬第n阶楼梯的方法数量，等于 2 部分之和

1. 爬上 n−1阶楼梯的方法数量。因为再爬1阶就能到第n阶
2. 爬上 n−2阶楼梯的方法数量，因为再爬2阶就能到第n阶

所以我们得到公式 dp[n]=dp[n−1]+dp[n−2]
同时需要初始化 dp[0]=1 和 dp[1]=1
时间复杂度：O(n)

```java
class Solution {  
    public int climbStairs(int n) {  
        int[] dp = new int[n + 1];  
        dp[0] = 1;  
        dp[1] = 1;  
        for(int i = 2; i <= n; i++) {  
            dp[i] = dp[i - 1] + dp[i - 2];  
        }  
        return dp[n];  
    }  
}
```

