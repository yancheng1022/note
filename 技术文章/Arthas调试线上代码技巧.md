# 1、背景
1、测试没问题，但生产环境没有生效，需要查看生产环境代码是否更新上去



# 2、安装

```shell
curl -O https://arthas.aliyun.com/arthas-boot.jar
java -jar arthas-boot.jar
```

启动之后，就会列出当前系统上所有的java进程，输入进程前面的序号，连接到相应的应用进程。

# 3、反编译代码

``` shell
jad --source-only com.chivd.ims.controller.HomePageController getSaleData --lineNumber false
```

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202403271326704.png)

> 注意：访问类的静态成员时（属性或方法），才会进行类加载。