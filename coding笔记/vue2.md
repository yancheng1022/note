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

插值语法：插值语法一般用在标签体的值{{}}
指令语法：用于解析标签(标签体,标签属性, 绑定事件)上，类似于v-bind

```vue
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Vue模版语法的学习</title>
    <script src="../js/vue.js"></script>
</head>
<body>
   <div id="root">
       <!--
          插值语法一般用在标签体的值{{}}
          指令语法:用于解析标签(标签体,标签属性, 绑定事件)上，类似于v-bind
       -->
       <h1>插值语法</h1>
       <h3>你好, {{ name }} </h3>
       <hr/>
       <h1>指令语法</h1>
       <!--v-bind绑定指令，就把下面的url当成一个js表达式去执行了-->
       <a v-bind:href="url.toUpperCase()" v-bind:x="x">百度一下</a>
       <!-- v-bind: 还可以简写为: -->
       <a :href="url_taiwan" :x="x">google 台湾</a>
       <p>学校: <a :href="school.url.toString()">{{ school.name }}</a> </p>
   </div>
   <script type="text/javascript">
       new Vue({
           el: "#root",
           data: {
               name : 'jack',
               url : 'https://www.baidu.com',
               x: 'test v-bind',
               url_taiwan: 'https://www.google.com.tw',
               school: {
                   name: '天津科技大学',
                   url:'https://www.wust.edu.cn/'
               }
           }
       })
   </script>
</body>
</html>
```

## 1.4、数据绑定


Vue中有2种数据绑定的方式：
1.单向绑定(v-bind)：数据只能从data流向页面。
2.双向绑定(v-model)：数据不仅能从data流向页面，还可以从页面流向data。

>1.双向绑定一般都应用在表单类元素上（如：input、select等）
   2.v-model:value 可以简写为 v-model，因为v-model默认收集的就是value值。
   3.不是什么都可用v-model的.比如v-model不支持h1。v-model只能应用在表单元素上(输入元素)，与用户交互(都有共同的value属性)



```vue
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>vue 数据绑定</title>
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id="root">

        <label>
            单项数据绑定:
            <!--<input type='text' v-bind:value="name"/>-->
            <!--简写-->
            <input type='text' :value="name"/>
        </label>
        <label>
            双向数据绑定:
            <!--<input type='text' v-model:value="name"/>-->
            <input type='text' v-model="name"/>
        </label>
        <br/>

        <h1 v-bind:x="name">
            你好啊
        </h1>
    </div>
    <script type="text/javascript">
        //v-bind可以完成数据绑定(单项绑定)
        //v-model双向数据绑定
        //单项数据绑定 双向数据绑定
        Vue.config.productionTip = false;
        new Vue({
            el: '#root',
            data:{
                name: 'shanghai'
            }
        })
    </script>
</body>
</html>
```