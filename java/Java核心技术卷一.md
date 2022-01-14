# Java核心技术

## 1、

### 运行

在java虚拟机里运行，**.class 文件

### package

就是文件夹

### 注释

```java
// 单行注释
/*
段落注释
*/
/**
*类注释
*DOC文档
*/
```

## 2、

### java数据类型（一带而过）

与 C 类似

特殊 String



引用类型

常量 final

自动类型转换

强制类型转换

全局变量、局部变量、实例变量

修饰符

运算符

操作符

逻辑运算符

条件运算符

条件语句

if else

for

while

do while

增强for循环

switch case

### String

定义字符串

```java 
String str_1 = "hello";
// String S 要大写

// 获取字符串长度
str_1.length();
System.out.println("str_1 -> length : " + str_1.length())
```

### StringBuffer/StringBuilder

对于经常变动的字符串，应该使用StringBuffer或StringBuilder.

区别：StringBuilder线程不安全，但是速度稍快，StringBuffer线程安全，但速度稍慢。在多线程程序中使用StringBuffer。

方法：

```java
// StringBuilder
appand(str);
insert(index, str);
delete(index1, index2); // [index1, index2)

// StringBuffer
appand(str);
insert(index, str);
delete(index1, index2); // [index1, index2)
replace(index1, index2, str);
```







## 第三章 Java的基本程序设计结构

- Java中的数组和C语言中的数组大致相同，有一些新的特性

```java
// 声明一个数组
	int[] arr = new int[10];
```

- 声明数组时，如果知道数组的元素，可以不用 new

```java
        int[][] balances = {
                { 1, 2, 3 },
                { 4, 5, 6 },
                { 7, 8, 9 }
        };
```



- for each 循环

```java
		/*  foreach不能遍历二维数组的每一个元素
		**  二维数组需要用两层 for each
        **  结构 ：for (类型 变量名1 : 变量名2) 循环体
        **  变量名1 储存 变量名2 中的每一个元素
        **  balances 是一个二维数组
        */  
        for (int[] i: balances) {
            for (int j: i) {
                System.out.println(j);
            }
        }
```

- 不规则数组

```java
/* 声明一个不规则数组 */
	// 声明有多少行不规则数组
	int[][] odds = new int[10 + 1];
	// 分配这些行
	for (int n = 0; n < 10; n++) {
        odds[n] = new int[n + 1];
    }
/* 这样就得到一个三角形的二维数组
** ！！！不要超过数组的边界
*/
```

## 第四章 对象与类

### 4.1 面向对象编程概述

- 类Class
- 封装
- 对象
- 识别类 

> 在创建类和方法时，用名词创建类，用动词创建方法（C语言中叫函数）

- 类之间的关系

1. 依赖 -> “uses-a”

>  用一个类的方法操纵另一个类的对象
>
> PS：尽可能地减少依赖

2. 聚合（关联） -> “has-a”

> 类A的对象包含类B的对象

3. 继承 -> "is-a"（OOP三大特性之一）

> 一般而言， 如果类 A 扩展类 B, 类 A 不但包含从类 B 继承的方法，还会 拥有一些额外的功能

### 4.2 使用预定义类

- 对象与对象变量

要想使用对象，就必须首先构造对象， 并指定其初始状态。然后，对对象应用方法。

- 构造器

构造器的名字应该与类名相同。因此 Date 类的构造器名为 Date。要想构造一个 Date 对 象， 需要在构造器前面加上 new 操作符

```java
new Date();
```

这个表达式构造了一个新对象。这个对象被初始化为当前的日期和时间。

 如果需要的话， 也可以将这个对象传递给一个方法：

```java
System.out.println(new Date());
String s = new Date().toString();
```

通常， 希望构造的对象可以多次使用， 因 此，需要将对象存放在一个变量中：

```java
Date birthday = new Date();
```

```java
Date deadline; // deadline doesn't refer to any object
/* 定义了一个对象变量 deadline, 它 可 以 引 用 Date 类型的对象。但是，一定要认识到： ** 变量 deadline 不是一个对象， 实际上也没有引用对象。此时，不能将任何 Date 方法应用** 于这个变量上。语句
*/
s = deadline.toString(); // not yet
// 将产生编译错误。
```

初始化 deadline 有两种方法：

```java
// 第一种方法
deadline = new Date();

// 第二种方法，引用一个已存在的对象
deadline = birthday;
```

表达式 new Date() 构造了一个 Date 类型的对象， 并且它的值是对新创建对象的 引用。这个引用存储在变量 deadline 中。

```java
Date deadline = new Date();
```

将对象变量设置为null，表明这个对象变量没有引用任何对象

如果对其应用方法会报错

```java
deadline = null;
```

PS：局部变量不会自动地初始化为 null，而必须通过调用 new 或将它们设置为 null 进行初始化。

- java类库中的LocalDate类

Date 类的实例有一个状态， 即特定的时间点。

以标准 Java 类库分别包含了两个类： 一个是用来表示时间点的 Date 类；另一个是用来表示大家熟悉的日历表示法的 LocalDate 类。

