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

## 1.12、其它标签

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

