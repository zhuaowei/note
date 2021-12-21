# Java高级

## JVM

### 一、class 加载过程

class 加载有4大过程：

1、loading

这是类的文件从硬盘加载到内存中的过程。

2、linking

这部分有分为3个小部分

​	2.1、verification

​		校验：验证class文件是否符合JVM的规范。

​	2.2、perparation

​		准备：class的静态变量赋值为默认值。

​	2.3、resolution

​		解析：将符号引用转换为直接引用，就是可以直接找到的，直接使用的。

​		例如构造方法、private方法。多态的方法是不行的。

3、initializing

这个是类的初始化，不是对象的初始化。在这一步会为静态变量赋值为初始值。

4、GC回收

>   默认值 int类型为 0，对象为null