
# 1、背景

我们都知道可以通过继承Thread类或者实现Runnable接口两种方式实现多线程。但是有时候我们希望得到多线程异步任务执行后的结果，也就是异步任务执行后有返回值，Thread和Runnable是不能实现的。当我们需要返回值的时候怎么办呢？ Java 1.5 推出的Callable和Future接口就解决了这个问题。但是因为Future有几个局限，由于这几个局限，在Java1.8就推出了加强版的Future类：CompletableFuture


# 2、Future使用

假如我们现在有如下需求：

老板正在开会，开会过程中发现少一份材料，通知秘书去整理，在秘书整理过程中老板这边还在继续开会，秘书整理完以后将材料给到老板手中。

需求分析：

老板开会是主线程，不能中断。秘书就是异步任务秘书执行完任务需要将结果返回给老板这个主线程手中。咱们看看通过Future实现此需求有什么局限，然后再通过CompletableFuture实现此需求看看是否更好。

Future接口（实现类：FutureTask）定义了操作异步任务执行的一些方法：如获取异步任务执行结果、取消任务的执行结果、判断任务是否被取消、判断任务执行是否完成等。

```java
public class BossMeeting {
 
    /**
     * 主线程为老板正在开会
     */
    public static void main(String[] args) {
        System.out.println("老板开会start");
        ExecutorService executorService = Executors.newFixedThreadPool(1);
        FutureTask<String> secretaryFuture = new FutureTask<>(() -> {
            Thread.sleep(1000);
            return "老板需要的材料";
        });
 
        //老板发现缺少材料，提交异步任务（找秘书）
        executorService.submit(secretaryFuture);
 
        /**
         * 方法1
         * 局限：导致线程堵塞
         */
        try {
            //获取秘书搜集的材料 （堵塞线程）
            String material = secretaryFuture.get();
            System.out.println("秘书搜集到的材料：" + material);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } catch (ExecutionException e) {
            throw new RuntimeException(e);
        }
 
        /**
         * 方法2
         * 通过while轮询方式会消耗cpu
         */
        while (true) {
            if (secretaryFuture.isDone()) {
                try {
                    //获取秘书搜集的材料 （堵塞线程）
                    String material = secretaryFuture.get();
                    System.out.println("秘书搜集到的材料：" + material);
                    break;
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                } catch (ExecutionException e) {
                    throw new RuntimeException(e);
                }
            } else {
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }
        System.out.println("老板开会end");
    }
}
```

输出结果：
老板开会start
秘书搜集到的材料：老板需要的材料
老板开会end
# 3、CompletableFuture实现非阻塞式执行

CompletableFuture提供了一种观察者模式类似的机制，可以让任务执行完成后通知监听的一方。

```java
public class CompletableFuture<T> implements Future<T>, CompletionStage<T> {
```

用CompletableFuture实现上述的业务场景：

```java
 
/**
 * 老板开会Future实现
 */
public class BossMeeting {
    public static void main(String[] args) {
        System.out.println("老板开会start");
        ExecutorService executorService = Executors.newFixedThreadPool(1);
        try {
            CompletableFuture.supplyAsync(() -> {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
                return "秘书搜集完材料";
                //结束返回
            }, executorService).whenComplete((v, e) -> {
                //无异常说明 执行成功
                if (e == null) {
                    System.out.println("秘书搜集到的材料：" + v);
                }
                //异常处理
            }).exceptionally(e -> {
                e.printStackTrace();
                System.out.println("执行异常：" + e.getCause());
                return null;
            });
 
            System.out.println("老板继续开会");
 
            try {
                //模拟老板继续开会3秒钟
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            System.out.println("老板开会end");
        } finally {
            executorService.shutdown();
        }
    }
}



```

执行结果：
老板开会start  
老板继续开会  
秘书搜集到的材料：秘书搜集完材料  
老板开会end


# 4、CompletableFuture相关方法


## 4.1、CompletableFuture创建方式

官方推荐使用CompletableFuture提供的静态方法创建CompletableFuture实例，以下是提供的静态方法：

```java
// 无返回值 使用ForkJoinPool线程池
public static CompletableFuture<Void> runAsync(Runnable runnable)
// 无返回值 可以自定义线程池
public static CompletableFuture<Void> runAsync(Runnable runnable, Executor executor)
// 有返回值 使用ForkJoinPool线程池
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier)
// 有返回值 可以自定义线程池
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier, Executor executor)
```

supply开头：这种方法，可以返回异步线程执行之后的结果。  
run开头：这种不会返回结果，就只是执行线程任务。  
如果你想异步运行一些后台任务并且不想从任务中返回任何东西，那么你可以使用run开头的


### 4.2、CompletableFuture获取返回值

通过get、join、getNow获取返回值，区别如下：

join：返回结果或者抛出一个unchecked异常(CompletionException)，不需要显示捕获异常。
get：返回结果或者一个具体的异常(ExecutionException, InterruptedException)，此方法继承至Future是堵塞的。
getNow：如果当前任务执行完成，返回执行结果，否则返回valueIfAbsent（默认值）。
