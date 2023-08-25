
#  1、Java概述
## 1.1、java特点

1. Java 语言是面向对象的(oop) 
2. Java 语言是健壮的。Java 的强类型机制、异常处理、垃圾的自动收集等是 Java 程序健壮性的重要保证
3. Java 语言是跨平台性的。[即: 一个编译好的.class 文件可以在多个系统下运行，这种特性称为跨平台]
4. java是一种半编译，半解释型语言。 . Java-->. class是由 Javac 编译，这个过程符合编译型语言的特点。而. class-->对应平台机器码 的这一过程 是由Java解释执行 

# 2、变量
## 2.1、java基本数据类型
java基本数据类型 - 四类八种

1. **整型**

byte 、short 、int 、long

2. **浮点型**

float 、 double

3. **字符型**

char

4. **布尔型**

boolean

| 数据类型名称 | 占用字节 | 范围 | 封装器类 |
| --- | --- | --- | --- |
| byte（字节型） | 1 | -（2^7）~2^7-1（-128~127） | Byte |
| shot（短整型） | 2 | -（2^15）~2^15-1(-32768~32767) | Short |
| int（整形） | 4 | -（2^31）~2^31-1（-2147483648~2147483647） | Integer |
| long（长整型） | 8 | -（2^63）~2^63-1 | Long |
| float（浮点型） | 4 | -3.403E8~3.403E38 | Float |
| double（双精度浮点型） | 8 | -1.798E308~1.798E308 | Double |
| boolean（布尔型） | 1 |  | Boolean |
| char（字符型） | 2 |  | **Character** |

> 注意：计算机的数值是用二进制补码表示的，二进制补码表示法中，最高位是用来表示符号的（0 表示正数，1 表示负数），其余位表示数值部分。所以，如果我们要表示最大的正数，我们需要把除了最高位之外的所有位都设为 1


## 2.2、基本数据类型转换
### 2.2.1、自动类型转换

当java程序在进行赋值或运算时，精度小的类型自动转换为精度大的数据类型，这个就是自动类型转换
精度由小到大排序：

byte short int long float double

```java
//自动类型转换细节
public class AutoConvertDetail {
    public static void main(String[] args) {
    //细节 1： 有多种类型的数据混合运算时，系统首先自动将所有数据转换成容量最大的那种数据类型，然后再进行计算
    int n1 = 10; //ok
    float d1 = n1 + 1.1;//错误 n1 + 1.1 => 结果类型是 double
    double d1 = n1 + 1.1;//对 n1 + 1.1 => 结果类型是 double
    float d1 = n1 + 1.1F;//对 n1 + 1.1 => 结果类型是 float
    //细节 2: 当我们把精度(容量)大 的数据类型赋值给精度(容量)小 的数据类型时，就会报错，反之就会进行自动类型转换。
    int n2 = 1.1;//错误 double -> int
    //细节 3: (byte, short) 和 char 之间不会相互自动转换
     byte b1 = 10; //对 , -128-127
     char c1 = b1; //错误， 原因 byte 不能自动转成 char
    //细节 4：自动提升原则： 表达式结果的类型自动提升为 操作数中最大的类型
    byte b4 = 1;
    short s3 = 100;
    int num200 = 1;
    float num300 = 1.1F;
    float num500 = b4 + s3 + num200 + num300; 
}
```
### 2.2.2、强制类型转换

将容量大的数据类型转换为容量小的数据类型。使用时要加上强制转换符( )，但可能造成精度降低或溢出,格外要注意。

### 2.2.3、基本数据类型和String转换

1. 基本数据类型转String类型

（1）将基本类型的值加 "" 即可
（2）String.valueOf(基本数据类型)

2. String转基本数据类型

（1）基本类型包装类调用parseXX方法即可

# 3、运算符
## 4.1、算数运算符
算数运算符是对数值变量进行运算的，在java程序种使用非常多

![java算数运算符](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202308231601761.png)


```java
// 题目1
int i = 1;
i = i++; // 规则会使用临时变量(1)temp=i(2)i=i+1(3)i=temp
System.out.println(i); // 1

// 题目2
int i = 1;
i = ++i; // 规则会使用临时变量(1)i=i+1(2)temp=i(3)i=temp
System.out.println(i); // 2

```
## 4.2、关系运算符

关系运算符的结果都是boolean型，也就是要么是true，要么是false

![java关系运算符](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202308231605071.png)


## 4.3、逻辑运算符

用于连接多个条件（多个关系表达式），最终的结果也是一个 boolean 值

![逻辑运算符](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202308231608353.png)


>   a^b: 叫逻辑异或  ：当 a 和 b 不同时，则结果为 true, 否则为 false  
>   &&短路与：如果第一个条件为 false，则第二个条件不会判断，最终结果为 false，效率高
>   & 逻辑与：不管第一个条件是否为 false，第二个条件都要判断，效率低  

## 4.4、赋值运算符

基本赋值运算符 = （ int a = 10;） 
复合赋值运算符 += ，-= ，*= ， /= ，%= 等

## 4.5、三元运算符
条件表达式 ? 表达式 1: 表达式 2; 

1. 如果条件表达式为 true，运算后的结果是表达式 1；
2. 如果条件表达式为 false，运算后的结果是表达式 2；

## 4.6、进制转换（为位运算准备）

对于整数，有四种表示方式：
二进制：0,1 ，满 2 进 1.以 0b 或 0B 开头。
十进制：0-9 ，满 10 进 1。
八进制：0-7 ，满 8 进 1. 以数字 0 开头表示。
十六进制：0-9 及 A(10)-F(15)，满 16 进 1. 以 0x 或 0X 开头表示。此处的 A-F 不区分大小写

![进制转换](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202308231612057.png)


