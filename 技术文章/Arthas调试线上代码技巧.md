# 1、背景
1、测试没问题，但生产环境没有生效，需要查看生产环境代码是否更新上去



# 2、安装

```shell
curl -O https://arthas.aliyun.com/arthas-boot.jar
java -jar arthas-boot.jar
```

启动之后，就会列出当前系统上所有的java进程，输入进程前面的序号，连接到相应的应用进程。

![image.png|675](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/202403271031144.png)

# 3、反编译代码

