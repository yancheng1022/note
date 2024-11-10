vscode插件：
chinese 中文
vscode-icons 文件图标
live server 热部署


# 1、HTML

html：HyperTxt Markup Language 超文本标记语言


## 1.1、html文档声明

html5

```html
<!DOCTYPE html>
```

html4

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">
```

## 1.2、html字符编码

```html
    <head>
        <meta charset="UTF-8">
        <title>标题</title>
    </head>
```

## 1.3、设置语言

```html
<html lang="zh-CN"></html>
```

## 1.4、排版标签

### 1.4.1、标题标签

h1,h2,h3,h4,h5,h6
一般一个页面h1只有一个
h1-h6不允许嵌套

### 1.4.2、段落标签

p  ：段落
div ： 容器

p标签中不能再用p，h1，div标签

## 1.5、语义化标签
标签默认的效果不重要，语义最重要
比如我们的h1标签可以通过修改样式修改成更大的字体或其他样式，那么h1存在的意义是什么？
（1）代码可读性强
（2）有利于seo
（3）方便设备解析（屏幕阅读器，盲人阅读器）

## 1.6、块级元素与行内元素

块级元素：独占一行 （div，h1，p）
行内元素：不独占一行 （input，span）

规则：
（1）块级元素中能写行内元素，块级元素（特殊：p标签中不能写块级元素）
（2）行内元素中能写行内元素，但不能写块级元素

>行内元素居中：vertical-align: center
>块元素居中：line-height: height
## 1.7、文本标签

文本标签用于包裹词汇，短语等。通常在排版标签里面。排版标签更宏观（大段的文字：h1,p,div等）。文本标签更微观（词汇，短语等）
文本标签通常都是行内元素

| 标签名    | 标签语义             | 单/双标签 |
| :----- | :--------------- | ----- |
| em     | 要着重阅读的内容         | 双     |
| strong | 十分重要的内容（语气比em强）  | 双     |
| span   | 没有语义，用于包裹短语的通用容器 | 双     |
| cite   | 作品标题             | 双     |
| code   | 一段代码             | 双     |
| abbr   | 缩写，最好配合title属性   | 双     |

## 1.8、图片标签

img  属性：src，alt，width，height

>常见的图片格式：
>jpg或jpeg：有损的压缩格式。占用空间小。不支持透明背景，不支持动态图
>png：无损的压缩格式，能够更高质量的保存图片。支持透明背景，不支持动态图
>bmp：不进行压缩的格式，最大程度保留图片细节，占用空间大。不支持透明背景，不支持动态图
>gif：支持颜色比较少，支持简单透明背景，支持动态图
>webp：谷歌推出的一种格式，专门用来在网页呈现图片，具备上述几种格式的有点，但兼容性不好（ie无法兼容）
>base64：把图片进行base64编码，形成一串文本，要通过浏览器打开，传统图片应用无法打开。如何使用？直接作为img标签的src属性即可，不受文件位置影响


## 1.9、超链接

作用：从当前页面进行跳转
标签 a ，常用属性：
href：要跳转的位置   
target：跳转时如何打开页面（_self：在本页签中打开（默认），_blank：在新页签中打开）
download：触发下载
跳转锚点：
```html
<!DOCTYPE html>
<html lang="zh-CN">
    <head>
        <meta charset="UTF-8">
        <title>mu</title>
    </head>
    <body>
        <a href="#ggmu">看曼联</a>
        <p>曼彻斯特联1</p>
        <p>曼彻斯特联2</p>
        <img id="ggmu" src="mu.jpg" alt="曼联">
        <a href="./test2.html#ggmu">其它页面锚点</a>
        <a href="javascript:alert(666);">执行js脚本</a>
    </body>
</html>
```

唤起指定应用：

```html
<a href="tel:13672161111">电话联系</a>
<a href="mailto:289777@qq.com">邮箱联系</a>
```

## 1.10、列表

有序列表

```html
        <ol>
            <li>第一行</li>
            <li>第二行</li>
            <li>第三行</li>
        </ol>
```

无序列表

```html
        <ul>
            <li>第一行</li>
            <li>第二行</li>
            <li>第三行</li>
        </ul>
```

自定义列表

```html
        <dl>
            <dt>标题</dt>
            <dd>第一行</dd>
            <dd>第二行</dd>
        </dl>
