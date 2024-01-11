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

1. 概念不同：抽象类是对根源的抽象，接口是对动作的抽象。接口表示的是：这个对象是什么（例如男人，女人的抽象类是人） 接口表示的是这个对象能做什么（吃东西）
2. 一个类只能继承一个抽象类，可以实现多个接口
3. 抽象类可以定义构造器，可以有具体方法和抽象方法。而接口不能定义构造器而且其中的方法全部是抽象方法

## 2.12、如何实现对象克隆

1. 实现Cloneable接口并重写Object类中的clone（）方法
2. 实现 Serializable 接口，通过对象的序列化和反序列化实现克隆，可以实现真正的深度克隆，代码如下

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

> 代理对象是在bean初始化后的一个BeanPostProcessor后置处理器进行判断，是否进行了aop配置，如果有，返回代理对象


```java
@Aspect
@Component
public class AuthAspect {
    /**
     * 定义了一个切点
     * 这里的路径填自定义注解的全路径
     */
    @Pointcut("@annotation(com.zz.business.annotations.Auth)")
    public void authCut() {

    }
	
    @Before("authCut()")
    public void cutProcess(JoinPoint joinPoint) {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();
        System.out.println("注解方式AOP开始拦截, 当前拦截的方法名: " + method.getName());
    }

    @After("authCut()")
    public void after(JoinPoint joinPoint) {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();
        System.out.println("注解方式AOP执行的方法 :" + method.getName() + " 执行完了");
    }


    @Around("authCut()")
    public Object testCutAround(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("注解方式AOP拦截开始进入环绕通知.......");
        Object proceed = joinPoint.proceed();
        System.out.println("准备退出环绕......");
        return proceed;
    }

    @AfterReturning(value = "authCut()", returning = "result")
    public void afterReturn(JoinPoint joinPoint, Object result) {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();
        System.out.println("注解方式AOP拦截的方法执行成功, 进入返回通知拦截, 方法名为: " + method.getName() + ", 返回结果为: " + result.toString());
    }

    @AfterThrowing(value = "authCut()", throwing = "e")
    public void afterThrow(JoinPoint joinPoint, Exception e) {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();
        System.out.println("注解方式AOP进入方法异常拦截, 方法名为: " + method.getName() + ", 异常信息为: " + e.getMessage());
    }
}
```

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202312301725231.png)


## 3.7、aop相关术语

（1）Join Point：连接点，表示一个程序在执行过程中的一个点，通常就是一个方法的执行
（2）Point Cut：切点，用来匹配一个或多个连接点
（3）Advice：通知，表示在切入点具体执行的逻辑。包括前置，后置，返回，异常，环绕通知
（4）切面：切点+通知
（5）织入：将切面应用到应用程序的过程

## 3.8、springmvc执行流程

1. 客户端发送请求到DispatcherServlet。
2. DispatcherServlet根据请求的URL找到对应的HandlerMapping。
3. HandlerMapping返回对应的HandlerExecutionChain，其中包含处理请求的Controller以及拦截器。
4. DispatcherServlet根据返回的HandlerExecutionChain取出对应的Controller，并调用其处理方法。
5. Controller处理请求，并返回一个ModelAndView对象。
6. DispatcherServlet根据返回的ModelAndView对象，调用对应的ViewResolver进行视图解析。
7. ViewResolver返回对应的View对象。DispatcherServlet将Model传递给View，View对Model进行渲染。
8. DispatcherServlet将渲染后的视图返回给客户端

## 3.9、spring bean的作用域

- singleton：单例模式，在整个Spring IoC容器中，使用singleton定义的Bean将只有一个实例
- prototype：原型模式，每次通过容器的getBean方法获取prototype定义的Bean时，都将产生一个新的Bean实例
- request：对于每次HTTP请求，使用request定义的Bean都将产生一个新实例，即每次HTTP请求将会产生不同的Bean实例。只有在Web应用中使用Spring时，该作用域才有效
- session：对于每次HTTP Session，使用session定义的Bean豆浆产生一个新实例。同样只有在Web应用中使用Spring时，该作用域才有效
- globalsession：每个全局的HTTP Session，使用session定义的Bean都将产生一个新实例。典型情况下，仅在使用portlet context的时候有效。同样只有在Web应用中使用Spring时，该作用域才有效

## 3.10、spring bean是线程安全的吗

对于原型Bean,每次创建一个新对象，也就是线程之间并不存在Bean共享，自然是不会有线程安全的问题

对于单例Bean,所有线程都共享一个单例实例Bean,因此是存在资源的竞争。

>怎样解决单例bean对象的线程安全问题
>1.在bean对象中尽量避免使用可变的成员变量
>2.在Bean对象中定义一个ThreadLocal成员变量，将需要的可变成员变量保存在ThreadLocal中

## 3.11、springBoot自动配置原理


>spring boot默认扫描启动类所在的包下的主类与子类的所有组件，但并没有包括依赖包中的bean，那么依赖包中的bean是如何被发现和加载的。我们需要从Spring Boot项目的启动类开始跟踪

