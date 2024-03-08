


SpringBoot的主要功能都是依靠它内部很多的扩展点来完成的，今天来做个总结和记录

# 1、SpringApplicationRunListener

## 1.1、基本概念

从命名我们就可以知道它是一个监听者，分析springboot启动流程我们会发现，它其实是用来在整个启动流程中接收不同执行点事件通知的监听者，SpringApplicationRunListener接口规定了SpringBoot的生命周期，在各个生命周期广播相应的事件，调用实际的ApplicationListener类。对于开发者来说，基本没有什么常见的场景要求我们必须实现一个自定义的SpringApplicationRunListener

```java
public interface SpringApplicationRunListener {
     //刚执行run方法时
    void started();
     //环境建立好时候
    void environmentPrepared(ConfigurableEnvironment environment);
     //上下文建立好的时候
    void contextPrepared(ConfigurableApplicationContext context);
    //上下文载入配置时候
    void contextLoaded(ConfigurableApplicationContext context);
    //上下文刷新完成后，run方法执行完之前
    void finished(ConfigurableApplicationContext context, Throwable exception);
}

```

## 1.2、具体使用

1、新建类实现SpringApplicationRunListener,需要构造方法,里面两个参数SpringApplication sa, String[] args;

```java

public class MyApplicationRunListener implements SpringApplicationRunListener {

  private final SpringApplication application;
  private final String[] args;

  public MyApplicationRunListener(SpringApplication sa, String[] args) {
    this.application = sa;
    this.args = args;
  }

  @Override
  public void starting() {
    System.out.println("服务启动RunnerTest  SpringApplicationRunListener的starting方法...");
  }

  @Override
  public void environmentPrepared(ConfigurableEnvironment environment) {
    System.out.println("服务启动RunnerTest  SpringApplicationRunListener的environmentPrepared方法...");
  }

  @Override
  public void contextPrepared(ConfigurableApplicationContext context) {
    System.out.println("服务启动RunnerTest  SpringApplicationRunListener的contextPrepared方法...");
  }

  @Override
  public void contextLoaded(ConfigurableApplicationContext context) {
    System.out.println("服务启动RunnerTest  SpringApplicationRunListener的contextLoaded方法...");
  }

  @Override
  public void running(ConfigurableApplicationContext context) {
    System.out.println("服务启动RunnerTest  SpringApplicationRunListener的running方法...");
  }

  @Override
  public void failed(ConfigurableApplicationContext context, Throwable exception) {
    System.out.println("服务启动RunnerTest  SpringApplicationRunListener的failed方法...");
  }

  @Override
  public void started(ConfigurableApplicationContext context) {
    System.out.println("服务启动RunnerTest  SpringApplicationRunListener的started方法...");
  }
}
```

2、在resources下新建META-INF\spring.factories文件,文件里面将新建的实现类的类路径配置进去:

```properties
org.springframework.boot.SpringApplicationRunListener=com.study.springbootplus.config.MyApplicationRunListener
```


# 2、ApplicationListener
## 2.1、基本概念

ApplicationListener不是新东西，它属于Spring框架对Java中实现的监听者模式的一种框架实现，这里需要注意的是：对于刚接触SpringBoot，但是对于Spring框架本身又没有过多地接触的开发人员来说，可能会将这个名字与SpringApplicationRunListener弄混。

ApplicationListener通过监听容器中发布的一些事件，事件发布就会触发监听器的回调，就完成了事件驱动开发。原理是观察者设计模式，设计初衷也是为了系统业务逻辑之间的解耦，提高可扩展性以及可维护性。spring定义了一些内置事件，当然我们也可以自定义事件

**内置事件：**

| 序号  | Spring 内置事件           | 描述                                                                                                                                            |
| --- | --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | ContextRefreshedEvent | ApplicationContext 被初始化或刷新时，该事件被发布。所有的Bean被成功装载，后处理Bean被检测并激活，所有Singleton Bean 被预实例化，ApplicationContext容器已就绪可用                                |
| 2   | ContextStartedEvent   | 当使用 ConfigurableApplicationContext （ApplicationContext子接口）接口中的 start() 方法启动 ApplicationContext 时，该事件被发布。你可以调查你的数据库，或者你可以在接受到这个事件后重启任何停止的应用程序。 |
| 3   | ContextStoppedEvent   | 当使用 ConfigurableApplicationContext （ApplicationContext子接口）接口中的 start() 方法启动 ApplicationContext 时，该事件被发布。你可以调查你的数据库，或者你可以在接受到这个事件后重启任何停止的应用程序。 |
| 4   | ContextClosedEvent    | 当使用 ConfigurableApplicationContext 接口中的 close() 方法关闭 ApplicationContext 时，该事件被发布。一个已关闭的上下文到达生命周期末端；它不能被刷新或重启。                                 |
| 5   | RequestHandledEvent   | 这是一个 web-specific 事件，告诉所有 bean HTTP 请求已经被服务。只能应用于使用DispatcherServlet的Web应用。在使用Spring作为前端的MVC控制器时，当Spring处理用户请求结束后，系统会自动触发该事件。                 |
## 2.2、自定义事件具体使用

场景：下单后调用短信发送

1、定义订单事件

```java
public class OrderCreateEvent extends ApplicationEvent {
 
    private String orderInfo;//订单信息
    public OrderCreateEvent(Object source,String orderInfo){
        super(source);
        this.orderInfo = orderInfo;
    }
 
    public String getOrderInfo() {
        return orderInfo;
    }
 
    public void setOrderInfo(String orderInfo) {
        this.orderInfo = orderInfo;
    }
}
 
```


2、定义短信发送的监听器（ApplicationListener）

```java
@Component
public class SmsListener implements ApplicationListener<OrderCreateEvent> {
    @Override
    public void onApplicationEvent(OrderCreateEvent event) {
        //. 发送短信: 调用短信服务，给手机号发送短信信息.
        System.out.println("发送短信 - 调用短信服务，给手机号发送短信信息;订单信息："+event.getOrderInfo());
    }
}
 
 
```

3、发布事件

```java
@Service
public class OrderService {
    @Autowired
    private ApplicationContext applicationContext;
 
    @Autowired
    private ApplicationEventPublisher applicationEventPublisher;
 
    /**
     * 创建订单.
     */
    public void createOrder(){
        //1. 创建订单: 生成订单信息，然后保存到数据库.
        System.out.println("创建订单 - 生成订单信息，然后保存到数据库");
 
 
        //2. 发布事件
        OrderCreateEvent orderCreateEvent = new OrderCreateEvent(this,"orderNo:20230815");
        applicationEventPublisher.publishEvent(orderCreateEvent);//也可以
}
```


使用事件模式可以将代码解耦，使得代码更加灵活和可扩展。如果直接调用处理点赞的方法，那么每次需要添加新的功能或者修改现有功能时，都需要修改这个方法，这样会导致代码的耦合度很高，难以维护和扩展。

而使用事件模式，可以将点赞事件和处理点赞的方法分离开来，当有新的功能需要添加时，只需要添加一个新的事件处理器即可，不需要修改原有的代码，这样可以大大提高代码的可维护性和可扩展性。