```

## 1.11、表格

### 1.11.1、常规表格
表格：table
表格标题：caption
表格头部：thead
表格主体：tbody
表格脚注：tfoot

```html
<table border="1">
            <!-- 表格标题 -->
            <caption>学生信息</caption>
            <!-- 表格头 -->
            <thead>
                <!-- 表格行 -->
                <tr>
                    <!-- 头部的每一列，th的h指head -->
                    <th>姓名</th>
                    <th>性别</th>
                    <th>年龄</th>
                    <th>民族</th>
                </tr>
            </thead>
            <!-- 表格主体 -->
            <tbody>
                <tr>
                    <!-- body每一列，td的d指data -->
                    <td>张三</td>
                    <td>男</td>
                    <td>十八</td>
                    <td>汉族</td>
                </tr>
                <tr>
                    <td>李四</td>
                    <td>女</td>
                    <td>十八</td>
                    <td>汉族</td>
                </tr>
            </tbody>
            <!-- 表格脚注 -->
            <tfoot>
                <tr>
                    <td></td>
                    <td></td>
                    <td></td>
                    <td>共计：2人</td>
                </tr>
            </tfoot>
        </table>
```

常用属性：
width
height
cellspacing：单元格间距
align：水平对齐
valign：垂直对齐

### 1.11.2、跨行跨列

colspan：跨列
rowspan：跨行

```html
<!-- 跨行和跨列 -->
         <table border="1" cellspacing="0">
            <caption>课程表</caption>
            <thead>
                <tr>
                    <td>项目</td>
                    <td colspan="3">上课</td>
                    <td>活动和休息</td>

                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>星期</td>
                    <td>星期一</td>
                    <td>星期二</td>
                    <td>星期三</td>
                    <td>星期四</td>
                </tr>
                <tr>
                    <td rowspan="2">上午</td>
                    <td>语文</td>
                    <td>数学</td>
                    <td>英语</td>
                    <td>体育</td>
                </tr>
                <tr>
                    <td>化学</td>
                    <td>生物</td>
                    <td>地理</td>
                    <td>历史</td>
                </tr>
                <tr>
                    <td>下午</td>
                    <td>美术</td>
                    <td>数学</td>
                    <td>政治</td>
                    <td>历史</td>
                </tr>
            </tbody>
         </table>
```


![image.png|389](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20241107172346.png)

## 1.12、表单

```html
<form action="https://search.jd.com/search" target="_blank" method="get">
        <!-- 文本输入框 -->
        <label for="zhanghu">账户：</label>
        <input id="zhanghu" type="text" name="account" value="zhangsan" maxlength="10">
        <!-- 文本输入框 - lable实现了提示文字和表单控件的关联-->
        <label >
            账户2：<input type="text" name="account2" id="zhanghu2">
        </label>
        <!-- 密码输入框 -->
        <label for="mima">密码：</label>
        <input id="mima" type="password" name="password" maxlength="6">
        <!-- 单选框 -->
        性别：<input type="radio" name="gender" value="male"> 男 <input type="radio" name="gender" value="female" checked> 女
        <!-- 复选框 -->
        爱好：<input type="checkbox" name="hobby" value="smoke">抽烟<input type="checkbox" name="hobby" value="drink">喝酒<input type="checkbox" name="hobby" value="perm" checked>烫头
        <!-- 隐藏域 -->
        <input type="hidden" name="abc" value="123">
        <!-- 确认按钮_1 -->
        <button>提交1</button>
        <!-- 确认按钮_2 -->
        <input type="submit" value="提交2">
        <!-- 重置_1 -->
        <button type="reset">重置1</button>
        <!-- 重置_2 -->
        <input type="reset" value="重置2">
        <!-- 普通按钮_1 -->
        <button type="button">普通按钮1</button>
        <!-- 普通按钮_2 -->
        <input type="button" value="普通按钮2">
        <!-- 文本域 -->
        文本域：<textarea name="other" rows="10" cols="30"></textarea>
        <!-- 下拉框 -->
         <select name="place">
            <option value="1">山西</option>
            <option value="2" selected>北京</option>
         </select>
      </form>
```

## 1.13、框架标签

```html
      <!-- 框架标签 -->
       <iframe src="https://www.bilibili.com" width="900" height="600" frameborder="0"></iframe>
       <a href="https://www.baidu.com" target="bd">点我去百度</a>
       <iframe name="bd" width="900" height="600"  frameborder="0"></iframe>
```

## 1.14、字符实体

```html
       <!-- 字符实体-空格 -->
       <div>hello &nbsp;&nbsp;world</div>
       <!-- 字符实体-大于小于 -->
       <div>&lt;h1&gt;是一个html标签</div>
       <!-- 字符实体-&amp;表示一个& -->
       <div>&amp;nbsp;</div>
       <!-- 字符实体-版权所有 -->
       <div>版权&copy;</div>
