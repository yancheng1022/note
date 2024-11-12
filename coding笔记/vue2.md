# 1、vue简介
用于构建用户界面的渐进式javascript框架

渐进式：vue可以自底向上逐层应用
>简单应用：只需要一个轻量小巧的核心库
>复杂应用：可以引入各式各样的vue插件

## 1.1、vue特点
1、采用组件化模式，提高代码复用率
2、声明式编码，让编码人员无需直接操作DOM
3、使用虚拟DOM+优秀的diff算法，尽量复用DOM节点

## 1.2、起步demo

```vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script type="text/javascript" src="./js/vue.js"></script>
</head>
<body>
    <div id="root">
        <h1>hello,{{name}}</h1>
    </div>

    <script>
        // 创建vue实例
        const vm = new Vue({
            el:'#root', //el用于指定当前vue实例为哪个容器服务，值为css选择器字符串
            data:{ // data中用于存储数据，供给el所指定的容器去使用
                name:"kaka"
            }
        })
    </script>
</body>
</html>
```

## 1.3、模板语法
