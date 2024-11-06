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
