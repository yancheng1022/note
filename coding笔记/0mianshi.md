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

ThreadLocal即线程变量，它用于共享变量在多线程中的隔绝，即每个线程都有一个该变量的副本彼此互不影响也就不需要同步机制了
每个Thread对象都有一个ThreadLocalMap，当创建一个ThreadLocal的时候，就会将该ThreadLocal对象添加到该Map中，其中键就是ThreadLocal，值可以是任意类型。这样就实现了ThreadLocal在一个线程中是共享的，在不同线程之间是隔离的

## 5.14、ThreadLocal内存泄漏

![](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202401041020818.png)

如果在线程池中使用ThreadLocal会造成内存泄漏，因为当ThreadLocal对象使用完之后，应该要把设置的key，value，也就是Entry对象进行回收，但线程池中的线程不会回收，而线程对象是通过强引用指向ThreadLocalMap，ThreadLocalMap也是通过强引用指向Entry对象线程不被回收，Entry对象也就不会被回收，从而出现内存泄漏，解决办法是，在使用了ThreadLocal对象之后，手动调用ThreadLocal的remove方法，手动清楚Entry对象



1. **弱引用**

正是因为有引用，对象才会在内存中存在。当对象的引用数量归零后，垃圾回收程序会把对象销毁。弱引用不会增加对象的引用数量。 引用的目标对象称为所指对象（referent）。 因此我们说，弱引用不会妨碍所指对象被当作垃圾回收。

2. **ThreadLocalMap的key使用弱引用原因**

假如使用强引用，当ThreadLocal不再使用需要回收时，发现某个线程中ThreadLocalMap存在该ThreadLocal的强引用，无法回收，造成内存泄漏。因此，使用弱引用可以防止长期存在的线程（通常使用了线程池）导致ThreadLocal无法回收造成内存泄漏。

3. **通常说的ThreadLocal泄露是什么原因**

我们注意到Entry对象中，虽然Key(ThreadLocal)是通过弱引用引入的，但是value即变量值本身是通过强引用引入。
这就导致，假如不作任何处理，由于ThreadLocalMap和线程的生命周期是一致的，当线程资源长期不释放，即使ThreadLocal本身由于弱引用机制已经回收掉了，但value还是驻留在线程的ThreadLocalMap的Entry中。即存在key为null，但value却有值的无效Entry。导致内存泄漏。

4. **怎么避免内存泄露？**

1.Threadlocal自身做了一些处理，在每次调用ThreadLocal的get、set、remove方法时都会执行一个方法，该方法检测整个Entry[]表中对key为null的Entry一并擦除，重新调整索引

2.程序员自身，在代码逻辑中使用完ThreadLocal，都要调用remove方法，及时清理


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