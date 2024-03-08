---
title: 各框架下的SPI机制实现原理
categories:
  - coding
date: 2022/11/21
tags:
  - 技术文章
  - SPI
---
# 1、基本概念

## 1.1、概念

SPI （ Service Provider Interface），是一种服务发现机制。SPI 的本质是将**接口的实现类**的全限定名配置在文件中，并由服务加载器读取**配置文件**，加载对应接口的实现类。这样就可以在运行时，获取接口的实现类。通过这一特性，我们可以给很容易的通过 SPI 机制为程序提供拓展功能

## 1.2、使用场景
概括地说，适用于：调用者根据实际使用需要，启用、扩展、或者替换框架的实现策略。比较常见的例子：

- 数据库驱动加载接口实现类的加载：JDBC加载不同类型数据库的驱动
- 日志门面接口实现类加载：SLF4J加载不同提供商的日志实现类
- Spring：Spring中大量使用了SPI,比如：对servlet3.0规范对ServletContainerInitializer的实现、自动类型转换Type Conversion SPI(Converter SPI、Formatter SPI)等
- Dubbo：Dubbo中也大量使用SPI的方式实现框架的扩展, 不过它对Java提供的原生SPI做了封装，允许用户扩展实现Filter接口。

# 2、java SPI

## 2.1、基本概念

Java SPI 是“基于接口的编程＋策略模式＋配置文件”组合实现的动态加载机制。设计一个接口，将接口的实现类写在配置文件中，服务通过读取配置文件来发现实现类，进行加载实例化然后使用。

配置文件路径：classpath下的META-INF/services/
配置文件名：接口的全限定名
配置文件内容：接口的实现类的全限定名


## 2.2、使用

1、定义接口和实现类

```java
// 接口
public interface Superman {
    void introduce();
}

// 实现类1
public class IronMan implements Superman{
    @Override
    public void introduce() {
        System.out.println("我是钢铁侠！");
    }
}
// 实现类2
public class CaptainAmerica implements Superman {
    @Override
    public void introduce() {
        System.out.println("我是美国队长！");
    }
}
```

2、定义配置类

classpath下的META-INF/services/ 下创建文件：com.yc.service.spi.Superman（接口的全限定名），文件内容:

```
com.yc.service.spi.IronMan
com.yc.service.spi.CaptainAmerica
```


3、使用

```java
public static void main(String[] args) {
    ServiceLoader<Superman> serviceLoader = ServiceLoader.load(Superman.class);
    System.out.println("Java SPI:");
    serviceLoader.forEach(Superman::introduce);
}
```


看到这里，就应该了解，mysql-connector 的 jar 包中正是通过 SPI 的方式实现了 java 的 java.Driver.sql 接口，所以我们的服务可以在运行时获取到 mysql 的驱动类，从而连接 mysql 


## 2.3、原理

Java SPI 的实现原理基于 Java 类加载机制和反射机制。使用 ServiceLoader.load(Class< T > service) 方法加载服务时，会检查 META-INF/services 目录下是否存在以接口全限定名命名的文件。如果存在，则读取文件内容，获取实现该接口的类的全限定名，并通过 Class.forName() 方法加载对应的类。在加载类之后，ServiceLoader 会通过反射机制创建对应类的实例，并将其缓存起来。

> 这里涉及到一个懒加载迭代器的思想：当我们调用 ServiceLoader.load(Class< T > service) 方法时，并不会立即将所有实现了该接口的类都加载进来，而是返回一个懒加载迭代器。只有在使用迭代器遍历时，才会按需加载对应的类并创建其实例

## 2.4、优缺点

**优点：**  
核心思想：**解耦**，使得第三方服务模块的装配控制的逻辑与调用者的业务代码分离。可以根据实际业务情况进行使用或扩展。  

**缺点：**  
1、获取接口的实现类的方式不灵活
serviceLoader 只能通过 Iterator 形式遍历获取，不能根据参数获取指定的某个实现类。  
2、资源浪费
serviceLoader 只能通过遍历的方式将接口的实现类全部获取、加载并实例化一遍。如果不想用某些实现类，它也被加载并实例化，造成浪费。


# 3、Spring SPI

与 JDK SPI 类似， 相对于 Java 的 SPI 的优势在于：  
Spring SPI 指定配置文件为 classpath 下的 META-INF/spring.factories，所有的拓展点配置放到一个文件中。  
配置文件内容为 key-value 类型，key 为接口的全限定名， value 为 实现类的全限定名，可以为多个。








