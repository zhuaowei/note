# Python

人生苦短，我用Python

## Python起源

### 1.1解释器

编译器：将其他语言翻译成机器语言的工具

编译器翻译方式：1、编译 2、解释

区别在于翻译时间不同

编译性语言更快

Python是一门跨平台语言，只需在相应平台安装不同的解释器

### 1.2Python的设计目标

1、简单直观、强大

2、开源

3、容易理解

4、适用于短期开发

### 1.3 Python 的设计哲学

1、优雅

2、明确

3、简单

用一种方法，最好是只有一种方法来做一件事

拒绝花哨，选择明确，没有歧义或少歧义的一种

2. ## 为什么选择Python？

代码量少

3. ## Python特点

1、完全面向对象

> 函数、模块、数字、字符串等都是对象，在 Python 中，一切皆对象
>
> 完全支持继承、重载、多重继承
>
> 支持重载预算符，也支持泛型设计

2、拥有强大的标准库

3、大量第三方模块

面向对象的思维方式

> 面向对象是一种思维方式，也是一门程序设计技术
>

4. Python的优缺点

4.1 优点

简单、易学

免费、开源

面向对象、

丰富的库

可扩展性 => 可以在Python中运行 C/C++

4.2 缺点

运行速度慢

国内市场小

中文资料匮乏

## 第一个Python程序

<hr>

### 1、第一个程序

1.1 Python 源程序的基本概念

Python源程序就是一个特殊格式的文本文件

1.2

```python
# print 函数 -> 打印引号中的内容
print("Hello, python")
```

在终端中执行  Python 程序

```
python 01.py
```

1.3 错误 => 不能执行或结果不是我们想要的

1. 手误
2. 对知识理解不足
3. 还有提升内容

缩进错误

Python 2.x 默认不支持中文

![image-20200305201228015](.\image-20200305201228015.png)

2、建议使用 Python 3.x 版本

Python 3.x 未来主流版本

不向下兼容

过渡版本 Python 2.6、2.7

3、执行 Python 程序的三种方式

3.1 解释器 Python/ python 3

python 的解释器

```
# 使用 Python 2.x 解释器
Python xxx.py

# 使用 Python 3.x 解释器
Python3 xxx.py
```

3.2 交互式

输入代码直接出结果

1、优点

适合验证/学习 Python 语法或局部代码

2、缺点

不能保存

不适合运行大程序

3、退出Shell

```
# 直接输入命令
>>> exit()

# 使用热键退出
Ctrl + D
```

3.3 Python 的 IDE -- pycharm

1、集成开发环境 -- 集成了开发软件需要的所有工具

一般有：

- 图形用户界面
- 代码编译器（支持代码补全，自动缩进）
- 编译器/解释器
- 调试器（断电/单步执行）
- ......

## 语法

### 注释

#### 1、注释 -- 增强可读性

- 单行注释

以 **#** 开头

**可在代码后写注释**

- 多行注释

#### 2、多行注释

```python
'''
三个引号
添加多行注释
'''
"""
也可以是这种引号
"""
```

Python注释规范

谷歌文档

> https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_style_rules/

Python官方文档

> https://www.python.org/dev/peps/pep-0008/

### 算数运算符

1、算数运算符

- 运算符的一种
- 处理四则运算

```
+	加
-	减
*	乘
/	除
//	取整除		   9 // 2 = 4
%	取余
**	幂			2 ** 3 = 8
```

- 可用于字符串

"a" * 10

=> aaaaaaaaaa

2、算数运算符的优先级

与数学中一致

同级从左至右，可用 () 调整顺序

### 程序执行原理



Python执行原理



> python 解释器占用内存很小，大概3、4M

### 程序的作用 -- **处理数据**

变量是用来存储数据的

### 变量的基本使用

1、变量定义

变量在使用前必须赋值，变量赋值后才能被创建

用等号（=）来赋值

> 变量名 = 值

2、变量的类型

根据变量右侧的值自动推导数据的类型

数字型

- int、long整型
- bool 布尔类型（ 0 / 1 ）
  - true 对应 1
  - false 对应 0
- float 浮点型
- complex 复数型

非数字型

- 字符串	str

- 列表

- 元组

- 字典

使用 **type 函数**可以查看一个变量的类型

```
type(name)
```

#### 不同类型变量之间的计算

1、数字型之间可以直接计算

2、字符串变量之间使用  +  拼接字符串

3、字符串变量可以通过  *  一个整数重复相同的字符串

4、数字型变量和字符串不能进行其他计算