- 静态工厂方法代表你调用构造器

```java
LocalDate.now();
// 构造一个新对象，表示构造时的日期
```

将构造的对象保存到对象变量中

```java
LocalDate newYearsEve = LocalDate.of(2001, );
```

一旦有 了一个 LocalDate 对象， 可以用方法 getYear、 getMonthValue 和 getDayOfMonth 得到年、月和日：

```java
int year = newYearsEve.getYear();	// 2001
int month = newYearsEve.getMonth();	// 5
int day = newYearsEve.getDay();		// 21
```

- plus Days 方法

```java
// 当前对象的日期加上指定天数
LocalDate aDayLater = newYearsEve.plusDays(1);
/* plusDays 方法会生成一个新的 LocalDate 对象，然后把这个新对象赋给aThousandDaysLater变量。原来的对象不做任何改动。*/

int year = aDayLater.getYear();	// 2001
int month = aDayLater.getMonth();	// 5
int day = aDayLater.getDay();		// 22
```

PS：

1、封装的意义在于，这一点并不重要， 重要的是类对外提供的方法。

2、不推荐使用 get Day、get Month、getYear方法

- 更改器方法与访问器方法

> 改变对象状态的方法叫做更改器方法(mutator method)
>
> 只访问对象而不修改对象的方法有时称为访问器方法(accessor method)

- java.time.LocalDate 8

```java
static Local Time now( )
// 构造一个表示当前日期的对象。
static LocalTime of(int year, int month , int day )
// 构造一个表示给定日期的对象。
int getYear( )
int getMonthValue( )
int getDayOfMonth( )
// 得到当前日期的年、 月和曰。

DayOfWeek getDayOfWeek
// 得到当前日期是星期几， 作为 DayOfWeek 类的一个实例返回。 调用 getValue 来得到
// 1 ~ 7 之间的一个数， 表示这是星期几， 1 表示星期一， 7 表示星期日。
Local Date piusDays(int n )
Local Date minusDays(int n)
// 生成当前日期之后或之前 n 天的日期。
```

### 4.3 用户自定义类

主力类（workhorse class）：通常， 这些类没有 main 方法， 却有自己的实例域和实例方法。

- employee 类

最简单的类定义形式

```java
class ClassName {
    field1
    field2
    ...
    constructor1
    constructor2
    ...
    method1
    method2
    ...
}
```

eg:

```java
class Employee {
    // instance fields
    private String name;
    private double salary;
    private LocalDate hireDay;
    
    // constuctor
    public Employee(String n, double s, int year, int month, int day) {
        name = n;
        salary = s;
        hireDay = LocalDate.of(year, month, day);
    }
    
    // a method
    public String getName() {
        return name;
    }
    
    // more methods
    // Getter
    // Setter
}
```

PS：文件名必须与public类的名字相匹配。

- 多个源文件的使用

1. 使用通配符  *

```
javac Employee*.java
```

2. java编译器会自动搜索程序中使用的类并编译

- public

> 关键字 public 意味着任何类的任何方法都可以调用这 些方法

- private

> 关键字 private 确保只有 Employee 类自身的方法能够访问这些实例域， 而其他类的方法不能 够读写这些域。

- 构造器

> 构造器与类同名。在构造 Employee 类的对象时， 构造器会运行，以便将实例域 初始化为所希望的状态。

> 构造器与其他的方法有一个重要的不同。构造器总是伴随着 new 操作符的执行被调用， 而不能对一个已经存在的对象调用构造器来达到重新设置实例域的目的。

- 隐式参数和显示参数

```java
public void raiseSalary(double byPercent) {
    double raise = salary * byPercent / 100;
    salary += raise;
}
// 隐式参数是出现在方法名前的 Employee 对象
// 显示参数指方法名后括号里的数值 (double byPercent)
```

- 关键字 this 表示隐式参数。

```java
public void raiseSalary(double byPercent) {
    double raise = this.salary * byPercent / 100;
    this.salary += raise;
}
// 建议这样写
```

- 封装的优点

> 1. 可以改变内部实现，除了该类的方法之外，不会影响其他代码。
> 2. 更改器方法可以执行错误检查， 然而直接对域 进行赋值将不会进行这些处理。

- 基于类的访问权限



- 私有方法

> 将关键字 public 改为 private

- final 实例域

> 构建对象时必须初始化这样的域。也就是说， 必须确保在每 一个构造器执行之后，这个域的值被设置， 并且在后面的操作中， 不能够再对它进行修改。

### 4.4 静态域与静态方法

- 静态域

> 将域定义为 static，每个类中只有一个这样的域。而每一个对象对于所有的实例域 却都有自己的一份拷贝。

```java
class Employee {
    private static int nextId = 1;
    /* 所有的 Employee 类共享一个 nextId
    ** 即使没有一个 Employee 类，nextId 也存在
    ** 他属于类，而不属于任何独立的对象
    */
    private int id;
    /* 每个 Employee 类都有一个 id，值不同 */
}
```

