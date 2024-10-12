# 1、python环境（miniconda + pycharm）

地址： https://repo.anaconda.com/miniconda/Miniconda3-latest-Windows-x86_64.exe
环境变量：
```
C:\ProgramData\miniconda3 
C:\ProgramData\miniconda3\Scripts 
C:\ProgramData\miniconda3\Library\bin 
```


# 2、python程序的基本格式

## 2.1、缩进

缩进风格统一：每个缩进层次使用单个制表符或四个空格（idea会自动将制表符设置成4个空格）
python用缩进而不是{}表示程序块的层次关系
python区分大小写

## 2.2、注释


```python
# 单行注释  #
print("hello world")  
print("你好，世界")  
  
'''  
多行注释  
'''  
print("hello world")  
print("你好，世界")  
  
"""  
多行注释 
"""  
print("hello world")  
print("你好，世界")
```

## 2.3、基本构成

python程序    --->   模块（一个模块对应一个源文件）    --->   语句


```python
# 代码过长时可以使用行连接符 \
a = 'abcd\  
    bcdef\  
    cdfd'
```

# 3、对象的基本组成和内存示意

对象 = 标识（id）  +  类型（type）  +  值（value）

标识用于唯一标识对象，通常对应于对象在计算机内存中的地址。使用内置函数id(obj)可返回对象obj的标识。
类型用于表示对象存储的“数据”的类型。类型可以限制对象的取值范围以及可执行的操作。可以使用type(obj)获得对象的所属类型。
值表示对象所存储的数据的信息。使用print(obj)可以直接打印出值。

# 4、python程序的构成

## 4.1、标识符规则

1、区分大小写
2、第一个字符必须是字母、下划线。其后的字符是字母，数字，下划线
3、不能使用关键字，如if，or，while等
4、以双下划线开头和结尾的名称通常具有特殊含义，尽量避免这种写法


开发中，通常约定俗成如下规则：

