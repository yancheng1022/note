# 1、背景
1、测试没问题，但生产环境没有生效，需要查看生产环境代码是否更新上去（反编译）
2、在没有日志的情况下，我们不知道客户端请求过来的参数是什么，临时加日志再发版是一件很复杂的事情（监视方法执行）
3、有时候我们发现应用卡住了， 通常是由于某个线程拿住了某个锁， 并且其他线程都在等待这把锁造成的（thread -b）


# 2、安装

```shell
curl -O https://arthas.aliyun.com/arthas-boot.jar
java -jar arthas-boot.jar
```

启动之后，就会列出当前系统上所有的java进程，输入进程前面的序号，连接到相应的应用进程。

# 3、反编译代码

``` shell
jad --source-only com.controller.HomePageController getSaleData --lineNumber false
```

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202403271326704.png)

> 注意：访问类的静态成员时（属性或方法），才会进行类加载。所以想要反编译静态方法，需要先确保调用一次

# 4、监视方法执行

```shell
watch com.data.controller.baseInfo.InstrumentBaseController list '{params,returnObj,throwExp}' -n 5 -x 3
```

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202403271353072.png)


# 5、执行静态方法


```shell
ognl '@com.chivd.common.utils.DateUtils@getDate()'
```


# 6、找出当前阻塞其它线程的线程

有时候我们发现应用卡住了， 通常是由于某个线程拿住了某个锁， 并且其他线程都在等待这把锁造成的。 为了排查这类问题， arthas 提供了thread -b， 一键找出那个罪魁祸首

# 7、线上修改代码（免重启）

```shell
# 找到需要修改的类

```