- 静态常量（常用）

例如：

```java
public class Math {
    public static final double PI = 3.14159265358979323846;
}

public class System {
    public static final PrintStream out = ...;
}
```

> 如果没有 static ，PI就是一个实例域。需要通过Math类的对象访问PI，并且每个Math 对象都有它自己的一份 PI 拷贝。

> 每个公有域都可以对公有域进行修改，最好不要将域设计为public。
>
> 对于 final 可以这样写。

- 静态方法

> 一种不能向对象实施操作的方法。
>
> 例如，Math 类的 pow 方法就是一个静态方法。(没有隐式的参数)
>
> Math.pow(x, a)

> 静态方法不能访问实例域，因为它不能操作对象。
>
> 静态方法可以访问自身类中的静态域。

- 工厂方法



- main方法

> main 方法也是一个静态方法。

```java
public class Application {
    public static void mian(String[] args) {
        // ...
    }
}
```

### 4.5 方法参数

> java总是按值调用，方法得到的是参数值的一个拷贝
>
> 然而，方法参数有两种类型
>
> 1. 基本数据类型
> 2. 对象引用
>
> 将对象作为参数传入方法可以改变对象成员的值

```java
public class ParamTest {
	public static void main(String [] args){
		System.out.println("\nTesting tripleSalary:");
		Employee harry = new Employee("Harry",50000);
		System.out.println("Before: salary=" + harry.getSalary());
		tripleSalary(harry);
		System.out.println("After: salary=" + harry.getSalary());
		
		
	}
	public static void tripleSalary(Employee x){
	x.raiseSalary(200);
	System.out.println("End of method: salary=" + x.getSalary());
	}
 
}
public class Employee {
	private String name;
	private double salary;
	
	public Employee(String n,double s){
		name = n;
		salary = s;
	}
 
	public String getName() {
		return name;
	}
	public double getSalary() {
		return salary;
	}
    public void raiseSalary(double byPercent){
    	double raise = salary*byPercent/100;
    	salary+=raise;	
    }
}
```

### 4.6  对象构造

- 重载

> Java 允许重载任何方法重载，不只是构造器方法。
>
> 重载允许两个方法拥有不同的参数类型和不同的参数数量
>
> 但是返回值必须相同。

- 默认域初始化

如果在构造器中没有显示地给域赋初值，那么就会被自动地赋为默认值：

数值为0，布尔值为false，对象引用为null

如果在编写类时，没有编写构造器，那么系统就会提供一个无参的构造器。这个构造器会将会将所有的实例域设置为默认值。

只有在类中没有构造器时，才会提供一个默认的构造器。

- 显示域初始化

在类定义中，直接将一个值赋给任何域。

在执行构造器之前，限制性赋值操作。适用于将相同的值赋予某个特定的实例域

- 参数名

用单个字母命名方法参数，在不阅读源码时不知道其含义，可以在同意义名字面前加 a。

另外，参数变量用同样的名字，将实例域屏蔽起来。可以用 this.salary 的形式访问实例域。

```java
public Employee(String name, double salary) {
    this.name = name;
    this.salary = salary;
}
```

- 调用另一个构造器

如果构造器的第一个语句形如this()，这个构造器将调用同一个类的另一个构造器。

```java
public Employee(double) {
    // 调用 Employee(String, double)
    this("Employee #" + nextId, s);
    nextId++;
}
```

- 初始化块

除了在构造器中设置值和在声明中赋值，还有第三方法赋值，叫做初始化块。

```java
class Employee {
    private static int nextId;
    
    private int id;
    private String name;
    private double salary;
    
    // 初始化块，一个类的声明中，可以包含多个代码块
    {
        id = nextId;
        nextId++;
    }
    
    public Employee(String n, double s)
    {
    	name = "n";
        salary = s;
    }
    
    public Employee() {
        name = "";
        salary = 0;
    }
    
}
```

静态初始化块

```java
// 静态初始化块
static {
    Random generator = new Random();
    nextId = generator.nextInt(10000);
    // 将 nextId 初始化为小于 10000 的随机整数
}
```

- 对象析构域 finalize 方法

Java有自动的垃圾回收期，不需要人工回收内存，所以java不支持析构器。

Runtime.addShutdownHook

### 4.7 包

Java 允许使用包（package）将类组织起来。打包

- 类的导入

> 一个类可以使用所属包里的所有类，以及其他包中的公有类（public class）。

可以使用两种方式访问另一个包中的公有类

1. 在每个类名之前添加完整的报名

```java
java.time.LocalDate today = java.time.LocalDate.now();
```

2. 使用 import 语句引用包含在包中的类。

   可以导入一个特定的类或整个包，放在源文件的顶部，但位于 package的底部。

```java
import java.util.*;

import java.time.LocalDate;
```

```java
// 只能用 (*) 导入一个包，而不能用 import java.* 或 import java.*.* 导入以 java 开头的所有包
```

如果使用的两个包中有相同名字的类，可以这样区分：

