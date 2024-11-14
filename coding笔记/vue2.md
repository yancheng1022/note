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

## 1.3、el与data的两种写法

data与el的2种写法
1.el有2种写法
	(1).new Vue时候配置el属性。
	(2).先创建Vue实例，随后再通过vm.$mount('#root')指定el的值。
	
2.data有2种写法
	(1).对象式
	(2).函数式
	如何选择：目前哪种写法都可以，以后学习到组件时，data必须使用函数式，否则会报错。
	
3.一个重要的原则：
	由Vue管理的函数(例如data)，一定不要写箭头函数，一旦写了箭头函数，this就不再是Vue实例了。

```vue
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>el和data的两种写法</title>
    <script src="../js/vue.js"></script>
</head>
<body>
   <div id="root">
       <h1>你好，{{ name }}</h1>
   </div>
   <script type="text/javascript">
       Vue.config.productionTip = false;
       //el的两种写法
       // const v = new Vue({
       //     // el: '#root', //第一种写法
       //     data: {
       //         name: '上海'
       //     }
       // });
       //
       // // console.log(v);
       // //关联root容器，用mount方法
       // setTimeout(() => {
       //     v.$mount('#root'); //第二种写法 挂载到页面上
       // }, 3000)

       //data的两种写法
       new Vue({
          el: '#root',
           //data的第一种写法:对象式
           // data: {
           //     name: 'shanghai'
           // }
           //第二种写法: 函数式(返回对象中包含你想渲染的模版数据)
           //学习组件的时候要是用函数式 这个函数是Vue来调用的
           // data: () => {
           //    console.log(`@@@@`, this); //此时this是window，因为箭头函数没有自己的this，它向外查找找到的window
           //    return ({
           //         name: 'shanghai'
           //     })
           // }
           //尽量不要写成剪头函数，因为会丢失this
           data (){
              console.log('@@@', this); //此时this是Vue
              return {
                  name: 'shanghai'
              }
           }
       });
   </script>
</body>
</html>
```

# 2、基础知识
## 2.1、模板语法

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

## 2.2、数据绑定


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

## 2.3、理解MVVM

1. M：模型(Model) ：data中的数据
2. V：视图(View) ：模板代码
3. VM：视图模型(ViewModel)：Vue实例

>1.data中所有的属性，最后都出现在了vm身上。
  2.vm身上所有的属性 及 Vue原型上所有属性，在Vue模板中都可以直接使用。

## 2.4、事件处理

### 2.4.1、事件的基本使用
1.使用v-on:xxx 或 @xxx 绑定事件，其中xxx是事件名；
2.事件的回调需要配置在methods对象中，最终会在vm上；
3.methods中配置的函数，不要用箭头函数！否则this就不是vm了；
4.methods中配置的函数，都是被Vue所管理的函数，this的指向是vm 或 组件实例对象；
5.@click="demo" 和 @click="demo($event)" 效果一致，但后者可以传参；

```vue
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>事件处理基本</title>
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <!--指令语法 v开头 例如v-on:click点击事件-->
        <h1>欢迎 {{ name }} </h1>
        <button v-on:click="showInfo1">点我提示信息1</button>
        <!--简写形式 @click-->
        <button @click="showInfo2($event,66)">点我提示信息2</button>
    </div>
    <script type="text/javascript">
        Vue.config.productionTip = false;
        const vm = new Vue({
            el: '#root',
            data(){
                return {
                   name: 'shanghai',

                }
            },
            //methods配置事件处理的回调函数
            methods:{
                showInfo1(e){
                    //默认给你传递event参数
                    //当是箭头函数的话this那就是window
                    console.log(this === vm); //this是vue实例
                    console.log('你好');
                },
                //接收参数
                showInfo2(e,num){
                    console.log(e.target);//
                    console.log(`接收参数：${num}`);
                }
            }
        });
    </script>
</body>
</html>
```

### 2.4.2、事件修饰符

Vue中的事件修饰符：
1.prevent：阻止默认事件（常用）；
2.stop：阻止事件冒泡（常用）；
3.once：事件只触发一次（常用）；
4.capture：使用事件的捕获模式；
5.self：只有event.target是当前操作的元素时才触发事件；
6.passive：事件的默认行为立即执行，无需等待事件回调执行完毕；