在启动类上我们一般会加入SpringBootApplication注解，它是一个复合注解，它下面的@EnableAutoConfiguration这个注解，这个注解也是一个复合注解。@EnableAutoConfiguration注解导入AutoConfigurationImportSelector类，这个类中有一个selectImports()方法，扫描了所有包含META-INF/spring.factories的jar包，这个文件中列举了所有自动装备类的清单。通过`@ConditionalOn`条件注解，满足一定条件才会通过反射去创建对应的类

> 比如**MybatisAutoConfiguration**这个类，会先通过依赖条件判断@ConditionOnClass等，判断有没有SqlSessionFactory类和Datasource实例。满足条件时创建对应的需要的实例

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

阻塞队列（BlockingQueue）是一个支持两个附加操作的队列。这两个附加的操作是：在队列为空时，获取元素的线程会等待队列变为非空。当队列满时，存储元素的线程会等待队列可用。
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

> **isHeldExclusively()**：该线程是否正在独占资源。只有用到condition才需要去实现它。
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

CountDownLatch允许一个或多个线程等待其他线程完成操作。await（）用来等待计数归0，countDown（）用来让计数减少一。

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
2.top -p 2732 单独监控该进程
3.在第二步的界面输入H，获取当前进程下的线程信息
4.找到cpu占用特别高的线程id
5.执行jstack 2732对当前进程做dump，输出所有的线程信息。同时将第四步得到的线程id转为16进制，在堆栈信息里去找对应的线程内容
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

性能：性能： null > system/const > eq_ref > ref >  range > index >  all 


（1）system：system是const的特例，**「表示表中只有一行记录」**，这个几乎不会出现，也作为了解。
（2）const: const表示通过索引一次就查找到了数据，一般const出现在**唯一索引或者主键索引中使用等值查询**

```sql
explain select * from user where id =2;
```

（3）eq_ref: eq_ref表示使用唯一索引或者主键索引扫描作为表链接匹配条件，对于每一个索引键，表中只有一条记录与之匹配

```sql
explain select * from user left join role_user on user.id = role_user.user_id left join role on role_user.role_id=role.id;
```

（4）ref：ref与eq_ref的区别就是：  eq_ref使用的是唯一索引或者主键索引，ref扫描后的结果可能会找到多条符合条件的行数据，本质上是一种索引访问，返回匹配的行
```sql
explain select * from user where name = '张三'
```

（5）range：range使用索引来检索给定范围的行数据，一般是在where后面使用between、<>、in等查询语句就会出现range
（6）index：index表示会遍历索引树，index会比ALL速度快一些，但是出现index说明需要检查自己的索引是否使用正确
（7）all：ALL与index的区别就是ALL是从硬盘中读取，而index是从索引文件中读取，ALL全表扫描意味着Mysql会从表的头到尾进行扫描，这时候表示通常需要增加索引来进行优化了




（2）key：实际使用的索引，表示MySQL在执行查询时所使用的索引

（3）exart：额外信息

> （1）Using index：select操作中使用了覆盖索引(Covering Index)，避免回表查询
> （2）Using where：
> （3）Using temporary：使了用临时表保存中间结果,MySQL在对查询结果排序时使用临时表。常见于排序 order by 和分组查询 group by
> （4）Using filesort：排序时没有按照建立复合索引字段的顺序进行，因此产生了外部的索引排序。效率低
> （2）
> （4）Using join buffer：表明使用了连接缓存，给驱动表建立索引可解决此问题

## 2.2、索引分类

1、从逻辑功能上划分：主键索引、唯一索引、普通索引、全文索引

>主键索引：每个表中唯一行的索引。主键索引要求主键列中的每个值都必须唯一且不能为空值
唯一索引：列的值在全表必须是唯一的，可以为空
普通索引：查询中经常出现的列，提升查询性能
全文索引：使用like '%xxx%'进行模糊查询时，字段的索引就会失效。因此，在数据量大的情况下，通过此种方式查询的效率极低。这个时候，就可通过全文索引（Full-Text Search）来进行优化。全文索引（Full-Text Search）是将存储于数据库中的整本书或整篇文章中的任意信息查找出来的技术。它可以根据需要获得全文中有关章、节、段、句、词等信息，也可以进行各种统计和分析

从物理实现划分：聚簇索引、非聚簇索引

## 2.3、聚簇索引、辅助索引（二级索引）

innodb主键使用了聚簇索引（当然也可以对普通字段创建辅助索引）。myisam主键和普通字段都使用辅助索引

1、聚簇索引

聚簇索引就是按照每张表的主键构造一颗B+树，同时叶子节点中存放的就是整张表的行记录数据，每张表只能拥有一个聚簇索引
Innodb通过主键聚集数据，如果没有定义主键，innodb会选择非空的唯一索引代替。如果没有这样的索引，innodb会隐式的定义一个主键来作为聚簇索引

2、辅助索引

辅助索引，也叫二级索引。叶子节点只存储了索引列和主键信息。
在innodb中，想要获取完整的数据需要根据主键值从聚簇索引中检索（这里称为回表，回表查询其实就执行了两次B+树查询）
而myisam中，主键索引和辅助索引想要获取完整数据都需要从原始表中获取数据


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