```java
import java.util.*;
import java.sql.*;
import java.util.Date;

// 如果两个类都要使用，可以在类名之前加上完整的包名
java.util.date deadline = new java.until.Date();
java.sql.Date today = new java.sql.Date();
```

- 静态导入

> 导入静态方法和静态域

```java
import static java.lang.System.*;
// 这样就可以使用 System 类的静态方法和静态域，而不用加类名前缀

out.println("Goodbye, world!");
exit(0);	// System.exit
```

- 将类放入包中

要将一个类放入包中，就必须将包的名字放在源文件的开头，包中定义类的代码之前。

```java
package com.horstmanm.corejava;

public class Employee {
    
}
```

如果没有加 package 语句，这个源文件中的类就被放在一个默认包（default package）中

从基目录编译和运行类

````
java com/mycompany/Demo.java
java com/mycompany/Demo.class
````

- 包作用域

标记为 public 的部分可以被任意类使用；

标记为 private 的部分职能被定义他们的类使用。

如果没有指定 public 和private ，这个部分（类、方法或变量）可以被同一个包中的所有方法访问。

### 4.8 类路径

类的路径必须与包名匹配。

另外，类文件也可以存储在JAR（Java归档）文件中

路径:

> /home/user/classdir/com/horstmann/Employee.java

- 设置类路径

最好用 -classpath 或 -cp 选项指定路径

### 4.9 文档注释

javadoc

以 /** 开头

- 注释的插入

> /\**......\*/

之后紧跟自由文本，标记由@开始

- 类注释

类注释必须放在import语句之后，类定义之前

- 方法注释

除了通用标记以外，还可以使用下面的标记

@param 变量描述

> 可以使用多行也可以使用HTML标记
>
> 一个方法的所有@param标记必须放在一起

@return 描述

> 可以跨行，可以使用HTML

@throws 类描述

> 添加注释，表示这个方法有可能抛出一个异常

- 域注释

只需对公有域（通常指静态变量）建立文档

- 通用注释

1. @author 姓名

可以使用多个

2. @version 文本

对当前版本的任何描述

3. @since 文本

可以是对引入特性的版本描述

4. @deprecated 文本

这个标记将对类、方法或变量添加一个不再使用的注释。

例如：

```html
@deprecated Use <code> setVersible(true) </code> instead
```

通过@see 和@link 标记，可以使用超级链接，链接到Javadoc文档的相关部分或外部文档

5. @see 引用

可用于类中，方法中。这个标记将在“see also” 部分增加一个超级链接

- 包与概述注释

产生包注释，需要在包目录添加一个单独的文件

1. 以package.html命名的文件。在<body>...</body>之间的所右文本都会被提取出来。
2. ) 提供一个以 package-info.java 命名的 Java 文件。这个文件必须包含一个初始的以 /** 和 */ 界定的 Javadoc 注释， 跟随在一个包语句之后。它不应该包含更多的代码或注释。

- 注释的抽取

1. 必须切换到包含子目录的com的目录
2. 如果是一个包，

```shell
javadoc -d docDir nameOfPackage
```

对于多个包

```shell
javadoc -d docDir name1 name2
```

如果文件在默认包中

```shell
javaodoc -d docDir *.java
```

如果省略 -d docDir 文件会被提取到当前目录下

添加超链接

```shell
javadoc -link http://docs.oracle.eom/:javase/8/docs/api *.java
```

- 类设计技巧

1. 保证数据私有
2. 一定要对数据初始化
3. 不要在类中使用过多基本类型，（用其他类替代）
4. 不是所有的域都需要独立的域访问器或域更改器
5. 将职责过多的类分解
6. 类名和方法名要能体现职责
7. 优先使用不可变的类

## 第五章 继承



### 5.1 类、超类和子类

- 定义子类

用关键字 extends 表示继承

```java
public class Manager extends Employee {
    
}
```

子类可以使用父类中的方法（自动继承父类的方法）

只需在子类说明与父类的不同之处

- 覆盖方法

只有父类的方法才能访问其私有部分。所以子类要使用共有的接口才能访问私有域。

要使用特定的关键字 super

```java
public double getSalary() {
    double baseSalary = super.getSalary();
    return baseSalary + bonus;
}
```

可以在子类中添加域、增加方法、覆盖方法，但是绝对不能删除继承的任何域和方法。

- 子类构造器

可以调用父类的构造器，必须在第一句调用

```java
public Manager(String name, double salary, int year, int month, int day) {
    super(name, salary, year, month, day);
    bonus = 0;
}
```

如果子类没有显示地调用父类的构造器，将默认地调用超类默认（无参）构造器

如果父类没有无参构造器，且子类没有调用任何父类构造器，则会报错。

一个对象可以指示多种实例的现象称为多态，系统会默认选择调用哪个方法的现象称为动态绑定。

- 继承层次

由一个父类派生出的所有类的集合被称为继承层次

继承路径称为继承链

java不支持多继承

- 多态

子类的对象也是超类的对象，反之不然

父类实例不能调用子类方法

- 理解方法调用

重载解析

