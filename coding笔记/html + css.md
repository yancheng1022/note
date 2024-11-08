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



