
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
}
```

# 4、工具类(MyTools)

- getClasses:根据包名获取该包下

```java
public class MyTools {

    public static Set<Class<?>> getClasses(String pack) {

        // 第一个class类的集合
        Set<Class<?>> classes = new LinkedHashSet<Class<?>>();
        // 是否循环迭代
        boolean recursive = true;
        // 获取包的名字 并进行替换
        String packageName = pack;
        String packageDirName = packageName.replace('.', '/');
        // 定义一个枚举的集合 并进行循环来处理这个目录下的things
        Enumeration<URL> dirs;
        try {
            dirs = Thread.currentThread().getContextClassLoader().getResources(packageDirName);
            // 循环迭代下去
            while (dirs.hasMoreElements()) {
                // 获取下一个元素
                URL url = dirs.nextElement();
                // 得到协议的名称
                String protocol = url.getProtocol();
                // 如果是以文件的形式保存在服务器上
                if ("file".equals(protocol)) {
                    // 获取包的物理路径
                    String filePath = URLDecoder.decode(url.getFile(), "UTF-8");
                    // 以文件的方式扫描整个包下的文件 并添加到集合中
                    findClassesInPackageByFile(packageName, filePath, recursive, classes);
                } else if ("jar".equals(protocol)) {
                    // 如果是jar包文件
                    // 定义一个JarFile
                    System.out.println("jar类型的扫描");
                    JarFile jar;
                    try {
                        // 获取jar
                        jar = ((JarURLConnection) url.openConnection()).getJarFile();
                        // 从此jar包 得到一个枚举类
                        Enumeration<JarEntry> entries = jar.entries();
                        findClassesInPackageByJar(packageName, entries, packageDirName, recursive, classes);
                    } catch (IOException e) {
                        // log.error("在扫描用户定义视图时从jar包获取文件出错");
                        e.printStackTrace();
                    }
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return classes;
    }

    private static void findClassesInPackageByJar(String packageName, Enumeration<JarEntry> entries, String packageDirName, final boolean recursive, Set<Class<?>> classes) {
        // 同样的进行循环迭代
        while (entries.hasMoreElements()) {
            // 获取jar里的一个实体 可以是目录 和一些jar包里的其他文件 如META-INF等文件
            JarEntry entry = entries.nextElement();
            String name = entry.getName();
            // 如果是以/开头的
            if (name.charAt(0) == '/') {
                // 获取后面的字符串
                name = name.substring(1);
            }
            // 如果前半部分和定义的包名相同
            if (name.startsWith(packageDirName)) {
                int idx = name.lastIndexOf('/');
                // 如果以"/"结尾 是一个包
                if (idx != -1) {
                    // 获取包名 把"/"替换成"."
                    packageName = name.substring(0, idx).replace('/', '.');
                }
                // 如果可以迭代下去 并且是一个包
                if ((idx != -1) || recursive) {
                    // 如果是一个.class文件 而且不是目录
                    if (name.endsWith(".class") && !entry.isDirectory()) {
                        // 去掉后面的".class" 获取真正的类名
                        String className = name.substring(packageName.length() + 1, name.length() - 6);
                        try {
                            // 添加到classes
                            classes.add(Class.forName(packageName + '.' + className));
                        } catch (ClassNotFoundException e) {
                            // .error("添加用户自定义视图类错误 找不到此类的.class文件");
                            e.printStackTrace();
                        }
                    }
                }
            }
        }
    }

    private static void findClassesInPackageByFile(String packageName, String packagePath, final boolean recursive, Set<Class<?>> classes) {
        // 获取此包的目录 建立一个File
        File dir = new File(packagePath);
        // 如果不存在或者 也不是目录就直接返回
        if (!dir.exists() || !dir.isDirectory()) {
            // log.warn("用户定义包名 " + packageName + " 下没有任何文件");
            return;
        }
        // 如果存在 就获取包下的所有文件 包括目录
        File[] dirfiles = dir.listFiles(new FileFilter() {
            // 自定义过滤规则 如果可以循环(包含子目录) 或则是以.class结尾的文件(编译好的java类文件)
            @Override
            public boolean accept(File file) {
                return (recursive && file.isDirectory()) || (file.getName().endsWith(".class"));
            }
        });
        // 循环所有文件
        for (File file : dirfiles) {
            // 如果是目录 则继续扫描
            if (file.isDirectory()) {
                findClassesInPackageByFile(packageName + "." + file.getName(), file.getAbsolutePath(), recursive, classes);
            } else {
                // 如果是java类文件 去掉后面的.class 只留下类名
                String className = file.getName().substring(0, file.getName().length() - 6);
                try {
                    // 添加到集合中去
                    // classes.add(Class.forName(packageName + '.' +
                    // className));
                    // 经过回复同学的提醒，这里用forName有一些不好，会触发static方法，没有使用classLoader的load干净
                    classes.add(Thread.currentThread().getContextClassLoader().loadClass(packageName + '.' + className));
                } catch (ClassNotFoundException e) {
                    // log.error("添加用户自定义视图类错误 找不到此类的.class文件");
                    e.printStackTrace();
                }
            }
        }
    }

}
```