静态绑定

动态绑定

- 阻止继承：final 类和方法

被标记为 final 的类不能被继承，方法不能被覆盖。

- 强制类型转换

```java
double x = 3.045;
int nx = (int)x; // 舍弃小数部分
```

对象也可以进行类型转换，允许子类赋给超类，反之可能会报错。

使用 instanceof 查看能否转换成功

```java
if (staff[1] instanceof Manager) {
    boss = (Manager)staff[1];
}
```

1. 只能在继承层次内进行转换
2. 父类转换为子类之前，用 instanceof 进行检查

- 抽象类（abstract）

祖先类更加通用

使用 abstract 关键字就不用实现这个方法。包含一个或多个抽象方法的类本身必须为抽象的。

子类必须定义所有的抽象方法才可以不声明为抽象的。

编译器只允许调用在类中声明的方法，所以不要省略在抽象类中定义的抽象方法。

- 受保护访问

private 对其他类不可见，包括子类

protected 允许被子类或子类方法访问，也只能访问这个域，不能访问其他域

1. 仅对本类可见 private。

2. 对所有类可见 public。
3. 对本包和所有子类可见 protected。
4.  对本包可见—默认（很遗憾，) 不需要修饰符。

### 5.2 判断两个对象是否相等的几种方法

1、 equals()

2、相等测试与继承

getClass()

instanceof

3、hashCode()

得到一个整数。可以通过对象得到这个整数，但无法反推出对象值。

不同的数据类型有不同的hash计算方法，Object类是根据对象的内存地址计算的hash值，所以不同对象的hash值很难相同，同一对象在不同时间运行的hash值也不同。String类的对象是根据字符串的值计算的hash值，字符串的值相同，hash值就相同，Integer类的对象是按照数字的值计算的hash值。

hash的应用：密码。真正将用户密码保护得很好的企业是不会让你输入原密码进行改密码的，只有重置密码。企业数据库将用户密码加上一个特殊字符串，然后计算出hash值，将这个hash值存储到数据库，用户登录时，将用户输入的密码加上特殊字符串，得到的hash值与数据库中的比较，如果一样则密码正确。由于hash值无法反推，公司内部也无法知道你的密码。

4、toString()

## 集合框架

### 泛型

类型参数化

使用：

```java
// E T V 通常用这三个字母
// 代表数据类型
class MyClass<E> {
    Object[] objs = new Object[5];
    public void set(E e) {
        objs[0] = e;
    }
}

MyClass<String> mc = new MyClass<String>();
```

### Collection(集合)

```tex
               Collection
                  /     \
              List      Set
```

Set: 无序，不重复

常用类：

ArrayList 查询效率高，增删效率低，线程不安全

LinkedList 线程不安全，增删效率高

Vector 线程安全

基本方法：

```java
size();
isEmpty();
add();
get(index);
set(index, e);
remove(index);
contains(); // 是否包含
clear();
```

集合操作方法：

```java
containsAll(c);// 是否包含c中所有元素
addAll();// 添加
removeAll();// 移除都有的元素
retainAll();//  取出都包含的元素
```

### Map

键值对(key - value) key 不能重复

HashMap 底层实现了哈希表（数组加链表）

声明：

```java
// key Integer, value String
Map<Integer, String> map = new HashMap<>();
```

方法：

```java
put(key, value);// 如果key重复，替换原来的
get(key);
remove(key);
containsKey(key);
containsValue(value);
size();
isEmpty();
putAll(map);// 存放所有
clear();
```

哈希表结构

Entry[]

```tex
0 |hash|key|value|next -> |hash|key|value|next -> null
1 null
2 |hash|key|value|next -> null
3 |hash|key|value|next -> |hash|key|value|next -> |hash|key|value|next -> null
...
默认长度是16
```

存储过程：

1. 首先计算key的哈希码，key.hashCode();
2. 根据哈希码计算出对应的序号 HashMap.hash()
3. 生成对象 |hash|key|value|next|
4. 添加到指定序号的后面，形成单向列表

## 第六章

### Lambda 表达式

lambda 表达式的基本格式

```java
(parameters) -> expression
或
(parameters) ->{ statements; }
```

lambda 表达式只能引用标记了 final 的外层局部变量，这就是说不能在 lambda 内部修改定义在域外的局部变量，否则会编译错误。



## 第十章 图形程序设计

### 10.1 Swing 概述

java 1.0包含一个GUI图形处理程序（AWT），后来出现了Swing，基于AWT，AWT没有被完全替代，主要用于事件处理。

### 10.2 创建框架

没有被包含在其他窗口的窗口（顶层窗口）被称为框架，AWT中的Frame类描述顶层窗口，Swing版叫JFrame。

导入类库