```vue
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>修饰符</title>
    <script src="../js/vue.js"></script>
    <style>
        *{
            margin: 20px;
        }
        .demo1{
            height: 100px;
            background: deepskyblue;
        }
        .box1{
            padding: 5px;
            background: deepskyblue;
        }
        .box2 {
            padding: 5px;
            background: orange;
        }
        .list{
            width:200px;
            height: 200px;
            background: salmon;
            overflow: auto;
        }
        .list li{
            height: 100px;
        }
    </style>
</head>
<body>
   <div id="root">
       <h1>欢迎来到 {{ name }}</h1>
       <!--阻止默认事件（常用-->
       <a href="https://www.baidu.com" @click.prevent="showInfo" >点我提示信息</a>
       <!--阻止事件冒泡（常用）-->
       <div class="demo1" @click="showInfo">
           <button @click.stop="showInfo">点我提示信息</button>
           <!--修饰符也可以连用，例如下面事例是即阻止冒泡同时也阻止默认行为-->
           <!--<a href="https://www.google.com.tw" @click.prevent.stop="showInfo">谷歌台湾</a>-->
       </div>
       <!--事件只触发一次-->
       <button @click.once="showInfo">点我提示信息,只在第一次点击生效</button>
       <!-- capture事件的捕获模式 让事件以捕获的方式来处理(先捕获再冒泡)-->
       <div class="box1" @click.capture="showMsg(1)">
           div1
           <div class="box2" @click="showMsg(2)">div2</div>
       </div>
       <!-- self 只有event.target是当前操作的元素时才触发事件(变相阻止冒泡)-->
       <div class="demo1" @click.self="showInfo">
           <button @click="showInfo">点我提示信息</button>
       </div>
       <!--passive：事件的默认行为立即执行，无需等待事件回调执行完毕；-->
       <!--scroll滚动条一滚动就会触发的事件 wheel鼠标滚轮事件-->
       <ul class="list" @scroll.passive="demo">
           <li>1</li>
           <li>2</li>
           <li>3</li>
           <li>4</li>
       </ul>
   </div>
   <script type='text/javascript'>
       Vue.config.productionTip = false;
       new Vue({
           el: "#root",
           data(){
               return {
                   name: 'Shanghai'
               }
           },
           methods:{
               showInfo(e){
                   // e.preventDefault(); 阻止a标签默认行为
                   // e.stopPropagation() //阻止事件冒泡
                   // alert('你好');
                   console.log(e.target);
               },
               showMsg(msg){
                   console.log(msg);
               },
               demo(){
                   // console.log(`@`)
                   // for(let i = 0; i < 100000; i++){
                   //     console.log('#')
                   // }
                   // console.log('累了')
               }
           }
       });
   </script>
</body>
</html>
```


### 2.4.3、键盘事件

1.Vue中常用的按键别名：
                        回车 => enter
                        删除 => delete (捕获“删除”和“退格”键)
                        退出 => esc
                        空格 => space
                        换行 => tab (特殊，必须配合keydown去使用)
                        上 => up
                        下 => down
                        左 => left
                        右 => right

2.Vue未提供别名的按键，可以使用按键原始的key值去绑定，但注意要转为kebab-case（短横线命名）

3.系统修饰键（用法特殊）：ctrl、alt、shift、meta
                        (1).配合keyup使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才被触发。
                        (2).配合keydown使用：正常触发事件。
                        
4.也可以使用keyCode去指定具体的按键（不推荐）
5.Vue.config.keyCodes.自定义键名 = 键码，可以去定制按键别名

