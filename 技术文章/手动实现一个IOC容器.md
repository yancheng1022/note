
自己动手实现一个基于注解的简单IOC容器，当然由于是个人实现不会真的完全按照SpringBoot框架的设计模式，也不会考虑过多的如循环依赖、线程安全等其他复杂问题，整个实现原理很简单，扫描注解，通过反射创建出我们所需要的bean实例，再将这些bean放到集合中，对外通过IOC容器类提供一个getBean()方法，用来获取Bean实例

类/接口

说明

BeanFactory

IOC容器的基础接口，提供IOC容器的基本功能

DefaultListableBeanFactory

IOC容器的核心实现类，提供多个map集合用来存储bean的定义对象，提供getBean方法的核心实现

XmlBeanFactory

IOC容器的实现类，基于xml构建bean信息

XmlBeanDefinitionReader

用于解析xml信息，并提供解析Document文档的方法，并将解析到的BeanDefinition对象注册到核心容器中

BeanDefinition

封装Bean的定义对象，如: bean的id class,scope ..等等

Property

封装Bean所关联依赖的属性


# 1、获取我们要管理的对象