```java
import java.awt.*;
import javax.swing.*;

public class Test {
    public static void main(args[] String) {
        // 不需要理解，之后第十四章解释，启动程序的神奇代码？？？
        EventQueue.invokeLater(() -> {
            // 新建窗口
            SimpleFrame sf = new SimepleFrame();
            // 设置右上角按钮关闭
            sf.setDefaultCloseOption(JFrame.EXIT_ON_CLOSE);
            // 设置窗口可见
            sf.setVisible(true);
        });
    }
}

class SimpleFrame extends JFrame {
    int width = 300;
    int height = 200;
    
    public SimpleFrame() {
        setSize(width, height);
    }
}
```

>默认框架的大小是 0 x 0
>
>JFrame.EXIT_ON_CLOSE 关闭框架的一个响应，多框架程序中不能关闭其中一个就退出。默认关闭窗口是隐藏，并没有终止程序。
>
>简单地构造框架是不会显示的，起初是不可见的，要设置其可见性，可以在显示之前添加其他组件。

### 10.3 框架定位

通过继承，从JFrame的超类中继承许多设置大小和位置的方法。

类继承关系：

```
					Object
					   ^
					   |
					Component
					   ^
					   |
					Container
					   ^
              ___________|___________
              |						|
		JComponent               Windows
              ^	                        ^
              |                         |
           Jpanel                    Frame
                                        ^
                                        |
                                      JFrame
```

主要有以下几个：

setLocation 和 setBounds 设置框架的位置

setIconImage 标题栏，任务切换窗口等位置的图标

setTitle 设置标题栏的文字

setResizable 窗口大小是否可变

```java
setLocation(x, y);
// x 距屏幕左方的距离
// y 距屏幕顶部的距离
setBounds(x, y, width, height);

setLocationByPlatform(true);
// 定位 ：显示窗口前调用，可以选用最后一个窗口最小偏移量的位置框架的属性
```

1. 框架的属性

> public String getTitle(); // 获取标题
>
> public void setTitle(String); // 设置标题

对于类型为 boolean 的属性，获取方法要以 is 开头，不过有个例外

> public boolean isLocationByPlatform()
>
> public void setLocationByPlatform(boolean)

2. 确定框架的大小

> 如果不指定，默认为 0 x 0
>
> 对于不同的电脑，屏幕分辨率也不同，可以使用类来获取

```java
Toolkit kit = Toolkit.getDefaultToolkit();
Dimension screenSize = kit.getScreenSize();
int screenWidth = screenSize.width;
int screenHeight = screenSize.height;

// 获取宽高之后，将框架宽高设为屏幕的一半
setSize(ScreenWidth / 2， screenHeight / 2);
setLocationByPlatform(true);
```

可以设置框架的图标

```java
// 加载图片，并获取图标
Image img = new ImageIcon("logo.jeg").getImage();

// 设置图标
setIconImage(img);
```

> 1. 将框架设置为最大
>
> frame.setExtendedState(Frame.MAXIMIZED_BOTH);
>
> 2. 最好重启应用后，回复上次的位置和大小
> 3. GraphicsDevice类允许应用全屏执行

一些常用方法：

```java
/* java.aet.Component */
boolean isVisible();
void setVisible(boolean); // 设置可见性
void setSize(int, int); // 设置大小
void setLocation(int, int); // 设置宽高
void setBounds(int, int, int, int);
Demension getSize();
void setSize(Dimension);
```

```java
/* java.awt.Window */
void toFront(); // 显示在最前面
void toBack();
boolean isLocationByPlatform();
void setLocationByPlatform(boolean)
```

```java
/* java.awt.Frame */
boolean isResizable();
void setResizable(boolean);
String getTitle();
void setTitle(String);
Image getIconImage();
void setIconImage(Image);
/* 获取或设置 Undecorated 属性，设置后，将没有标题栏或关闭按钮 */
boolean isUndecorated();
void setUndecorated(boolean);
int getExtendedState();
void setExtendedState(int); // 值为以下几种
Frame.NORMAL
Frame.ICONIFIED
Frame.MAXIMIZED_HORIZ
Frame.MAXIMIZED_VERT
Frame.MAXIMIZED_BOTH
```

```java
/* java.awt.Toolkit */
static Toolkit getDefaultToolkit();
// 返回默认工具箱
Dimension getScreenSize();
// 返回屏幕尺寸
```

```java
/* java.swing.ImageIcon */
ImageIcon(String); // 构造图标
Image getImage(); // 获取图标
```

### 10.4 在组件中显示信息

不建议直接在框架中绘制文字，应该在框架中添加组件，在组件中绘制文字等信息。

可以使用JFrame类的对象直接将组件添加到框架中。

frame.add(c);

使用 JComponent 类绘制一个组件，自定义一个类，继承 JCompoment，并覆盖 paintComponent 方法。 在绘图时，会自动调用 paintComponent 方法，不需要自己调用。重新绘图时，也会自己调用。

```java
class MyComponent extends JComponent {
    public void paintComponent(Graphics g) {
        // code
    }
}
```

如果需要刷新屏幕，使用 repaint() 方法

显示文本是特殊绘图，使用 Graphics 类中的 drawString 方法

g.drawString(text, x, y);

主要使用的方法：

```java
/* javax.swing.JFrame */
Container getContentPane(); // 返回这个JFrame 的内容窗格的对象
Component add(Component c); // 添加组件
```