```vue
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>vue 键盘事件</title>
    <script src="../js/vue.js"></script>
</head>
<body>
  <div id="root">
        <!--vue中键盘事件的绑定 一般用keyUp(keydown)-->
        <h1>欢迎来到{{ name }}</h1>
        <input type="text" @keyup.enter="showInfo" placeholder="按下回车提示输入"/>
        <input type="text" @keyup.delete="showInfo" placeholder="退格或删除提示输入"/>
        <input type="text" @keyup.esc="showInfo" placeholder="按下esc提示输入"/>
        <input type="text" @keydown.tab="showInfo" placeholder="按下tab示输入"/>
        <input type="text" @keyup.ctrl="showInfo" placeholder="按下command提示输入"/>
        <input type="text" @keydown.p="showInfo" placeholder="按下p提示输入"/>
        <!--键盘事件的修饰符也可以连用-->
       <input type="text" @keydown.command.g="showInfo" placeholder="按下command和g提示输入"/>
    </div>
    <script type="text/javascript">
        new Vue({
            el:'#root',
            data:{
                name: 'shanghai'
            },
            methods:{
                showInfo:function (e){
                    // if(e.keyCode === 13) {
                    //     console.log(e.target.value);
                    // }
                    console.log(e.target.value);
                    console.log(e.key); //按下的名字
                }
            }
        })
    </script>
</body>
</html>
```

## 2.5、计算属性

### 2.5.1、计算属性基础

1.定义：要用的属性不存在，要通过已有属性计算得来。
2.原理：底层借助了Objcet.defineproperty方法提供的getter和setter。
3.get函数什么时候执行？
	(1).初次读取时会执行一次。
	(2).当依赖的数据发生改变时会被再次调用。
4.优势：与methods实现相比，内部有缓存机制（复用），效率更高，调试方便。
5.备注：
	1.计算属性最终会出现在vm上，直接读取使用即可。
	2.如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生改变。

```vue
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>vue计算属性语法实现</title>
    <script src="../js/vue.js"></script>
</head>
<body>
<div id="root">
    <!--

		 -->
    <!--v-model双向绑定-->
    姓:<input type="text" v-model="firstName"/>
    <br/><br/>
    名:<input type="text" v-model="lastName"/>
    <br/><br/>
    测试:<input type="text" v-model="test"/>
    <br/><br/>
    全名: <span>{{ fullName }}</span>
</div>
<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            //data里的都是属性
            firstName: '张',
            lastName: '三',
            test:'test'
        },
        //计算属性--> 旧属性加工
        computed: {
            fullName: {
                //get的作用，当读取fullName时get就会被调用，且返回值就做为fullName的值
                //defineProperty
                //注意vm._data是不存在计算属性的
                //计算属性算完之后直接丢到了viewModel实例对象身上
                get(){
                    //此时get函数中的this指向是vm
                    console.log('get调用了', this);
                    return this.firstName + '--' + this.lastName
                },
                /**
                 * set什么时候调用
                 *   1.当fullName被修改时
                 * @param value
                 */
                set(value){
                    //修改计算属性所依赖的普通属性(放在data里面的)
                    //this === vm
                    const [ firstName, lastName ] = value.split('-');
                    this.firstName = firstName;
                    this.lastName = lastName; //依赖属性发生改变之后,计算属性自动改变
                }
            }
        }
    });
</script>
</body>
</html>
```

### 2.5.2、计算属性简写

```vue
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>vue计算属性语法实现</title>
    <script src="../js/vue.js"></script>
</head>
<body>
<div id="root">
    <!--v-model双向绑定-->
    姓:<input type="text" v-model="firstName"/>
    <br/><br/>
    名:<input type="text" v-model="lastName"/>
    <br/><br/>
    测试:<input type="text" v-model="test"/>
    <br/><br/>
    全名: <span>{{ fullName }}</span>
</div>
<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            //data里的都是属性
            firstName: '张',
            lastName: '三',
            test:'test'
        },
        //计算属性--> 旧属性加工
        computed: {
            //简写形式
            //前提:计算属性只考虑读取不考虑修改 set丢了
            //简写计算书写为一个函数(这个函数当成getter使用), 注意不要写箭头函数
            //执行完getter之后，vm直接保存返回的数据为fullName属性的属性值,此时vm.fullName不是函数而是一个确切的计算值
            fullName: function (){
                return this.firstName + '--' + this.lastName;
            }
     }});
</script>
</body>
</html>
```

## 2.6、监视属性

### 2.6.1、基本用法
监视属性watch：
1.当被监视的属性变化时, 回调函数自动调用, 进行相关操作
2.监视的属性必须存在，才能进行监视！！
3.监视的两种写法：
		(1).new Vue时传入watch配置
		(2).通过vm.$watch监视