#### 变量的输入 -->  input 函数

用户输入的任何内容， python 都认为是一个字符串

```
字符串变量 = input("提示信息")
```

#### 类型转化函数

```
# 将 x 转换为一个函数
int(x)

# 将 x 转换为一个浮点数
float(x)
```

获取数字型变量很不方便，可以使用以下格式

```
price = float(input("提示信息"))
```

#### 变量的格式化输出

%   格式化操作符

与 C中相同

%% -> 输出 %

语法格式如下：

```
print("%d" % x)
print("%d%d" % (a, b, c))
print("%d" % (a * 10))
```

### 变量的命名

1、标识符  =>  变量名、函数名

1.1名字需要见名知义

- 标识符可以由  字母 下划线 和数字组成
- 不能以数字开头
- 不能与关键字重名

1.2 关键字

在 python 内部已经使用的标识符

关键字具有特殊的功能和含义

通过以下命令查看 python 中的关键字

```
import keyword
print(keyword.kwlist)
```

- import  可以导入一个“工具包”

2、变量命名规则 -->  不强制，只是增强代码的可读性

标识符区分大小写

- 匈牙利命名法
- 骆驼命名法
- 帕斯卡命名法
- [Google C++官方文档](https://zh-google-styleguide.readthedocs.io/en/latest/google-cpp-styleguide/naming/)

### 语句

#### 判断语句 （if）

<hr>

格式

```python
if 条件:
	条件成立时要做的事
```

TAB 键 和 空格 不要混用

**python 2.x 中 判断不等于还可以用 <>，也可以用 !=**

> if 语句及缩进部分是一个完整的代码块

else 处理条件不满足的情况

```
if 条件:
	A
	...
else:
	B
	...
```

> if else 可以看成一个代码块

#### 逻辑运算

<hr>

- 与  -->  and
- 或  -->  or
- 非  -->  not

#### if 语句进阶

> elif 相当于 else if

if 的嵌套

与 C中一样，**注意缩进**

#### 随机数的使用

首先导入随机数的模块 -- “工具包’

```python
import random
```

导入模块后，在模块后敲一个 TAB 键会提示该模块中包含的所有函数

返回 [a, b] 之间的整数，包含 a, b

a 必须小于等于 b

> random.randint(a, b)

#### 循环

程序三大流程

1、顺序 --> 从上到下

2、分支 -- > 根据句条件判断，决定分支

3、循环 --> 特定代码重复执行

##### while

```
while 条件：
	语句满足时执行的语句
	...
```

循环结束后，之前定义的计数器的值是存在的

#### 赋值运算符

```
=
+=
-=
*=
/=
//=
%=
**=
```

#### break 和 continue

默认情况下，print() 在输出内容后自动换行，如果不想换行

```
print("***", end = "")

# "" 中可以指定 print() 在输出后继续显示的内容
```

### 函数

1、函数的定义

格式

```
def 函数名():
	函数封装的代码
	...
```

必须定义函数后才能使用

> 在函数定义上方要保留两个空行
>
> 在函数名下方写注释

```


def 函数名():
	""""注释"""
	...
```

函数的参数的使用

以求和函数为例

```
def sum(num1, num2):
	result = num1 + num2
	print(result)
```

形参：定义函数时小括号中的参数

实参：调用函数时小括号中的参数

返回值用 return 

> return 后面的代码都不会被执行

```python
def sum(num1, num2):
	result = num1 + num2
	return num1 + num2
	
return = sum(a, b)
```

函数的嵌套调用

与C中相同

```python
def line(char):
	print(char * 50)
	
line("-")
```

### 模块

> python 程序架构的一个核心概念

- 模块好比工具包，要使用，先导入

- 每一个以 .py 为扩展名的python源代码文件都是一个模块

- 在模块中定义的全局变量、函数都是模块能够提供给外界直接使用的工具

6.1 第一个模块体验

导入模块

```python
import 模块 as 名字
#名字是为了方便使用，自定义
```

可以在模块中定义变量或函数

使用格式

> 模块名.变量
>
> 模块名.函数

6.2 模块名也是一个标识符

- 不能以数字开头
- 不能与关键字重名

6.3 pyc 文件（了解）

> C 是 compled 编译过的意思



### 高级变量类型

- 数字型

  - 整形

  - 浮点型

  - 布尔型

    - 真 True  非零数  -- 非零即真

    - 假 False  0

  - 复数型

    - 主要用于科学计算

- 非数字型
  - 字符串
  - 列表
  - 元组
  - 字典

- 在 python 中，所有非数字型变量都支持以下特点

1. 都是一个序列 sequence，也可以理解为容器
2. 取值 []
3. 遍历  for in
4. 计算长度、最大值、最小值、比较、删除
5. 链接 + 和 重复 *
6. 切片

#### 1、列表（C 中的数组）

1.1定义

```python
name = []
name_list = ["zhangsan", "lisi", "wangwu"]
```

> 输入 **name_list.**，接下来按 **Tab 键**会提示列表能够执行的操作

常用操作

![image-20200310174341407](E:\Documents\笔记\image-20200310174341407.png)

```
name_list = ["zhangsan", "lisi", "wangwu"]
# 1、取值（同 C）
name_list[0]

# 取索引
# 用 index 要注意，如果数据不在列表中，程序会报错
name_list.index("lisi")

# 2、修改（不能超出范围）
name_list[1] = "李四"

# 3、添加
# 追加
name_list.append("zhaoliu")

# 插入（在列表指定索引位置擦汗如数据）
name_list.insert(1, "hehe")

temp_list = [ "q", "w", "e" ]
# 扩展(在末尾追加其他数组的内容)
name_list.extend("temp_list")

# 4、删除
# 删除列表中第一次出现的数据
name_list.remove("wangwu")

# pop
# 不添加数据，默认删除最后一个
name_list.pop()
# 使用下标，指定删除
name_list.pop(3)

# 清空
name_list.clear()
```

del （本质上从内存中删除，在列表中不建议使用）

```python 
del name_list[1]

# 在后续代码中不可再用此数据
del name
```

len  --  检查元素个数

```python 
name_list = [ "q", "w", "e" ]
list_len = len(name_list)
print("%d" % list_len)
```

count -- 统计元素出现次数

```python
count = name_list.count("q")
```

sort  -- 排序

```python
num_list = [ 1, 2, 3, 4, 5 ]
# 升序
num_list.sort()

# 降序
num_list.sort(reverse=True)

# 逆序
num_list.reverse()
```

关键字不需要使用小括号

#### 循环遍历

迭代遍历

```python
num = [ 1, 2, 3, 4, 5 ]

'''
for 循环内部使用的变量 in 列表
	......
'''

for i in num:
    print("%d" % num[i])
```

> python 中允许在列表中存储不同类型的数据
>
> 但最好不要用

### 2、元组

与列表类似，元组定义完成不能修改

2.1 定义  -- 用 () 定义，下标从 0 开始

```python
info = ( "zhangsan", 1, 1.5 )
```

创建空元组

```
info = ()
```

元组中只有一个元素时，需要在元素后面添加逗号

```
info = (1,)
```

2.2 元组有关的操作

取值和取索引

```python
# 取值
print(info[0])

# 取索引
print(info.index("zhangsan"))

# 统计次数
print(info.count("zhangsan"))

# 统计元组中元素个数
print(len(info))
```

2.3 循环遍历(需求不多)

```
for i in info:
	print()
```

2.4 应用场景

- 函数的参数的返回值，一个函数可以接受任意多个参数，或者一次返回多个数据
- 格式字符串
- 让列表不被修改，保护数据安全

```
info = ("zhangsan", 18)
print("%s 的年龄是 %d" % info)
```

元组与列表之间的转换

```
# 使用 list 函数可以把元组转换为列表
list(元组)

# 使用 tuple 函数可以把列表转换为元组
tuple(列表)
```

### 3、字典：存储无序数据的集合

键值对

键是索引

值是数据

用 ==：== 分隔

键必须是唯一的

值可以取任何数据类型

但是键只能是字符串、数字或元组

```python
xiaoming  = {"name" : "xiaoming",
             "age" : 18,
             "gender" : True,
             "heiht" : 60,
             "hight" : 175 }
print(xiaoming)
```

操作：

```
# 取值
print(xaioming["name"])

# 增加
xiaoming["age"] = 18
# 修改
xiaoming["age"] = 19

# 删除
xiaoming.pop("age")
```

常用

```python
xiaoming_dict = {"name" : "xiaoming",
                 "age" : 18}
temp_dict = {"height" : 175,
             "age" : 20}
# 统计键值对的数量
print(len(xiaoming_dict))

# 合并字典
xiaoming_dict.update(temp_dict)
# 如果被合并的字典中包含已存在键值对，
# 会覆盖原有的键值对

# 清空键值对
xiaoming_dict.clear()
```

遍历

```python
 
```