![image.png](https://cdn.nlark.com/yuque/0/2022/png/2996398/1659315826387-ed7e7bab-8366-4fd2-8353-b82acde62fa2.png#clientId=u6ef58dc1-b2c2-4&from=paste&height=243&id=uc4b6c469&originHeight=470&originWidth=664&originalType=binary&ratio=1&rotation=0&showTitle=false&size=126331&status=done&style=none&taskId=u69461551-0d4d-415e-bdd3-fb02621b29f&title=&width=343)

### 4.6.1、其它进制转十进制

1. 二进制转十进制

规则：从最低位（右边）开始，将每个位上的数提取出来，乘以2的（位数-1）次方，然后求和

> 案例：将ob1011转成十进制数
> 1+2+8=11


2. 八进制转十进制

规则：从最低位（右边）开始，将每个位上的数提取出来，乘以8的（位数-1）次方，然后求和

> 案例：0234转成十进制
> 4\*1+3\*8+2\*64=156


3. 十六进制转十进制

规则：从最低位（右边）开始，将每个位上的数提取出来，乘以16的（位数-1）次方，然后求和

> 案例：0x23A
> 10\*1+3\*16+2\*512=570


### 4.6.2、十进制转其他进制

1. 十进制转二进制

规则：将该数不断除以2.直到商为0为止，然后将每步余数倒过来，就是对应二进制

> 案例：34转为对应二进制 = 0B00100010

![十进制转二进制](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202308231618617.png)


2. 十进制转八进制

规则：将该数不断除以8.直到商为0为止，然后将每步余数倒过来，就是对应二进制

> 案例：131转为8进制

![十进制转八进制](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202308231620590.png)



### 4.6.3、二进制转其它进制

1. 二进制转八进制

规则：从低位开始，将二进制数每三位一组，转成对应的八进制

> 案例：0b11010101转成八进制
> 0325


2. 二进制转十六进制

规则：从低位开始，将二进制数每四位一组，转成对应的十六进制数即可

> 案例：0b11010101转成十六进制
> D5

### 4.6.4、其它进制转二进制

1. 八进制转二进制

规则：将八进制数每1位。转成对应的一个3位的二进制数即可

> 案例：将0237转成二进制
> 0b10011111

2. 十六进制转二进制

规则：将十六进制数每1位。转成对应的一个4位的二进制数即可

> 案例：请将0x23B转成二进制
> 0b001000111011

## 4.7、位运算符
### 4.6.1、原码反码补码

1. 进制的最高位是符号位: 0表示正数1表示负数
2. 正数的原码，反码，补码都一样
3. 负数的反码=它的原码符号位不变，其它位取反.负数的补码=它的反码+1
4. 0的反码，补码都是0
5. 在计算机运算的时候，都是以补码的方式来运算的
6. 补码的补码就是原码

> 为什么计算机通过补码进行运算？
> 通过使用补码，无需单独的符号位判断来执行加法和减法运算。计算机可以直接对补码进行位运算，而不需要额外的逻辑电路来处理符号位

### 4.6.2、位运算

java种有7个位运算 &（按位与）,|（按位或）,^（按位异或）,~（按位取反），<<（左移），>>（带符号右移），>>>（无符号右移）

#### 4.6.2.1、& （按位与）

两位全为1，结果为1，否则为0

```asciidoc
  10  (2)
& 11  (3)
------
  10  (2)
```

#### 4.6.2.2、|（按位或）

两位一个为1，结果为1，否则为0

```asciidoc
  10  (2)
| 11  (3)
------
  11  (3)
```

#### 4.6.2.3、 ^（按位异或）

两位一个为0，结果为1，否则为0

```asciidoc
  10  (2)
^ 11  (3)
------
  01  (1)
```


#### 4.6.2.4、~（按位取反）

0->1, 1->0

```asciidoc
~2 
原码：00000010
按位取反：11111101
补码表示：10000011 = 3
```

#### 4.6.2.5、>>（算数右移）

低位溢出，符号位不变，并用符号位补溢出的高位

```asciidoc
1 >> 2
1的补码：00000001
右移两位，并用符号位填充：00000000
原码结果为：0

-1 >> 2
-1的补码：11111111
右移两位，并用符号位填充：11111111
原码结果为：100000001 = -1
```

#### 4.6.2.6、>> （算数左移）

符号位不变，低位补0

```asciidoc
1 << 2
0001 << 2 = 0100 = 4

-1 << 3
11111111 << 3 = 11111000
原码结果为：10001000 = -8
```

#### 4.6.2.7、>>> （逻辑右移 或叫 无符号右移）

低位溢出，高位补0

```asciidoc
8 >>> 2
1000 >>> 2 = 0010 = 2
```

# 4、面向对象
## 4.1、面向对象三大特征
### 4.1.1、封装

封装就是将数据或函数等集合在一个类中。向外暴露访问数据的方法get/set
### 4.1.2、继承

继承就是由已经存在的类创建新类的机制、在已经存在的类的基础上扩展功能，提高代码复用率
### 4.1.3、多态

多态允许我们使用一个父类类型的引用来引用子类对象，并根据实际对象的类型来执行相应的方法。简单来说，多态性允许不同类型的对象对同一个方法做出不同的响应

## 4.2、内部类

如果定义类在局部位置(方法中/代码块) :(1) 局部内部类 (2) 匿名内部类 
如果定义在成员位置 (1) 成员内部类 (2) 静态内部类  
### 4.2.1、局部内部类

局部内部类是定义在外部类的局部位置，比如方法中，并且有类名

> 说明：
> 1. 可以直接访问外部类的所有成员，包括私有
> 2. 不能添加访问修饰符，因为他就是一个局部变量，局部变量不能使用修饰符
> 3. 作用域：仅在定义它的方法或代码块中
> 4. 局部内部类访问外部类的成员：直接访问。外部类访问局部内部类的成员，创建对象再访问

```java
public class OuterClass {
    private int outerData = 10;

    public void outerMethod() {
        int localVar = 5;
        class LocalInnerClass {
            public void innerMethod() {
                System.out.println("访问外部类数据：" + outerData);
                System.out.println("访问局部变量：" + localVar);
            }
        }
        LocalInnerClass innerObj = new LocalInnerClass();
        innerObj.innerMethod();
    }

    public static void main(String[] args) {
        OuterClass outerObj = new OuterClass();
        outerObj.outerMethod();
    }
}
```
### 4.2.2、匿名内部类

匿名内部类是一种没有显式定义类名的内部类，定义在外部类的局部位置，比如方法中，它通常用于创建实现某个接口或继承某个类的匿名对象

> 说明：
> 1. 匿名内部类运行类型：外部类名称$1


1. 基于接口的匿名内部类

```java
interface IA{
    void cry();
}

class Outer {
    // 编译类型为IA，运行类型为匿名内部类Outer$1
    // jdk 底层在创建匿名内部类 Outer$1,立即马上就创建了 Outer$1 实例，并且把地址返回给 tiger
    IA tiger = new IA() {
        @Override public void cry() { 
            System.out.println("老虎叫..."); 
        } 
    };
}
```

2. 基于类的匿名内部类

```java
class Father {
    public void test(){}
}

class Outer{
    // father 编译类型 Father, father 运行类型 Outer$1
    Father father = new Father("jack"){
        @Override
        public void test() {
            System.out.println("匿名内部类重写了 test 方法");
        }
    };
}
```


### 4.2.3、成员内部类

成员内部类是定义在外部类的成员位置，并且没有static修饰

> 说明：
> 1. 可以直接访问外部类的所有成员，包括私有的
> 2. 可以添加任何修饰符，因为它的地位就是一个成员

```java
public static void main(String[] args) {
    Outer08.Inner08 inner08Instance = outer08.getInner08Instance();
    inner08Instance.say();
}

class Outer08 { //外部类
    public class Inner08 {//成员内部类
        public void say() {
            //可以直接访问外部类的所有成员，包含私有的
            //如果成员内部类的成员和外部类的成员重名，会遵守就近原则. 
            //可以通过 外部类名.this.属性 来访问外部类的成员
            System.out.println("n1 = " + n1 + " name = " + name + " 外部类的 n1=" + Outer08.this.n1);
            hi();
        }
    }    
}
```
### 4.2.4、静态内部类

成员内部类是定义再外部类的成员位置，有static修饰

```java
public static void main(String[] args) {
    Outer10.Inner10 inner10 = new Outer10.Inner10();
    inner10.say();
}
class Outer10 { //外部类
    static class Inner10 {
        private static String name = "卡卡";
        public void say() {
            cry();
        }
    }
}
```

# 5、枚举
## 5.1、实现方式
 枚举是一组常量的集合。 可以这样理解：枚举属于一种特殊的类，里面只包含一组有限的特定的对象  
### 5.1.1、 自定义类实现枚举  

1. 不需提供set方法，因为枚举对象值通常为只读
2. 对枚举对象/属性使用final+static共同修饰，实现底层优化
3. 枚举 对象名通常使用全部大写，常量的命名规范

```java
class Season {//类
    private String name;
    private String desc;//描述

    public static final Season SPRING = new Season("春天", "温暖");
    public static final Season WINTER = new Season("冬天", "寒冷");
    public static final Season AUTUMN = new Season("秋天", "凉爽");
    public static final Season SUMMER = new Season("夏天", "炎热");
}

public class Enumeration02 {
    public static void main(String[] args) {
        System.out.println(Season.AUTUMN);
        System.out.println(Season.SPRING);
    }
}

```
### 5.1.2、enum关键字实现枚举

1. 当我们使用 enum 关键字开发一个枚举类时，默认会继承 Enum 类,  这样我们就可以使用 Enum 类相关的方法  
2. 如果使用无参构造器 创建 枚举对象，则实参列表和小括号都可以省略
3. 当有多个枚举对象时，使用,间隔，最后有一个分号结尾
4. 枚举对象必须放在枚举类的行首  

```java
public class Enumeration03 {
    public static void main(String[] args) {
        System.out.println(Season2.AUTUMN);
        System.out.println(Season2.SUMMER);
    }
}

//演示使用 enum 关键字来实现枚举类
enum Season2 {//类

    //3. 如果有多个常量(对象)， 使用 ,号间隔即可
    //4. 如果使用 enum 来实现枚举，要求将定义常量对象，写在前面
    //5. 如果我们使用的是无参构造器，创建常量对象，则可以省略 ()
    SPRING("春天", "温暖"), 
    WINTER("冬天", "寒冷"), 
    AUTUMN("秋天", "凉爽"), 
    SUMMER("夏天", "炎热");
    
    private String name;
    private String desc;//描述
    
    private Season2() {//无参构造器
    }
    private Season2(String name, String desc) {//有参构造器
        this.name = name;
        this.desc = desc;
    }
}
```
# 6、注解
 	注解(Annotation)也被称为元数据(Metadata)，用于修饰解释 包、类、方法、属性、构造器、局部变量等数据信息  。
 和注释一样，注解不影响程序逻辑，但注解可以被编译或运行，相当于嵌入在代码中的补充信息  
## 6.1、基本注解介绍
 使用 Annotation 时要在其前面增加 @ 符号, 并把该 Annotation 当成一个修饰符使用。用于修饰它支持的程序元素 三个基本的 Annotation:  
1) @Override: 限定某个方法，是重写父类方法, 该注解只能用于方法 
2) @Deprecated: 用于表示某个程序元素(类, 方法等)已过时 
3) @SuppressWarnings: 抑制编译器警告（当不希望看到某些警告使用该注解，都不想看到使用all）
![image.png](https://cdn.nlark.com/yuque/0/2022/png/2996398/1659515504574-a42ed829-be25-4e67-8a35-d965b0e01490.png#clientId=u6ef58dc1-b2c2-4&from=paste&height=242&id=uc603ddec&originHeight=453&originWidth=943&originalType=binary&ratio=1&rotation=0&showTitle=false&size=192254&status=done&style=none&taskId=u41eb6d47-7557-4ebf-a8ba-131476b75a3&title=&width=503)

## 6.2、自定义注解
修饰符：访问修饰符必须为public,不写默认为pubic；
关键字：关键字为@interface；
注解名称： 注解名称为自定义注解的名称，使用时还会用到；
注解类型元素：注解属性 ( 接口方法 ) 返回值类型要求 ,可以使用default来进行赋默认值；
```java
public @interface Info { 
    String value() default "tracy"; 
    boolean isDelete(); 
}
```

## 6.3、元注解
所谓元注解，其主要作用就是负责注解其他注解，为其他注解提供了相关的解释说明。Java中存在五个元注解，分别是 @Target、@Retention、@Documented、@Inherited、@Repeatable

**Target**：描述了注解修饰的对象范围，取值在java.lang.annotation.ElementType定义，常用包括：
> METHOD：用于描述方法
> PACKAGE：用于描述包
> PARAMETER：用于描述方法变量
> TYPE：用于描述类、接口或enum类型


**Retention**：表述注解保留时间的长短，取值在java.lang.annotation.RetentionPolicy中，取值：
> SOURCE：在源文件中有效，编译过程中会被忽略
> CLASS：随源文件一起编译在class文件中，运行时忽略
> RUNTIME：在运行时有效(只有定义为RetentionPolicy.RUNTIME时，我们才能通过注解反射获取注解)


**Document**：表明这个注解应该被 javadoc文档注释工具记录，正常情况下javadoc中不包含注解的，@Documented属于标志注解

**Inherited：**被该注解注解的元注解注解某个类时，子类继承该注解
## 6.4、案例-反射获取注解

1. 定义一个注解
```java
@Target(ElementType.FIELD)//只能定义在字段上
@Retention(RetentionPolicy.RUNTIME)//在运行时有效
public @interface MyAnnotation {
    //字段描述
    String description();
    //字段长度
    int length();
}
```

2. 注解的使用
```java
@Data
public class User {
    @MyAnnotation(length = 12,description = "用户名的长度只能为12位")
    private String userName;
}
```

3. 通过反射获取注解
```java
public void test1(){
    User user = new User();
    user.setUserName("张三");

    //获取类模板
    Class<User> userClass = User.class;

    //获取所有字段
    Field[] declaredFields = userClass.getDeclaredFields();
    for (Field field : declaredFields) {
        //判断该字段是否存在MyAnnotation注解
        if(field.isAnnotationPresent(MyAnnotation.class)){
            MyAnnotation myAnnotation = field.getAnnotation(MyAnnotation.class);
            System.out.println("字段:["+field.getName()+"],描述:["+myAnnotation.description()+"],长度:["+myAnnotation.length()+"]");
            }
        }
    }

```
# 7、异常
![image.png](https://cdn.nlark.com/yuque/0/2022/png/2996398/1659576643717-f9446fd6-5496-49e6-83d6-f5af0b5c6ad2.png#clientId=u6ef58dc1-b2c2-4&from=paste&height=264&id=u875445ff&originHeight=264&originWidth=683&originalType=binary&ratio=1&rotation=0&showTitle=false&size=55624&status=done&style=none&taskId=uda759f0d-e75d-44d5-ade6-64678af7dcf&title=&width=683)

1. **Error（错误）**

程序本身不能处理的异常，只能靠外接干预 （常见的如**内存溢出**，j**vm虚拟机自身的非正常运行**等）

2. **Exception（异常）**

是程序正常运行中，可以预料的意外情况。比如数据库连接中断，空指针，数组下标越界。异常出现可以导致程序非正常终止

（1）编译时异常
又叫可检查异常，Java语言强制要求捕获和处理所有非运行时异常。通过行为规范，强化程序的健壮性和安全性

（2）运行时异常
又叫不检查异常RuntimeException，这些异常一般是由程序逻辑错误引起的，即语义错

# 8、常用类
## 8.1、日期类
### 8.1.1、第一代日期类Date（JDK1.0）

1. Date：精确到毫秒，代表特定瞬间
2. SimpleDateFormat：格式化（日期-文本）和解析日期（文本-日期）的类
```java

SimpleDateFormat sdf = new SimpleDateFormat("yyyy 年 MM 月 dd 日 hh:mm:ss E");
// format：将日期转换成指定格式的字符串
Date d1 = new Date();
String format = sdf.format(d1); 
// parse：格式化的String 转成对应的 Date
String s = "1996 年 01 月 01 日 10:20:30 星期一";
Date parse = sdf.parse(s);
```

### 8.1.2、第二代日期类Calendar（JDK1.1）
Calendar它是一种抽象类，相比Date它在操作日历的时候提供了一些方法来操作日历字段
```java
// Calendar 是一个抽象类， 并且构造器是 private,可以通过 getInstance() 来获取实例
// Calendar 没有提供对应的格式化的类，因此需要程序员自己组合来输出(灵活)

//1. 创建日历类对象
Calendar c = Calendar.getInstance(); 
//2. 获取日历对象的某个日历字段
System.out.println("年：" + c.get(Calendar.YEAR));
// 这里为什么要 + 1, 因为 Calendar 返回月时候，是按照 0 开始编号
System.out.println("月：" + (c.get(Calendar.MONTH) + 1));
System.out.println("日：" + c.get(Calendar.DAY_OF_MONTH));
System.out.println("小时：" + c.get(Calendar.HOUR));
System.out.println("分钟：" + c.get(Calendar.MINUTE));
System.out.println("秒：" + c.get(Calendar.SECOND));
```

### 8.1.3、第三代日期类LocalDate（JDK8）

1. **Calendar日期类存在的问题？**

（1）可变性，像日期和时间这样的类应该是不可变的
（2）格式化，格式化只对Date有用，Calendar只能自己拼装
（3）不是线程安全的；不能处理闰秒（每隔两天，多出一秒）等

2. **基本概念**

LocalDate：日期（年月日）
LocalTime：包含时间（时分秒）
LocalDateTime：包含日期+时间（年月日时分秒）

```java
//1. 使用 now() 返回表示当前日期时间的 对象
LocalDateTime ldt = LocalDateTime.now(); //LocalDate.now();//LocalTime.now()
//2. 使用 DateTimeFormatter 对象来进行格式化
DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
String format = dateTimeFormatter.format(ldt);
System.out.println("格式化的日期=" + format);
System.out.println("年=" + ldt.getYear());
System.out.println("月=" + ldt.getMonth());
System.out.println("月=" + ldt.getMonthValue());
System.out.println("日=" + ldt.getDayOfMonth());
System.out.println("时=" + ldt.getHour());
System.out.println("分=" + ldt.getMinute());
System.out.println("秒=" + ldt.getSecond());
LocalDate now = LocalDate.now(); //可以获取年月日
LocalTime now2 = LocalTime.now();//获取到时分秒
// 3. 提供 plus 和 minus 方法可以对当前时间进行加或者减
//看看 890 天后，是什么时候 把 年月日-时分秒
LocalDateTime localDateTime = ldt.plusDays(890);
System.out.println("890 天后=" + dateTimeFormatter.format(localDateTime));
//看看在 3456 分钟前是什么时候，把 年月日-时分秒
LocalDateTime localDateTime2 = ldt.minusMinutes(3456);
System.out.println("3456 分钟前 日期=" + dateTimeFormatter.format(localDateTime2));
```

# 9、泛型
## 9.1、基本概念

1. 泛型又称参数化类型，是JDK5出现的新特性，泛型的本质是参数化类型，即给类型指定一个参数，然后在使用时再指定此参数具体的值，那样这个类型就可以在使用时决定了。这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法

2. 为什么使用泛型？

（1）保证类型安全。在没有泛型之前，从集合中读取到的每一个对象都必须进行类型转换，如果不小心插入了错误的类型对象，在运行时的转换处理就会出错
（2）消除强制转换。消除源代码中的许多强制类型转换，这使得代码更加可读，并且减少了出错机会

## 9.2、使用泛型
泛型有三种使用方式，分别为：泛型类、泛型接口和泛型方法。

1. **泛型类**
>  注意事项：
> （1）泛型类型必须是引用类型（非基本数据类型）
> （2）参数类型规范
> T：任意类型 type
> E：集合中元素的类型 element
> K：key-value形式 key
> V：key-value形式 value


```java
public class GenericClass<T> {
 
    private T value;
 
    public GenericClass(T value) {
 
        this.value = value;
    }
 
    public T getValue() {
 
        return value;
 
    }
 
    public void setValue(T value) {
 
        this.value = value;
 
    }
 
}
```

2. **泛型接口**
3. **泛型方法**
> 泛型方法，是在调用方法的时候指明泛型的具体类型

```java

    public <T> T genercMethod(T t){
 
        System.out.println(t.getClass());
 
        System.out.println(t);
 
        return t;
 
    }

```

3. **泛型通配符**

Java泛型的通配符是用于解决泛型之间引用传递问题的特殊语法, 主要有以下三类:
![image.png](https://cdn.nlark.com/yuque/0/2022/png/2996398/1659602087157-c470c9ab-b41e-43ca-8ef7-84df415862b2.png#clientId=u6ef58dc1-b2c2-4&from=paste&height=232&id=u5f915943&originHeight=351&originWidth=891&originalType=url&ratio=1&rotation=0&showTitle=false&size=34289&status=done&style=none&taskId=u2fa77ea9-b20d-4d76-9120-74d91bdedea&title=&width=588)
```java
// 1：表示类型参数可以是任何类型
 
public class Apple<?>{} 
 
// 2：表示类型参数必须是A或者是A的子类
 
public class Apple<T extends A>{} 
 
// 3: 表示类型参数必须是A或者是A的超类型
 
public class Apple<T supers A>{}
```

（1）无边界的通配符(Unbounded Wildcards), 就是, 比如List。无边界的通配符的主要作用就是让泛型能够接受未知类型的数据. 
（2） 固定上边界的通配符(Upper Bounded Wildcards)，采用<? extends E>的形式 .使用固定上边界的通配符的泛型, 就能够接受**指定类及其子类类型**的数据。要声明使用该类通配符, 采用<? extends E>的形式, 这里的E就是该泛型的上边界。
> 注意: 这里虽然用的是extends关键字, 却不仅限于继承了父类E的子类, 也可以代指显现了接口E的类

（3）固定下边界的通配符(Lower Bounded Wildcards)，采用<? super E>的形式.使用固定下边界的通配符的泛型, 就能够接受**指定类及其父类类型的数据.**。要声明使用该类通配符, 采用<? super E>的形式, 这里的E就是该泛型的下边界.。
> 注意: 你可以为一个泛型指定上边界或下边界, 但是不能同时指定上下边界。

## 9.3、实现原理
原码：
```java
public class Caculate<T> {
    private T num;
}
```
编译后的代码：
```java
public class Caculate{
    public Caculate(){}
    private Object num;
}
```
java的泛型称为伪泛型，因为Java的泛型只是编译期的泛型，一旦编译成字节码，泛型就被擦除了，即在Java中使用泛型，我们无法在运行期知道泛型的类型，一旦编译成字节码，泛型将被取代为Object。
在不使用泛型时，我们需要将Object手动转型成特定类型，而在使用泛型后，我们不需要自己转型，但实际上我们get到的对象仍然是Object类型的，只不过编译器会自动帮我们加入这个转型动作

# 10、IO
![image.png](https://cdn.nlark.com/yuque/0/2022/png/2996398/1659660621535-86633e46-8b7a-420d-b439-ca1960c52f75.png#clientId=u6ef58dc1-b2c2-4&from=paste&height=378&id=u4631b452&originHeight=447&originWidth=635&originalType=binary&ratio=1&rotation=0&showTitle=false&size=168066&status=done&style=none&taskId=u05e1f801-4e70-4b5e-9068-c7a7cdf79e8&title=&width=537)
## 10.1、文件
### 10.1.1、文件流
文件在程序中是以流的形式操作的
![image.png](https://cdn.nlark.com/yuque/0/2022/png/2996398/1659660759738-6f451a84-db18-4ca4-889d-8b75b4e8352f.png#clientId=u6ef58dc1-b2c2-4&from=paste&height=121&id=u21d5ad8d&originHeight=160&originWidth=711&originalType=binary&ratio=1&rotation=0&showTitle=false&size=115341&status=done&style=none&taskId=u69d7bf57-e3ee-406d-a98a-9a7c037923f&title=&width=537)
流：数据在数据源（文件）和程序（内存）之间经历的路径
输入流：数据从数据源（文件）到程序（内存）的路径
输出流：数据从程序（内存）到数据源（文件）的路径

### 10.1.2、常用的文件操作

1. **文件的创建**

![image.png](https://cdn.nlark.com/yuque/0/2022/png/2996398/1659661321097-ad59daa0-a683-4d4c-b623-37144997c982.png#clientId=u6ef58dc1-b2c2-4&from=paste&height=130&id=ub8611803&originHeight=130&originWidth=1001&originalType=binary&ratio=1&rotation=0&showTitle=false&size=268930&status=done&style=none&taskId=ue0880e4b-8338-4a5e-a7b0-eb576813371&title=&width=1001)
```java
public class FileCreate {

    //方式1 new File(String pathname)
    @Test
    public void create01() {
        String filePath = "e:\\news1.txt";
        File file = new File(filePath);

        try {
            file.createNewFile();
            System.out.println("文件创建成功");
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
    
    //方式2 new File(File parent,String child) //根据父目录文件+子路径构建
    @Test
    public  void create02() {
        File parentFile = new File("e:\\");
        String fileName = "news2.txt";
        //这里的file对象，在java程序中，只是一个对象
        //只有执行了createNewFile 方法，才会真正的，在磁盘创建该文件
        File file = new File(parentFile, fileName);

        try {
            file.createNewFile();
            System.out.println("创建成功~");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //方式3 new File(String parent,String child) //根据父目录+子路径构建
    @Test
    public void create03() {
        String parentPath = "e:\\";
        String fileName = "news4.txt";
        File file = new File(parentPath, fileName);

        try {
            file.createNewFile();
            System.out.println("创建成功~");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

2. **获取文件的相关信息**
```java
public class FileInformation {
    //获取文件的信息
    @Test
    public void info() {
        //先创建文件对象
        File file = new File("e:\\news1.txt");

        //调用相应的方法，得到对应信息
        System.out.println("文件名字=" + file.getName());
        //getName、getAbsolutePath、getParent、length、exists、isFile、isDirectory
        System.out.println("文件绝对路径=" + file.getAbsolutePath());
        System.out.println("文件父级目录=" + file.getParent());
        System.out.println("文件大小(字节)=" + file.length());
        System.out.println("文件是否存在=" + file.exists());//T
        System.out.println("是不是一个文件=" + file.isFile());//T
        System.out.println("是不是一个目录=" + file.isDirectory());//F
    }
}

```

3. **目录的操作和文件删除**
```java
public class Directory_ {
    //判断 d:\\news1.txt 是否存在，如果存在就删除
    @Test
    public void m1() {
        String filePath = "e:\\news1.txt";
        File file = new File(filePath);
        if (file.exists()) {
            if (file.delete()) {
                System.out.println(filePath + "删除成功");
            } else {
                System.out.println(filePath + "删除失败");
            }
        } else {
            System.out.println("该文件不存在...");
        }
    }
}

```
## 10.2、IO流
### 10.2.1、IO流分类
按照操作数据单位不同分为：字节流（8bit）二进制文件，字符流（按字符）文本文件
按数据流的流向不同分为：输入流，输出流
按流的角色不同分为：节点流，处理流/包装流
![image.png](https://cdn.nlark.com/yuque/0/2022/png/2996398/1659662289021-93129478-8cc1-4d9c-82c9-932c15153a37.png#clientId=u6ef58dc1-b2c2-4&from=paste&height=114&id=uf89be968&originHeight=167&originWidth=829&originalType=binary&ratio=1&rotation=0&showTitle=false&size=154323&status=done&style=none&taskId=u5d128cf6-4c3f-4321-a117-49823508580&title=&width=567)
> java的IO流共涉及40多个类，都是从如上4个抽象基类派生


### 10.2.2、IO流体系图
![image.png](https://cdn.nlark.com/yuque/0/2022/png/2996398/1659662565603-29a1ed7c-3038-44ed-9b16-e3c85c413121.png#clientId=u6ef58dc1-b2c2-4&from=paste&height=514&id=ua0092696&originHeight=625&originWidth=638&originalType=binary&ratio=1&rotation=0&showTitle=false&size=217918&status=done&style=none&taskId=u6b5c9b67-8c4c-4d95-96de-c7a847f2fe0&title=&width=525)
#### 10.2.2.1、FileInputStream
![](https://cdn.nlark.com/yuque/0/2022/png/393192/1652318775076-92675274-7e99-49aa-8e4c-ffbab8e01525.png#from=url&height=253&id=FYWpL&originHeight=370&originWidth=722&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=&width=494)
```java
public class FileInputStream_ {

    /**
     * 演示读取文件...
     * 单个字节的读取，效率比较低
     * -> 使用 read(byte[] b)
     */
    @Test
    public void readFile01() {
        String filePath = "e:\\hello.txt";
        int readData = 0;
        FileInputStream fileInputStream = null;
        try {
            //创建 FileInputStream 对象，用于读取 文件
            fileInputStream = new FileInputStream(filePath);
            //从该输入流读取一个字节的数据。 如果没有输入可用，此方法将阻止。
            //如果返回-1 , 表示读取完毕
            while ((readData = fileInputStream.read()) != -1) {
                System.out.print((char)readData);//转成char显示
            }

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //关闭文件流，释放资源.
            try {
                fileInputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }

    /**
     * 使用 read(byte[] b) 读取文件，提高效率
     */
    @Test
    public void readFile02() {
        String filePath = "e:\\hello.txt";
        //字节数组
        byte[] buf = new byte[8]; //一次读取8个字节.
        int readLen = 0;
        FileInputStream fileInputStream = null;
        try {
            //创建 FileInputStream 对象，用于读取 文件
            fileInputStream = new FileInputStream(filePath);
            //从该输入流读取最多b.length字节的数据到字节数组。 此方法将阻塞，直到某些输入可用。
            //如果返回-1 , 表示读取完毕
            //如果读取正常, 返回实际读取的字节数
            while ((readLen = fileInputStream.read(buf)) != -1) {
                System.out.print(new String(buf, 0, readLen));//显示
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //关闭文件流，释放资源.
            try {
                fileInputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }
}

```
#### 10.2.2.2、FileOutputStream
![](https://cdn.nlark.com/yuque/0/2022/png/393192/1652316040276-548aa1ca-f475-4eda-b4e4-0ccd454bccf1.png#from=url&height=362&id=ka5K6&originHeight=469&originWidth=636&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=&width=491)
```java
public class FileOutputStream01 {
    /**
     * 演示使用FileOutputStream 将数据写到文件中,
     * 如果该文件不存在，则创建该文件
     */
    @Test
    public void writeFile() {

        //创建 FileOutputStream对象
        String filePath = "e:\\a.txt";
        FileOutputStream fileOutputStream = null;
        try {
            //得到 FileOutputStream对象 对象
            //1. new FileOutputStream(filePath) 创建方式，当写入内容是，会覆盖原来的内容
            //2. new FileOutputStream(filePath, true) 创建方式，当写入内容是，是追加到文件后面
            fileOutputStream = new FileOutputStream(filePath, true);
            //写入一个字节
            //fileOutputStream.write('H');//
            //写入字符串
            String str = "hsp,world!";
            //str.getBytes() 可以把 字符串-> 字节数组
            //fileOutputStream.write(str.getBytes());
            /*
            write(byte[] b, int off, int len) 将 len字节从位于偏移量 off的指定字节数组写入此文件输出流
             */
            fileOutputStream.write(str.getBytes(), 0, 3);

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                fileOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}


```
#### 10.2.2.3、FileReader
![](https://cdn.nlark.com/yuque/0/2022/png/393192/1652316081977-d2e1b934-b183-45e2-baca-159c81b12dc1.png#from=url&height=355&id=lI5Li&originHeight=594&originWidth=611&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=&width=365)
![](https://cdn.nlark.com/yuque/0/2022/png/393192/1652316103516-b4ff9c1e-3ac7-45df-95dd-81245694e980.png?x-oss-process=image%2Fresize%2Cw_1178%2Climit_0#from=url&id=Nj6ZQ&originHeight=219&originWidth=1178&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
```java
public class FileReader_ {
    /**
     * 单个字符读取文件
     */
    @Test
    public void readFile01() {
        String filePath = "e:\\story.txt";
        FileReader fileReader = null;
        int data = 0;
        //1. 创建FileReader对象
        try {
            fileReader = new FileReader(filePath);
            //循环读取 使用read, 单个字符读取
            while ((data = fileReader.read()) != -1) {
                System.out.print((char) data);
            }

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (fileReader != null) {
                    fileReader.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * 字符数组读取文件
     */
    @Test
    public void readFile02() {
        System.out.println("~~~readFile02 ~~~");
        String filePath = "e:\\story.txt";
        FileReader fileReader = null;

        int readLen = 0;
        char[] buf = new char[8];
        //1. 创建FileReader对象
        try {
            fileReader = new FileReader(filePath);
            //循环读取 使用read(buf), 返回的是实际读取到的字符数
            //如果返回-1, 说明到文件结束
            while ((readLen = fileReader.read(buf)) != -1) {
                System.out.print(new String(buf, 0, readLen));
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (fileReader != null) {
                    fileReader.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

}

```
#### 10.2.2.4、FileWriter
![](https://cdn.nlark.com/yuque/0/2022/png/393192/1652316087673-bdb5b928-e290-4b4d-b9b5-f79107959b82.png?x-oss-process=image%2Fresize%2Cw_487%2Climit_0#from=url&height=274&id=f1nW8&originHeight=363&originWidth=487&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=&width=367)
![](https://cdn.nlark.com/yuque/0/2022/png/393192/1652316108474-2adbc803-2f63-4cff-a2a0-fc4c5602c022.png?x-oss-process=image%2Fresize%2Cw_1178%2Climit_0#from=url&id=Hv48S&originHeight=388&originWidth=1178&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 10.2.3、节点流和处理流
![image.png](https://cdn.nlark.com/yuque/0/2022/png/2996398/1659664274052-4a08e453-a1f6-4cbc-80e8-46347cbe9246.png#clientId=u6ef58dc1-b2c2-4&from=paste&height=310&id=uace64dd8&originHeight=310&originWidth=967&originalType=binary&ratio=1&rotation=0&showTitle=false&size=307435&status=done&style=none&taskId=u39793f87-c911-4a05-bcd2-a9c6f34e9e9&title=&width=967)
节点流：是底层流/低级流，直接跟数据源相接
处理流（包装流）：包装节点流，既可以消除不同节点流的实现差异，也可以提供更方便的方法来完成输入输出
#### 10.2.3.1、处理流-BufferedReader和 BufferedWriter
BufferedReader/BufferedWriter增加了缓存机制，大大提高了读写文本文件的效率，同时，提供了更方便的按行读取的方法：readLine(); 处理文本时，我们一般可以使用缓冲字符流
```java
public class BufferedReader_ {
    public static void main(String[] args) throws Exception {
        String filePath = "e:\\a.java";
        //创建bufferedReader
        BufferedReader bufferedReader = new BufferedReader(new FileReader(filePath));
        //读取
        String line; //按行读取, 效率高
        //说明
        //1. bufferedReader.readLine() 是按行读取文件
        //2. 当返回null 时，表示文件读取完毕
        while ((line = bufferedReader.readLine()) != null) {
            System.out.println(line);
        }

        //关闭流, 这里注意，只需要关闭 BufferedReader ，因为底层会自动的去关闭 节点流
        //FileReader。
        bufferedReader.close();
    }
}

```

```java
public class BufferedWriter_ {
    public static void main(String[] args) throws IOException {
        String filePath = "e:\\ok.txt";
        //创建BufferedWriter
        //说明:
        //1. new FileWriter(filePath, true) 表示以追加的方式写入
        //2. new FileWriter(filePath) , 表示以覆盖的方式写入
        BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter(filePath));
        bufferedWriter.write("hello, kaka!");
        bufferedWriter.newLine();//插入一个和系统相关的换行
        bufferedWriter.write("hello2, kaka!");

        //说明：关闭外层流即可 ， 传入的 new FileWriter(filePath) ,会在底层关闭
        bufferedWriter.close();

    }
}

```
#### 10.2.3.2、处理流-BufferedInputStream 和 BufferedOutputStream 

```java
public class BufferedCopy02 {
    public static void main(String[] args) {

        String srcFilePath = "e:\\a.java";
        String destFilePath = "e:\\a3.java";

        //创建BufferedOutputStream对象BufferedInputStream对象
        BufferedInputStream bis = null;
        BufferedOutputStream bos = null;

        try {
            //因为 FileInputStream  是 InputStream 子类
            bis = new BufferedInputStream(new FileInputStream(srcFilePath));
            bos = new BufferedOutputStream(new FileOutputStream(destFilePath));

            //循环的读取文件，并写入到 destFilePath
            byte[] buff = new byte[1024];
            int readLen = 0;
            //当返回 -1 时，就表示文件读取完毕
            while ((readLen = bis.read(buff)) != -1) {
                bos.write(buff, 0, readLen);
            }
            System.out.println("文件拷贝完毕~~~");
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //关闭流 , 关闭外层的处理流即可，底层会去关闭节点流
            try {
                if(bis != null) {
                    bis.close();
                }
                if(bos != null) {
                    bos.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}

```
# 11、反射
## 11.1、反射基本概念
将类的各个组成部分封装为其他对象，这就是反射机制
好处：1. 可以在程序运行过程中，操作这些对象。2. 可以解耦，提高程序的可扩展性。

## 11.2、 获取Class对象的方式

1. Class.forName("全类名")：将字节码文件加载进内存，返回Class对象。多用于配置文件，将类名定义在配置文件中。读取文件，加载类
2.  类名.class：通过类名的属性class获取，多用于参数的传递
3. 对象.getClass()：getClass()方法在Object类中定义着。
> 同一个字节码文件(*.class)在一次程序运行过程中，只会被加载一次，不论通过哪一种方式获取的Class对象都是同一个。


## 11.3、Class对象功能

1. 获取成员变量
```java
Field[] getFields() ：获取所有public修饰的成员变量
Field getField(String name)   获取指定名称的 public修饰的成员变量
Field[] getDeclaredFields()  获取所有的成员变量，不考虑修饰符
Field getDeclaredField(String name)  
```

2. 获取构造方法
```java
Constructor<?>[] getConstructors()  返回public修饰的构造方法
Constructor<T> getConstructor(类<?>... parameterTypes)  
Constructor<T> getDeclaredConstructor(类<?>... parameterTypes)  
Constructor<?>[] getDeclaredConstructors()  返回所有的构造方法
```

3. 获取成员方法
```java
Method[] getMethods()  
Method getMethod(String name, 类<?>... parameterTypes)  
Method[] getDeclaredMethods()  
Method getDeclaredMethod(String name, 类<?>... parameterTypes)  
```

4. 获取全类名
```java
String getName()  
```
## 11.4、Field 成员变量
```java
1. 设置值
	void set(Object obj, Object value)  
2. 获取值
	get(Object obj) 
3. 忽略访问权限修饰符的安全检查
	setAccessible(true):暴力反射
```
## 11.5、Constructor 构造方法
```java
T newInstance(Object... initargs)  
如果使用空参数构造方法创建对象，操作可以简化：Class对象的newInstance方法
```

## 11.6、Method 方法对象
```java
1. 执行方法：
	Object invoke(Object obj, Object... args)  
2. 获取方法名称：
	String getName:获取方法名
```