```vue
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>天气案例_监视属性</title>
    <script src="../js/vue.js"></script>
</head>
<body>
<div id="root">
    <h1>今天天气很 {{ info }}</h1>
    <button @click="changeWeather">
        切换
    </button>
</div>
<script type="text/javascript">
    Vue.config.productionTip = false;
    const vm = new Vue({
        el:'#root',
        data:{
            isHot: true
        },
        //计算属性
        computed: {
            info(){
                return this.isHot ? '炎热' : '凉爽';
            }
        },
        //改变模版数据的方法
        methods:{
            changeWeather(){
                this.isHot = !this.isHot;
            }
        },
        // watch:{
        //     //监视的配置对象
        //     //watch不仅能监视data的普通属性，也可以检测计算属性
        //     isHot:{
        //         immediate: true, //当这个属性为true时，页面刚渲染就运行handler函数
        //         //handler 什么时候调用呢
        //         //当isHot发生改变就会调用该函数
        //         //handler接收两个参数，一个是这个状态参数改变前的值，另一个是改变后的旧值
        //         handler(newValue, preValue){
        //             console.log('ishot 被修改了');
        //             console.log(`newValue: ${newValue}, preValue: ${preValue}`);
        //         }
        //     }
        // }
		    
    });
    //watch的第二种写法，直接采用vm对象实例
    vm.$watch('isHot', {
        immediate: true, //当这个属性为true时，页面刚渲染就运行handler函数
        //handler 什么时候调用呢
        //当isHot发生改变就会调用该函数 
        //handler接收两个参数，一个是这个状态参数改变前的值，另一个是改变后的旧值
        handler(newValue, preValue){
            console.log('ishot 被修改了');
            console.log(`newValue: ${newValue}, preValue: ${preValue}`);
        }
    });
</script>
</body>
</html>
```

### 2.6.2、深度监视

深度监视：
(1).Vue中的watch默认不监测对象内部值的改变（一层）。
(2).配置deep:true可以监测对象内部值改变（多层）。

>(1).Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以(想让它可以则配置deep:true)！
  (2).使用watch时根据数据的具体结构，决定是否采用深度监视。

```vue
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>天气案例_深度监视</title>
    <script src="../js/vue.js"></script>
</head>
<body>
<div id="root">
    <h1>今天天气很 {{ info }}</h1>
    <button @click="changeWeather">
        切换
    </button>
    <hr/>
    <h3>a的值是:{{ numbers.a }}</h3>
    <button @click="numbers.a++">
        点我让a加一
    </button>
    <h3>b的值是:{{ numbers.b }}</h3>
    <button @click="numbers.b++">
        点我让b加一
    </button>
    <h1>
        测试vue自身监测数据属性
        {{ numbers.d.e }}
    </h1>
</div>
<script type="text/javascript">
    Vue.config.productionTip = false;
    const vm = new Vue({
        el:'#root',
        data:{
            isHot: true,
            numbers:{
                a:1,
                b:1,
                d:{
                    e:2
                }
            }
        },
        //计算属性
        computed: {
            info(){
                return this.isHot ? '炎热' : '凉爽';
            }
        },
        //改变模版数据的方法
        methods:{
            changeWeather(){
                this.isHot = !this.isHot;
            }
        },
        watch:{
            //监视的配置对象
            //watch不仅能监视data的普通属性，也可以检测计算属性
            isHot:{
                //immediate: true, //当这个属性为true时，页面刚渲染就运行handler函数
                //handler 什么时候调用呢
                //当isHot发生改变就会调用该函数
                //handler接收两个参数，一个是这个状态参数改变前的值，另一个是改变后的旧值
                handler(newValue, preValue){
                    console.log('ishot 被修改了');
                    console.log(`newValue: ${newValue}, preValue: ${preValue}`);
                }
            },
            //监测多级结构里面的属性 深度监视
            // 'numbers.a':{
            //     handler(){
            //       console.log('a发生改变了')
            //     }
            // }
            //深度监视numbers中的所有属性
            numbers:{
                deep: true, //监视多级结构的属性
                handler(){
                    console.log('numbers 发生改变')
                }
            }
        }
    });

</script>
</body>
</html>
```