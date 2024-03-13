
自己动手实现一个基于注解的简单IOC容器，当然由于是个人实现不会真的完全按照SpringBoot框架的设计模式，也不会考虑过多的如循环依赖、线程安全等其他复杂问题，整个实现原理很简单，扫描注解，通过反射创建出我们所需要的bean实例，再将这些bean放到集合中，对外通过IOC容器类提供一个getBean()方法，用来获取Bean实例


| 类/接口        | 说明                      |
| :---------- | :---------------------- |
| BeanFactory | IOC容器的基础接口，提供IOC容器的基本功能 |
|             |                         |

# 1、定义注解

这四个注解的使用位置不同 但是我让他们都保存到运行时了

## 1.1、@myAutowired

自动byType注入

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface myAutowired {

}
```

## 1.2、@myComponent

标记该类让他被我写的ioc管理

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface myComponent {
String value() default "";
}
```

## 1.3、@myQualifier

是根据value去注入

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface myQualifier {
    String value();
}
```

## 1.4、@myValue

@myValue是根据value的值给实例化对象赋值

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface myValue {
    String value();
}
```


# 2、MyBeanDefinition

存放class和className

```java
public class MyBeanDefinition {
    private String BeanName;
    private Class beanClass;

    @Override
    public String toString() {
        return "MyBeanDefinition{" +
                "BeanName='" + BeanName + '\'' +
                ", beanClass=" + beanClass +
                '}';
    }

    public String getBeanName() {
        return BeanName;
    }

    public void setBeanName(String beanName) {
        BeanName = beanName;
    }

    public Class getBeanClass() {
        return beanClass;
    }

    public void setBeanClass(Class beanClass) {
        this.beanClass = beanClass;
    }

    public MyBeanDefinition(String beanName, Class beanClass) {
        BeanName = beanName;
        this.beanClass = beanClass;
    }
}
```