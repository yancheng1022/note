
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
@Data
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

    public MyBeanDefinition(String beanName, Class beanClass) {
        BeanName = beanName;
        this.beanClass = beanClass;
    }
}
```

# 3、MyAnnotationConfigApplicationContext

```java
public class MyAnnotationConfigApplicationContext {

    private Map<String,Object> ioc = new HashMap<>();
    private List<String> beanNames = new ArrayList<>();

    public MyAnnotationConfigApplicationContext(String pack) {
        //遍历包，找到目标类(原材料)
        Set<BeanDefinition> beanDefinitions = findBeanDefinitions(pack);
        //根据原材料创建bean
        createObject(beanDefinitions);
        //自动装载
        autowireObject(beanDefinitions);
    }

    public void autowireObject(Set<BeanDefinition> beanDefinitions){
        Iterator<BeanDefinition> iterator = beanDefinitions.iterator();
        while (iterator.hasNext()) {
            BeanDefinition beanDefinition = iterator.next();
            Class clazz = beanDefinition.getBeanClass();
            Field[] declaredFields = clazz.getDeclaredFields();
            for (Field declaredField : declaredFields) {
                Autowired annotation = declaredField.getAnnotation(Autowired.class);
                if(annotation!=null){
                    Qualifier qualifier = declaredField.getAnnotation(Qualifier.class);
                    if(qualifier!=null){
                        //byName
                        try {
                            String beanName = qualifier.value();
                            Object bean = getBean(beanName);
                            String fieldName = declaredField.getName();
                            String methodName = "set"+fieldName.substring(0, 1).toUpperCase()+fieldName.substring(1);
                            Method method = clazz.getMethod(methodName, declaredField.getType());
                            Object object = getBean(beanDefinition.getBeanName());
                            method.invoke(object, bean);
                        } catch (NoSuchMethodException e) {
                            e.printStackTrace();
                        } catch (IllegalAccessException e) {
                            e.printStackTrace();
                        } catch (InvocationTargetException e) {
                            e.printStackTrace();
                        }
                    }else{
                        //byType
                    }
                }
            }
        }
    }

    public Object getBean(String beanName){
        return ioc.get(beanName);
    }

    public String[] getBeanDefinitionNames(){
        return beanNames.toArray(new String[0]);
    }

    public Integer getBeanDefinitionCount(){
        return beanNames.size();
    }

    public void createObject(Set<BeanDefinition> beanDefinitions){
        Iterator<BeanDefinition> iterator = beanDefinitions.iterator();
        while (iterator.hasNext()) {
            BeanDefinition beanDefinition = iterator.next();
            Class clazz = beanDefinition.getBeanClass();
            String beanName = beanDefinition.getBeanName();
            try {
                //创建的对象
                Object object = clazz.getConstructor().newInstance();
                //完成属性的赋值
                Field[] declaredFields = clazz.getDeclaredFields();
                for (Field declaredField : declaredFields) {
                    Value valueAnnotation = declaredField.getAnnotation(Value.class);
                    if(valueAnnotation!=null){
                        String value = valueAnnotation.value();
                        String fieldName = declaredField.getName();
                        String methodName = "set"+fieldName.substring(0, 1).toUpperCase()+fieldName.substring(1);
                        Method method = clazz.getMethod(methodName,declaredField.getType());
                        //完成数据类型转换
                        Object val = null;
                        switch (declaredField.getType().getName()){
                            case "java.lang.Integer":
                                val = Integer.parseInt(value);
                                break;
                            case "java.lang.String":
                                val = value;
                                break;
                            case "java.lang.Float":
                                val = Float.parseFloat(value);
                                break;
                        }
                        method.invoke(object, val);
                    }
                }
                //存入缓存
                ioc.put(beanName, object);
            } catch (InstantiationException e) {
                e.printStackTrace();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            } catch (InvocationTargetException e) {
                e.printStackTrace();
            } catch (NoSuchMethodException e) {
                e.printStackTrace();
            }
        }
    }

    public Set<BeanDefinition> findBeanDefinitions(String pack){
        //1、获取包下的所有类
        Set<Class<?>> classes = MyTools.getClasses(pack);
        Iterator<Class<?>> iterator = classes.iterator();
        Set<BeanDefinition> beanDefinitions = new HashSet<>();
        while (iterator.hasNext()) {
            //2、遍历这些类，找到添加了注解的类
            Class<?> clazz = iterator.next();
            Component componentAnnotation = clazz.getAnnotation(Component.class);
            if(componentAnnotation!=null){
                //获取Component注解的值
                String beanName = componentAnnotation.value();
                if("".equals(beanName)){
                    //获取类名首字母小写
                    String className = clazz.getName().replaceAll(clazz.getPackage().getName() + ".", "");
                    beanName = className.substring(0, 1).toLowerCase()+className.substring(1);
                }
                //3、将这些类封装成BeanDefinition，装载到集合中
                beanDefinitions.add(new BeanDefinition(beanName, clazz));
                beanNames.add(beanName);
            }
        }
        return beanDefinitions;
    }
}
```