```

## 1.15、html全局属性

id：标签唯一标识
class：给标签指定类名，然后通过css就可以给标签设置样式
style：给标签设置css样式

## 1.16、meta元信息

```html
<head>
    <!-- 字符编码 -->
    <meta charset="UTF-8">
    <!-- 用户用IE8及其以上浏览器，进入edge渲染模式，更好的呈现网页 -->
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <!-- 针对移动端的配置 -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- 配置网页关键字 -->
     <meta name="keywords" content="曼联,欧冠">
     <!-- 配置网页描述信息 -->
      <meta name="description" content="曼联网站xxxxx">
    <!-- 针对搜索引擎爬虫配置 - follow:允许爬虫跟随此页面链接；index：允许爬虫；noindex：不允许爬虫-->
     <meta name="robots" content="follow">
    <title>标题</title>
</head>
```
## 1.17、其它标签

```html
    <!-- 换行 -->
    <br>
    <!-- 分割线 -->
     <hr>
     <!-- 按原文显示 -->
      <pre>
         Glory     Glory
              Man       United
      </pre>
```


# 2、css

层叠样式表：cascading style sheets

## 2.1、css编写位置

```html
<!DOCTYPE html>
<html lang="zh-CN">
    <head>
        <meta charset="UTF-8">
        <title>css样式位置</title>
        <!-- 内部样式 -->
        <style>
            h2{
                color: green;
            }
        </style>
        <!-- 外部样式 -->
        <link rel="stylesheet" href="a.css">
    </head>
    <body>
        <!-- 行内样式 -->
        <h1 style="color: brown;">css行内样式</h1>
        <!-- 内部样式 -->
        <h2>css内部样式</h2>
        <!-- 外部样式 -->
        <h3>css外部样式</h3>
    </body>
</html>
```

>样式优先级：
  行内样式 > 内部样式 = 外部样式
  内部样式和外部样式，后面的会覆盖前面的
  同一样式表中，后面的也会覆盖前面
  
## 2.2、css选择器

```html
<!DOCTYPE html>
<html lang="zh-CN">
    <head>
        <meta charset="UTF-8">
        <title>css</title>
        
        <style>
            /* 通配选择器 */
            * {
                color: orange;
            }
            /* 元素选择器 */
            h1 {
                color: red;
            }
            /* 类选择器：多个元素可以属于同一个class */
            .key1{
                color: blue;
            }
            /* id选择器: 多个元素的id属性不能重复 */
            #key2 {
                color: aqua;
            }
            /* 交集选择器 */
            p.key1 {
                color: blueviolet;
            }
            /* 并集选择器 */
            .key1,#key2,.key3{
                background-color: aquamarine;
            }
            /* 后代选择器 */
            ul li {
                color: red;
            }
            /* 子代选择器(只有儿子) */
            ul>a {
                color: yellow;
            }
            /* 兄弟选择器(紧紧相邻的兄弟) */
            li+a {
                font-size: 40px;
            }
            /* 兄弟选择器(所有的兄弟) */
            li~a {
                font-size: 40px;
            }
            /* 属性选择器:选中具有title属性的元素 */
            [title] {
                color: green;
            }
            /* 属性选择器:选中具有具体属性值的元素 */
            [title="kaka2"] {
                color: blue;
            }
            /* 属性选择器:选中具有c开头属性值的元素 */
            [title^="c"] {
                color: red;
            }
            /* 属性选择器:选中具有3结尾属性值的元素 */
            [title$="3"] {
                color: orange;
            }
            /* 属性选择器:选中包含ckaka属性值的元素 */
            [title*="ckaka"] {
                color: purple;
            }
            /* 动态伪类选择器 link visited hover active focus(表单) */
            a:visited {
                color: brown;
            }
            a:hover {
                color: blue;
            }
            input:focus {
                background-color: green;
            }
            /* 结构伪类 ul下第一个儿子li元素 */
            ul>li:first-child {
                color: chartreuse;
            }
            /* 否定伪类 */
            ul>li:not(.head){
                color: blue;
            }
            /* 目标伪类 */
            div:target {
                background-color: green;
                height: 300px;
            }
            /* 语言伪类 */
            div:lang(en) {
              color: chartreuse;
            }
            /* 伪元素选择器 */
            div::first-letter {
              color: red;
              font-size: 20px;
            }
        </style>
    </head>
    <body>
      <h1>hello,world</h1>
      <h2>hello,world</h2>
      <p class="key1">曼联</p>
      <p id="key2">mu</p>
      <p class="key3">老特拉福德</p>
      <ul>
        <li>抽烟</li>
        <li>喝酒</li>
        <li class="head">烫头</li>
        <a href="#">张三</a>
        <p>
            <a href="#">李四</a>
        </p>
        <a href="#">王五</a>
      </ul>
      <div title="kaka">卡卡</div>
      <div title="kaka2">卡卡</div>
      <div title="ckaka3">卡卡</div>
      <input type="text">
      <a href="#six">去看第六个</a>
      <div id="six">第六个</div>
      <div lang="en">first</div>
      <div>hello,Manlian</div>
    </body>
