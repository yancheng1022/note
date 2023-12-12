## 1.lambda

### 1.1 lambda特性

Lambda 表达式，本质式匿名内部类的语法糖，使代码简洁易读。使用lambda的前提是接口是函数式接口（只有一个抽象方法的接口）。lambda在编译的时候会转化成匿名内部类的形式

### 1.2 简单示例

```
public class AnonymousInnerClassExample {

    public static void main(String[] args) {

        // 使用匿名内部类创建一个线程
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("Hello from anonymous inner class thread!");
            }
        });
        // 启动线程
        thread.start();


        // 使用 Lambda 表达式创建一个线程
        Thread thread = new Thread(() -> System.out.println("Hello from Lambda thread!"));
        // 启动线程
        thread.start();
    }
}
```

## 2. Stream

Stream 是 Java8 中处理集合的关键抽象概念，它可以指定你希望对集合进行的操作，可以执行非常复杂的查找、过滤和映射数据等操作。**Stream**的操作符大体上分为两种：**中间操作符**和**终止操作符**

### 2.1 中间操作符

对于数据流来说，中间操作符在执行制定处理程序后，数据流依然可以传递给下一级的操作符

1. map(mapToInt,mapToLong,mapToDouble) ：转换操作符，把比如A->B，这里默认提供了转int，long，double的操作符。

```
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
// 获取对应的平方数
List<Integer> squaresList = numbers.stream().map( i -> i*i).distinct().collect(Collectors.toList());
```

2. flatmap(flatmapToInt,flatmapToLong,flatmapToDouble) ：拍平操作比如把 int[]{2,3,4} 拍平 变成 2，3，4 也就是从原来的一个数据变成了3个数据，这里默认提供了拍平成int,long,double的操作符。

```
String[] words = new String[]{"Hello","World"};
// 结果：[["H","e","l","l","o"],["W","o","r","l","d"]]
List<String[]> collect = Arrays.stream(words).map(n -> n.split("")).distinct().collect(toList());
// 结果：["H","e","l","o","W","r","d"]
List<String>   collect = Arrays.stream(words).map(n -> n.split("")).flatMap(Arrays::stream).distinct().collect(toList());
```

3. limit ：限流操作，比如数据流中有10个 我只要出前3个就可以使用。

```
String[] strings = {"kaka1","kaka2","kaka3"};
Stream stream = Stream.of(strings);
Stream streamlimit = stream.limit(1);
streamlimit.forEach(System.out::println);
```

4. distinct：去重操作，对重复元素去重，底层使用了equals方法

```
List<String> list = Arrays.asList("AA", "BB", "CC", "BB", "CC", "AA", "AA");
String output = list.stream().distinct().collect(Collectors.joining(","));
System.out.println(output);
```


5. filter ：过滤操作，方法返回`true`代表当前元素会**保留下来**










```
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
// 获取空字符串的数量
long count = strings.stream().filter(string -> string.isEmpty()).count();
```

6. skip 跳过操作，跳过某些元素

```
// 结果kaka2 kaka3
String[] array = {"kaka1","kaka2","kaka3"};
Arrays.asList(array).stream().skip(1).forEach(System.out::println);
```

7. sorted(unordered)： 排序操作，对元素排序，前提是实现Comparable接口，当然也可以自定义比较器。

```
Integer[] array = {6,4,5};
// 自然升序
List<Integer> collect = Arrays.asList(array).stream().sorted().collect(toList());
// 自然降序
List<Integer> collect =  Arrays.asList(array).stream().sorted(Comparator.reverseOrder()).collect(toList());
// 自定义降序
List<Integer> collect =  list.stream().sorted(Comparator.comparing(Student::getAge).reversed()).collect(toList());
```

### 2.2 终止操作符

数据经过中间加工操作，就轮到终止操作符上场了；终止操作符就是用来对数据进行收集或者消费的，数据到了终止操作这里就不会向下流动了，终止操作符只能使用一次

1. collect 收集操作，将所有数据收集起来，这个操作非常重要，官方的提供的Collectors 提供了非常多收集器，可以说Stream 的核心在于Collectors

```
List<String> list = Arrays.asList("jack", "bob", "alice", "mark");
// ["jack","bob","alice","mark"]
List<String> listResult = list.stream().collect(Collectors.toList());
// 逗号拼接  结果：jack,bob,alice,mark
String result = list.stream().collect(Collectors.joining(","));
```

2. count 统计操作，统计最终的数据个数

```
List<String> list = Arrays.asList("jack", "bob", "alice", "mark");
long count = list.stream().count();
```

3. findFirst、findAny 查找操作，查找第一个、查找任何一个 返回的类型为Optional

```
List<String> lst1 = Arrays.asList("Jhonny", "David", "Jack", "Duke", "Jill","Dany","Julia","Jenish","Divya");
List<String> lst2 = Arrays.asList("Jhonny", "David", "Jack", "Duke", "Jill","Dany","Julia","Jenish","Divya");
//总是打印出David
Optional<String> findFirst = lst1.parallelStream().filter(s -> s.startsWith("D")).findFirst();
//会随机地打印出Jack/Jill/Julia
Optional<String> fidnAny = lst2.parallelStream().filter(s -> s.startsWith("J")).findAny();
```

4. noneMatch（所有元素都不匹配，返回true）、allMatch（所有元素匹配，返回true）、anyMatch （任何一个元素匹配，返回true）匹配操作，数据流中是否存在符合条件的元素 返回值为bool 值

```
List<String> strs = Arrays.asList("a", "a", "a", "a", "b");
boolean aa = strs.stream().anyMatch(str -> str.equals("a"));
boolean bb = strs.stream().allMatch(str -> str.equals("a"));
boolean cc = strs.stream().noneMatch(str -> str.equals("a"));
System.out.println(aa);// TRUE
System.out.println(bb);// FALSE
System.out.println(cc);// FALSE
```

5. min、max 最值操作，需要自定义比较器，返回数据流中最大最小的值。

```
//返回字符串最长的元素
String s = lst1.stream().max(Comparator.comparing(String::length)).get();
```

6. forEach、forEachOrdered 遍历操作，这里就是对最终的数据进行消费了

只有在parallel()并行处理情况下，才会有差别。  
forEach在并行情况下，自由执行，不考虑顺序。  
forEachOrdered在并行情况下，按顺序执行

```
//如果不是parallel()并行处理，  这2个方法没区别
Stream.of("AAA","BBB","CCC").forEach(System.out::println);
Stream.of("AAA","BBB","CCC").forEachOrdered(System.out::println);
//parallel()并行处理的情况下，还想保证顺序，就用forEachOrdered
Stream.of("AAA","BBB","CCC").parallel().forEach(System.out::println);
Stream.of("AAA","BBB","CCC").parallel().forEachOrdered(System.out::println);
```

7. toArray 数组操作，将数据流的元素转换成数组。

```
int[] ints = IntStream.of(20, 40, 60, 70, 100, 120, 140).toArray();
```