```java
/* java.awt.Component */
void repaint(); // “尽可能快地”重新绘制组件
Dimension  getPreferredSize(); // 要覆盖这个方法，返回这个组件的首选大小
```

```java
/* javax.swing.JComponent */
void paintComponent(Graphics g);
```

```java
/* java.awt.Window */
void pack(); // 调整窗口大小，要考虑到其他组件的首选大小
```

### 10.5 处理 2D 图形

使用 Graphics2D 类对象，这是 Graphics 的子类。使用 paintComponent 方法会自动获得一个 Graphics 对象，只需对其进行类型转换。

```java
public void paintComponent(Graphics g) {
    Graphics2D g2 = (Graphics2D)g;
}
```

还有其他图形的2D类

Line2D 直线

Rectangle2D 矩形

Ellipse2D 椭圆

// TOWRITE 暂时没时间写

### 10.6

### 10.8 显示图像

使用 Graphics 类的 drawImage 方法显示图像。g.drawImage(img, x, y, null);

还有 Graphics 类的 copyArea 方法

```java
/* java.awt.Graphics */
boolean drawImage(Image img, int x, int y, ImageObserver oberserver);
//    ImageObserver 绘制进行中以通告为目的的对象，可能为null

boolean drawImage(Image img, int x, int y, int width, int height, ImageObserver oberserver);
// 绘制图像，以一定比例，在指定区域绘制。可能在还没有绘制完成时就结束。

void copyArea(int x, int y, int width, int height, int dx, int dy);
// 复制一片区域到目标区域
// dx 移动水平距离
// dy 移动垂直距离
```

## 第十一章 事件处理

### 11.1 事件处理基础

在AWT所知事件范围内，完全可以控制从事件源（按钮、滚动条）到事件监听器（eventListener）的传递过程，并将任何对箱指派给事件监听器。

所有事件的相关信息都封装在一个事件对象中（java.util.EventObject）。还有它的子类，ActionEvent和Windows。

按钮发送ActionEvent对象，窗口发送Windows对象。

```
	事件源 -------------------- 事件监听器
          << set of one or more >>     |
                                           | <<implements>>
                                           V
                                      监听器接口
```

监听器对象是一个实现了特定监听器接口（listener interface) 的类的实例。

事件源是一个能够注册监听器对象并发送事件对象的对象。

当事件发生时，事件源将事件对象传递给所有注册的监听器。

监听器对象将利用事件对象中的信息决定如何对事件做出响应。

给事件源添加事件监听器：

```java
ActionListener listener = ...;
JButton button = new JButton("OK");
button.addActionListener(listener);
// 只要按钮产生了动作事件，listener 就会得到通告
```

为了实现 ActionListener 接口，监听器类必须有一个被称为 actionPerformed 的方法。

```java
class MyListener implements ActionListener {
    public void actionPerformed(ActionEvent event) {
        // reaction to button click goes here
    }
}
```

只要用户点击按钮，JButton就会创建一个ActionEvent对象，然后调用listener.actionPerformed传递事件对象。

```java
/* javax.swing.JButton */
JButton(String label);
JButton(Icon icon);
JButton(String lable, Icon icon);
// 也可以是HTML代码 <html><b>OK</b></html>
```

```java
/* java.awt.Container */
Component add(Component c);
// 将组件添加到容器中
```

一般每个监听器只执行一个简单动作，不必向上面那么麻烦。可以使用 lambda 表达式指定监听器。

```java
exitButton.addActionListener(event -> System.exit(0));
```

```java
/* java.awt.event.ActionEvent */
String getActionCommand(); // 返回与动作关联的命令字符串，可以用 setActionCommand() 更改
```

```java
/* java.util.EventObject */
Object getSource(); // 返回这个事件对象的引用
```

改变观感：忽略

### 11.2 动作

将一个事件监听器添加到多个事件源上，可以实现点击、快捷键触发等操作。

swing 包提供了一个非常实用的机制来封装命令，并将他们连接到多个事件源，这就是Action接口。

一个动作是封装了以下内容的对象：

- 命令的说明（一个文本字符串和一个可选图标）。
- 执行命令所需的参数。

Action 接口包含以下方法：

> void actionPerformed(ActionEvent event)
>
> void setEnabled(boolean b) // 启用或禁用这个动作
>
> boolean isEnabled() // 检查是否可用
>
> void putValue(String key, Object value) // 存储动作对象的任意值/名
>
> Object getValue(String key) // 检索
>
> void addPropertyChangeListener(PropertyChangeListener listener)
>
> void removePropertyChangeListener(PropertyChangeListener listener)

预定义动作名称表：

> NAME                     动作名称，显示在按钮和菜单上
>
> SMALL_ICON               存储小图标的地方
>
> SHORT_DESCRIPTION        图标的简要说明
>
> LONG_DESCRIPTION         图标的详细说明
>
> MNEMONIC_KEY             快捷键缩写
>
> ACCELERATOR_KEY          存储加速击键的地方
>
> ACTION_COMMAND_KEY       历史遗留，旧版本 registerKeyBoardAction 方法中可用
>
> DEFAULT                  可能有用的综合属性