</html>

```

>选择器优先级：id选择器  > 类选择器 > 元素选择器 > 通配选择器

## 2.3、盒子模型

盒子模型组成部分：内容、内边距、边框和外边距4个部分组成

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20241109212702.png)

### 2.3.1、margin塌陷问题：

第一个元素上margin会作用在父元素上，最后一个元素下maring会作用在父元素上
如何解决？给父元素设置css样式 overflow:hidden

### 2.3.2、处理内容溢出

```css
overflow: hidden   auto   scroll   
overflow-x: hidden
overflow-y:scroll
```

### 2.3.3、隐藏元素的两种方式

```css
# 隐藏并且不占据位置
display: none
# 隐藏并且占据位置
visibility: hidden
```

### 2.3.4、元素之间空白问题

父元素设置：font-size: 0


### 2.3.5、行内块的幽灵空白问题

底部有缝隙

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .outer {
            background-color: aqua;
        }
    </style>
</head>
<body>
    <div class="outer">
        <img src="mu.jpg">
    </div>
</body>
</html>
```

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20241109224429.png)

解决方式：
方式1：
```css
       img{
            vertical-align: bottom;
        }
```

方式2：
```css
        .outer {
            background-color: aqua;
            font-size: 0;
        }
```
这种方式在后面真有文字的情况下，需要再单独设置文字块的字体大小


## 2.4、浮动

在最初，浮动是用来实现文字环绕图片效果的，现在浮动是主流的页面布局方式之一
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .outer {
            background-color: aqua;
        }
        img {
            float: left;
        }
    </style>
</head>
<body>
    <div class="outer">
        <img src="mu.jpg">
        <span>曼彻斯特联足球俱乐部（Manchester United F.C.，中文简称“曼联”，英文简称“Man Utd”或“MUFC”）是一家位于英国西北区大曼彻斯特郡[曼彻斯特].....</span>
    </div>
</body>
</html>
```


![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20241110085030.png)


### 2.4.1、元素浮动后的特点
1、脱离文档流
2、不管浮动前是什么元素，浮动后，默认宽高都被内容撑开（尽可能小），而且可以设置宽高
3、不会独占一行，可以与其它元素公用一行
4、不会margin合并，也不会margin塌陷，能够完美设置四个方向的margin和padding
5、不会像行内块一样被当作文本处理（没有行内块的空白问题）

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20241110085152.png)



### 2.4.2、浮动后的影响

对兄弟元素的影响：后面的兄弟元素，会占据浮动元素之前的位置，在浮动元素下面；对前面的兄弟无影响
对父元素的影响：不能撑起父元素的高度，导致父元素高度塌陷；但父元素的宽度依然束缚浮动元素

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .outer {
            background-color: gray;
            border: 1px solid black;
            /* overflow: hidden用途  1) 隐藏超出父元素的内容，保持布局整洁；2) 清除浮动，使得父元素能包围其内部浮动的子元素；3) 解除margin坍塌，避免子元素外边距导致的布局问题 */
            overflow: hidden;
        }
        .box {
            float: left;
            width: 100px;
            height: 100px;
            background-color: red;
        }
    </style>
</head>
<body>
    <div class="outer">
        <div class="box box1">1</div>
        <div class="box box2">2</div>
        <div class="box box3">3</div>
        <div class="box box4">4</div>
    </div>
</body>
</html>
```

解决：

1、给父元素设置overflow:hidden

2、给浮动元素的父元素，设置伪元素，通过伪元素清除浮动

```css
.outer::after {
	content: '';
	display: block;
	clear: both;
}
```

>布局中的一个原则：设置浮动的时候，兄弟元素要么全浮动，要么全不浮动

## 2.5、定位

### 2.5.1、相对定位
相对自己原来的位置。给元素设置 position:relative即可实现相对定位
可以使用 left right top bottom四个属性调整位置

相对定位的特点？
1、不会脱离文档流，元素位置的变化只是视觉效果的变化，不会对其它元素产生任何影响
2、定位元素显示层级比普通元素高

 >绝大多数情况下，相对定位会与绝对定位配合使用
 
```css
 .box2 {
            position: relative;
            left: 50px;
        }
```