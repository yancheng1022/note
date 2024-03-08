


SpringBoot的主要功能都是依靠它内部很多的扩展点来完成的，今天来做个总结和记录

# 1、SpringApplicationRunListener

从命名我们就可以知道它是一个监听者，分析springboot启动流程我们会发现，它其实是用来在整个启动流程中接收不同执行点事件通知的监听者，SpringApplicationRunListener接口规定了SpringBoot的生命周期，在各个生命周期广播相应的事件，调用实际的ApplicationListener类

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