AbstractAction 类实现了以上方法中除了 actionPerformed 之外的所有方法。我们可以继承 AbstractAction 类，然后实现 actionPerformed 方法即可。

实现击键事件：KeyStroke 类

不需要 new KeyStroke(), 只需调用 KeyStroke 中的静态方法

> KeyStroke ctrlKey = KeyStroke.getKeyStroke(“ctrl B”);

当输入一个快捷键时，哪个组件处理它呢，如果给每个组件都需要知道如何处理很多快捷键，将会很麻烦。

不过现实是有一个更好的解决方法，swing 设计者提供的解决方案。每个Jcomponent有三个输入映射（inputMap），每个映射的KeyStroke都与动作关联。

> WHEN_FOCUSED                         组件拥有焦点
>
> WHEN_ANCESTOR_OF_FOCUSED_COMPONENT   包含拥有焦点的组件
>
> WHEN_IN_FOCUSED_WINDOW               被拥有焦点的组件的窗口包含（其他）

处理顺序：

1. WHEN_FOCUSED
2. WHEN_ANCESTOR_OF_FOCUSED_COMPONENT
3. WHEN_IN_FOCUSED_WINDOW

如果多个按键处于第 3 映射中时，可能会出现问题。

可以使用 getInputMap 从组件中获取映射。

> InputMap imap = panel.getInputMap(JComponent.WHEN_FOCUSED);

InputMap 不能直接将 KeyStroke 对象映射到 Action 对象。需要先映射到任意对象，然后由 ActionMap类实现将对象映射到动作上的第二个映射。

```java
imap.put(KeyStroke.getKeyStroke("ctrl Y"), "panel.yellow");
ActionMqp amap = panel.getActionMap();
amap.put("panel.yellow", yellowAction);

// 习惯上，none 表示空动作，可以轻松取消按键，
imap.put(KeyStroke.getKeyStroke("ctrl C"), "none");
```

总结：

1. 实现一个扩展于AbstractAction 类的类，多个相关的动作可以使用一个类。
2. 构造一个动作类的对象。
3. 使用动作对象创建按钮或菜单项。
4. 为了实现按键触发，需额外几步。首先，定位顶层窗口组件（包含所有其他组件的面板）。
5. 然后，得到顶层组件的 WHEN_ANCESTOR_OF_FOCUSED_COMPONENT 的输入映射。为需要的按键创建一个 KeyStroke 对象。创建一个描述动作字符串的动作键对象，将（按键，动作键）添加到输入映射中。
6. 最后，得到顶层组件的动作映射。将（动作键，动作对象）添加到输入映射中。

```java
/* java.swing.JComponent */
ActionMap getActionMap(); // 返回关联动作映射键的和动作对象的映射
InputMap getInputMap(int flag); // 获得将按键映射到动作键的输入映射
```

### 11.3 鼠标事件

当用户点击鼠标按钮时，将会调用三个监听方法：（按事件顺序）

> mousePressed      鼠标第一次被按下
>
> mouseReleased     鼠标被释放
>
> mouseClicked      最后调用的

用 MouseEvent 对象做参数，可以调用一些方法。

> getX()             点击时的 x 坐标
>
> getY()             点击时的 y 坐标
>
> getClickCount()    点击次数

不建议鼠标事件和键盘事件混用，会造成混乱。

按钮掩码

> BUTTON1_DOWN_MASK
>
> BUTTON2_DOWN_MASK
>
> BUTTON3_DOWN_MASK // 鼠标右键 windows
>
> SHIFT_DOWN_MASK
>
> CTRL_DOWN_MASK
>
> ALT_DOWN_MASK
>
> ALT_GRAPH_DOWN_MASK
>
> META_DOWN_MASK

有两个独立的接口， MouseListener 和 MouseMotionListener，分别监听鼠标点击和鼠标移动。

还有光标的形状和对应的常量

> DEFAULT_CURSOR
>
> CROSSHAIR_CURSOR
>
> HAND_CURSOR
>
> MOVE_CURSOR
>
> TEXT_CURSOR
>
> WAIT_CURSOR
>
> // 还有八个调整窗口图标
>
> N_RESIZE_CURSOR
>
> NE_RESIZE_CURSOR
>
> E_RESIZE_CURSOR
>
> SE_RESIZE_CURSOR
>
> S_RESIZE_CURSOR
>
> SW_RESIZE_CURSOR
>
> W_RESIZE_CURSOR
>
> NW_RESIZE_CURSOR

还可以自定义图标的形状：（ToolKit 类）

```java
ToolKit tk = ToolKit.getDefaultToolKit();
Image img = tk.getImage("pathOfFile");
Cursor dyCursor = tk.createCustomCursor(img, new Point(10, 10), "dy stick");
// 三个参数分别是 图片，鼠标的热点偏移，描述光标的字符串
```

