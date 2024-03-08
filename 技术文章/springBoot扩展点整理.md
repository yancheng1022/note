


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

ApplicationListener通过监听容器中发布的一些事件，事件发生就会触发监听器的回调，就完成了事件驱动开发。spring定义了一些内置事件，当然我们也可以自定义事件

**内置事件：**
1、ContextRefreshedEvent
ApplicationContext 被初始化或刷新时，该事件被发布。此处的初始化是指：所有的Bean被成功装载，后处理Bean被检测并激活，所有Singleton Bean 被预实例化，ApplicationContext容器已就绪可用

2、ContextStartedEvent
当使用 ConfigurableApplicationContext （ApplicationContext子接口）接口中的 start() 方法启动 ApplicationContext 时，该事件被发布。你可以调查你的数据库，或者你可以在接受到这个事件后重启任何停止的应用程序

3、ContextStoppedEvent
当使用 ConfigurableApplicationContext 接口中的 stop() 停止 ApplicationContext 时，发布这个事件。你可以在接受到这个事件后做必要的清理的工作

4、ContextClosedEvent
当使用 ConfigurableApplicationContext 接口中的 close() 方法关闭 ApplicationContext 时，该事件被发布。一个已关闭的上下文到达生命周期末端；它不能被刷新或重启

5、RequestHandledEvent
这是一个 web-specific 事件，告诉所有 bean HTTP 请求已经被服务。只能应用于使用DispatcherServlet的Web应用。在使用Spring作为前端的MVC控制器时，当Spring处理用户请求结束后，系统会自动触发该事件

## 2.2、自定义事件具体使用

1、定义自己的ApplicationListener

```java
@Component
public class MyApplicationListener implements ApplicationListener<ApplicationEvent> {
    @Override
    public void onApplicationEvent(ApplicationEvent event) {
        System.out.println(" my application listener" + event);
    }
}
```

2、定义自己的事件

```java
public class MyApplicationEvent extends ApplicationEvent {
    public MyApplicationEvent(Object source) {
        super(source);
    }
}
```

3、发布事件

```java
	public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(MainConfig.class);
        context.publishEvent(new MyApplicationEvent(new String("event")) {});
    }
```