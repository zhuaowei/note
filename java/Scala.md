# Scala

## 一、简介

## 二、基础

### 2.1、数据类型

Scala的基本数据类型和 java 有些许的不同。

Byte

Short

Int

Long

Float

Double

Char

String

Boolean

Unit：相当于Java中的void

Null

Nothing：所有类的子类，Scala会自动判断用户定义的变量类型，当Scala判断不出用户定义的是什么类型时，就会使用Nothing，

Any：所有类的超类，相当于Java中的Object

AnyRef：所有引用类型的基类

AnyVal：所有数值类的基类（Int，Double...）

### 2.2、变量

#### 2.2.1、声明变量

Scala中不需要指明变量的类型，Scala会根据变量的初始值自动判断变量属于什么类型。

声明有两个关键字，`val` 和 `var` ，`val` 是常量，定义后不能修改，`var` 是变量，可以修改。

定义为val是为了方便JVM回收垃圾，提高运行效率，所以，如果不是定义变量，尽量使用val。

```scala
val a = 100
var b = 99
```



#### 2.2.2、变量类型声明

声明变量的时候可以指定变量的类型

```scala
val name: String = "hello world"
val age: Int = 20
```

#### 2.2.3、变量类型引用

声明变量可以不用指定变量的类型，它是根据变量的初始值进行判断的。如果没有给变量定义初始值，也没有指定变量类型，就会报错。

#### 2.2.4、声明多个变量

声明多个变量可以用 `,` 隔开

```scala
val a, b = 100
```

### 2.3、运算符

Scala除了没有Java中的自增和自减运算符，其余都有。这一点和Python一样。

加减乘除

关系运算符（大于，小于，等于...）

逻辑运算符（与、或、非）

位运算符（取反、按位与、按位或、按位异或，左移、右移，无符号右移>>>）

赋值运算符（+=、-=...）

### 2.4、流程控制

#### 2.4.1、IF...ELSE

和Java一样

#### 2.4.2、for循环

```scala
// [1, 10] 包含 10
for (var i <- 1 to 10) {
    
}

// [1, 10) 不包含10
for (var j <- 1 until 10) {
    
}
```

>   其实是 to/until 是一个方法，可以写成 `i <- 1.to(10, 1)` 
>
>   第二个参数是间隔，也可以不带。

循环嵌套

```scala
for (var i <- 1 to 10) {
    for (var j <- 1 until 10) {
    
    }
}

// 也可以在同一行写下
for (var i <- 1 to 10; var j <- 1 until 10) {
    
}

// 也可以给循环遍历的变量限定条件，可以省略分号，用空格间隔
for (var i <- 1 to 100; if (i > 50); if (i % 2)) {
    
}
```

使用yield存储循环返回的变量

```scala
var retVal = for{ var x <- List
     if condition1; if condition2...
}yield x
```

#### 2.4.3、while循环

和Java中的一样

```scala
while(condition) {
    statement(s)
}
```

#### 2.4.4、do...while循环

同样和Java中的一样

```scala
do {
    statement(s)
} while(condition)
```

### 2.5、方法与函数

方法与函数几乎相同，区别很小。方法是类的一部分，函数是一个对象，可以赋值给一个变量。

Scala 中的函数则是一个完整的对象，Scala 中的函数其实就是继承了 Trait 的类的对象。

使用 `val` 定义一个函数，使用 `def` 定义一个方法。

#### 2.5.1、方法

##### 1、声明方法

```scala
def sayHello(name: String) : {
    println("hello, " + name)
    // 可以使用 s 输出
    // println(s"hello, $name !")
}
```

##### 2、调用

这一点和Java中一样，首先创建一个对象实例，然后用这个对象调用方法。

##### 3、返回值

如果不写返回值，默认返回值就是Unit，相当于Java中的void。如果需要哦返回值，就需要在大括号前面写上返回值的类型和等号。

```scala
def max(a: Int, b: Int): Int = {
    if (a > b) {
        return a
    } else {
        return b
    }
}
```

>   1.   可以省略return，Scala会自动判断方法执行的最后一行，将最后一行的运行结果返回。
>   2.   如果不写等号，就意味着没有返回值，写 return 会报错。

#### 2.5.2、递归

跟其他语言类似的用法，递归方法需要显示的定义方法的返回值，否则scala不确定递归的返回值是不是推断的。

```scala
// 这个方法就是返回某个数字的阶乘
def fun(num:Int): Int = {
    if (num == 1) {
        1
    }
    num * fun(num - 1)
}
println(fun(5))
```

#### 2.5.3、方法默认值

给方法的参数定义默认值，就可以在调用时，可以不给方法传值。

```scala
def fun(a: Int=10, b:Int=20): Int = {
    a + b
}
// 下面都是可行的
fun()
fun(100)
fun(b=200)
fun(100, 200)
```

#### 2.5.4、可变长参数

传递的参数个数不确定。

```scala
def fun(s:String*) = {
    for (elem <- s) {
        println(elem)
    }
}
```

>   1.   指定了传入的类型的参数，就只能传入这种类型的数据，个数无限制
>
>   2.   如果传入的参数的个数和类型都未知，可以使用Any类型

#### 2.5.5、匿名函数

匿名函数是可以没有名字的，它常作为方法的参数传入，执行一些操作。

匿名函数也可以赋值给一个变量，然后通过变量名调用它。

从这里看出函数和方法的细微差别，仅此而已。

```scala
// 匿名函数 小括号里是函数的形参， => 代表是匿名函数 大括号里面是操作
() => {}

// 给匿名函数赋值为一个变量
def fun = () => { println("hello") }
fun()
```

上面的 for 循环可以写成匿名函数的形式

```scala
def fun(s:String*) = {
    s.foreach((elem) => {println(elem)})
}
// 由于匿名函数的形参只用了一次，我们可以将其用 _ 代替，然后去掉除函数体外的所有部分
def fun(s:String*) = {
    s.foreach(println(_))
}
// 又由于匿名函数只传入一个参数，可以再次将它简写，去掉括号以及下划线
def fun(s:String*) = {
    s.foreach(println)
}
```

#### 2.5.6、偏应用函数

偏应用函数适合于 参数特别多，调用次数也多，但调用的时候，只有固定的某个参数不变，其他都不变。

```scala
// 这个参数频繁调用，但是只有log每次调用的时候会变
def showLog(date:Date, log:String) = {
    println(s"date is $date, log is $log")
}
val date = new Date()
// 定义偏应用函数，date是固定日期，变化的参数使用 _ 表示
def fun = showLog(date, _:String)
fun("a")
fun("b")
fun("c")
```

#### 2.5.7、高阶函数

什么是高阶函数，包括三种：

1、参数为函数的函数

2、返回值为函数的函数

3、参数和返回值都是函数的函数

##### 1、参数为函数的函数

将函数的参数类型和返回值类型声明为高阶函数的参数。

```scala
// 定义一个函数作为参数传入
def plus(a: Int, b: Int): Int = {
    a + b
}
// 高阶函数，s也可以作为形参函数的参数传入
// scala中很多类似的操作，定义自己对数据的操作，然后让一些包中的方法去操作
def fun(f:(Int, Int) => Int, s:String) = {
    f(100, 200) + "-" + s
}
// 调用
fun(plus, s)
```

##### 2、返回值为函数的函数

定义返回值为函数的函数，需要显示地指出返回函数的类型

```scala
def fun(s: String): (String, String) => String = {
    def demo(s1:String, S2:String): String = {
        s1 + "-" + s2 + "-" + s
    }
    // 返回函数
    demo
}
```

如果不显示写出返回函数的类型，scala虽然能够判断出返回的函数类型，但它不确定。

还可以使用 `-` 指定返回函数

```scala
def fun(s: String): = {
    def demo(s1:String, S2:String): String = {
        s1 + "-" + s2 + "-" + s
    }
    // _ 表示返回函数
    demo _
}
// 调用
fun("hello")("a", "b")
// >>> a-b-hello
```

##### 3、参数也为函数，返回值也为函数的函数

就是前面两种的结合，不再赘述。

#### 2.5.8、柯里化函数

可以定义函数参数在不同的括号中，可以有两个，也可以有多个。

```scala
def demo(a:Int, b:Int)(c:Int, d:Int) = {
    a + b + c + d
}
// 调用
demo(1, 2)(3, 4)
```

调用柯里化函数的形式和高阶函数中返回值为函数的函数类似

### 2.6、闭包

闭包是一个函数，返回值依赖于声明在函数外部的一个或多个变量。

```scala
var factor = 3  
val multiplier = (i:Int) => i * factor  
```

multiplier 是一个函数，函数定义里面用到了函数外边的变量。

因为 multiplier 使用了外部的变量，所以它构成一个”闭包“。

### 2.7、String

Scala中使用的String类型就是Java的String类。（java.lang.String）

方法调用都一样。

### 2.8、Array

scala 中的集合类型（Array，List，Set，Map）都有可变和不可变两种，不可变长度是固定的，声明后就不能再添加。默认声明的就是不可变的。

#### 2.8.1、不可变Array

声明一个一维数组

```scala
val arr = Array[String]("a", "b", "c")

val arr1 = new Array[Int](3)
arr1(0) = 1
arr1(1) = 2
arr1(2) = 3
```

>   1.   声明有两种方式，不带new的直接在后面括号填写内容，带new的后面写定义的array的长度。
>
>   2.   给数组中某些具体的值赋值的时候，其他语言是中括号，scala是小括号。

声明一个二维数组

```scala
val arr = new Array[Array[Int]](3)
arr(0) = Array[Int](1, 2, 3)
arr(1) = Array[Int](4, 5, 6)
arr(2) = Array[Int](7, 8, 9)
```

>   1.   声明array的时候，中括号里面写的是泛型
>   2.   声明二维数组，泛型要写成Array，Array还有一个泛型，写元素的类型

循环遍历

```scala
// 可以使用普通的循环，也可以使用foreach
// 一维
for (elem <- arr) {
    println(elem)
}
arr.foreach(println)

// 二维
for(oneDim <- arr) {
    for (elem <- oneDim) {
        println(elem)
    }
}
for (oneDim <- arr; elem <- oneDim) {
    println(elem)
}
arr.foreach(oneDim => {oneDim.foreach(println)})
```

#### 2.8.2、可变Array

ArrayBuffer是Array的可变形式，声明和上面的方式是一样的。它有一些可以改变数组长度的方法。

```scala
val arr = Array[Int](1, 2, 3)
// 添加
// 尾部添加
arr.+=(4)

// 头部添加
arr.+=:(100)

// 一次添加多个
arr.append(5, 6, 7)

// >>> 100 1 2  3 4 5 6 7
```

### 2.9、List

#### 2.9.1、immutable List（不可变List）

list也是数组，但是List可以放不同类型的数据，一般不这样写，最好指定它的类型，否则，scala可能判断不出数据的类型，返回Noting。

List和上面不同的是它没有new关键字。

声明List

```scala
val list = List[Int](1, 2, 3)
```

循环遍历和上面的一样的

map方法

```scala
val list = List[String]("hello java", "hello scala", "hello spark")
// 传入一个函数，对每一样进行操作
list.map(s => {s.split(" ")})
// 将每一行按空格分割，返回的类型是 List[Array[String]]

// 对每一样操作，然后将其处理成一维数组
list.flatMap(s => {s.split(" ")})
// 返回类型是 List[Stirng]
```

>   1.   flatMap是在map的基础上，对结果进行处理
>   2.   map是输出输出一对一，flatMap是一对多

filter 过滤

```scala
// 保留数组中，能是匿名函数返回值为 true 的项
list.filter(s => { "hello java".equals(s) })
```

counter 计数

```scala
// 返回满足匿名函数中 返回值为true 的个数
list.counter(s => { s.length > 3 })
```

#### 2.9.2、mutable List（可变List）

可变数组使用 ListBuffer 定义，可以使用 new 关键字定义List。

可变List也可以使用map、flatMap、filter、counter等函数，也可以添加新元素。

添加新元素使用 `+=` `+=:` `append`

### 2.10、Set

默认使用的Set是不可变数组，不过它没有Buffer，它的可变和不可变名字是一样的，下面介绍的Map也同样。要使用可变的，需要手动导包。

```scala
import scala.collection.mutable  // mutable包中放的都是可变的
import scala.collection.immutable  // immutable包中放的都是不可变的
```

如果要混和使用可变和不可变，需要在声明时加上 `mutable/immutable`

```scala
import scala.collection.mutable  // mutable包中放的都是可变的
import scala.collection.immutable  // immutable包中放的都是不可变的
object Demo {
    def main(args:Array[String]): Unit = {
        val set = mutable.Set[Int](1, 2, 3)
        val imset = immutable.Set[Int](2, 3, 4)
        
    }
}
```

集合操作

交集 intersect

差集 diff

```scala
// 交集
set1.intersect(set2)
// 符号形式
set1 & set2

// 差集 set1 - set2
set1.diff(set2)
// 符号表示
set1 &~ set2
```

set也有filter，map等方法

### 2.11、Map

声明可以使用括号，也可以使用箭头，重复的key，后面的覆盖前面的

```scala
val map = Map[String, Int](("a", 1), ("b", 2), "c" -> 3, "c" -> 4)
```

**运算**

```scala
// 合并两个map，如果有重复的 map2会覆盖map1
map1.++(map2)
// 相反 map1 会覆盖 map2
map1.++:(map2)
```

map也可以使用 foreach，filter等函数，传入的参数就是每一个 k-v

根据key获取value

```scala
// 返回的是一个Option对象
map.get("a")
// 返回value的类型
map.get("a").get
// 如果有，就返回value，如果没有，就返回自定义的值
map.get("a").getOrElse("no value")
```

**Option**对象可能会有，也可能没有，当map中获取到value是，它就有值，否则，就是None

获取Option中的value值使用get

**遍历**Map的Key，Value

```scala
map.keys
map.values
map.foreach(tp => {
    // _1 就是 key，_2 就是 value
    val k = tp._1
    val v = tp._2
})
```

声明可变长Map可以使用**put方法**添加元素

```scala
map.put("d", 5)
```

也可以使用 filter，counter方法

### 2.12、Tuple

**声明**元组的三种方式

```scala
// new Tuple1 一元组
val tuple1 = new Tuple1("hello")
// 不用 new
val tuple2 = Tuple2("a", 1)
// 直接写括号
val tuple5 = (1, 2, 3, 4, 5)
```

>   1.   声明几元组，就在元组类型后面写几
>   2.   元组最大长度是22
>   3.   只有一元元组的类型是在中括号中，其他的在小括号中
>
>   `val tuple1:[String] new Tuple1("hello")`
>
>   `val tuple2:(String, Int) = Tuple2("a", 1)`
>
>   4.   map的每个元素就是二元元组

**取值**：._xx

```scala
// 获取第几个元素就写几
tuple6._6
```

**遍历**：元组无法使用for循环和foreach遍历，可以使用一个方法生成迭代器，用迭代器遍历

```
val iter = tuple6.productIterator
for (i <- iter) {
    println(i)
}
iter.foreach(println)
```

**toString/swap**

每个元组类中都有一个ToString方法，打印的值就是用括号括起来的所有元素

只有二元元组有swap方法，它可以将两个元素调换位置。

## 三、类和对象

在Scala中分为类（class）和对象（object）

### 3.1、class

#### 3.1.1、定义一个class

```scala
class Person(name: String, age: Int) {
	...
}
```

>   1.   使用class关键字定义一个类
>   2.   类名后面要跟一个括号，可以定义传入的参数
>   3.   类里面可以定义类的属性，构造函数和方法

#### 3.1.2、定义类的属性

属性分为常量和变量，常量用 `val` 声明，变量用 `var` 声明。常量定义后不可更改，相当于Java中的final，变量可以进行修改。

定义为val是为了方便JVM回收垃圾，提高运行效率，所以，如果不是定义变量，尽量使用val。

#### 3.1.3、定义类的构造方法

```scala
class Person(xname: String, xage: Int) {
    val name = xname
    val age = xage
    val gender
    
    this(xname: String, xage: Int, xgender:Char) {
        this(xname, xage)
        gender = xgender
    }
}
```

>   1.   构造方法可以重载，使用 this 进行重载
>   2.   重载的构造方法必须以其他的构造方法开始

#### 3.1.4、创建对象

使用 new 创建对象时，除了方法和构造方法不执行，类中的所有代码都会执行。

### 3.2、object

object相当于Java中的单例模式，里面定义的都是静态的。

同一个文件中，object可以和class重名，他们的属性和方法是共享的，这个叫作伴生类和伴生对象。

```scala
object World {
    
}
```

>   1.   object不可以定义默认构造
>   2.   如果要像类一样构造带参实例，需要实现apply方法

### 3.3、Trait

Trait相当于Java中的抽象类和接口的结合。

Trait不能传参，也就是说，定义Trait的时候不能写括号，Trait中定义的方法可以有实现，也可以没有实现。

其他类可以继承Trait，继承后，必须实现没有实现的方法。

```scala
Trait Action {
    def listen(name:String): Unit = {
        println(s"$name is listening...")
    }
    def read(name:String)
}
//
class Person() extends Action {
    def read(name:String): Unit = {
        println(s"$name is reading...")
    }
}
```

>   1.   继承使用 extends 关键字
>   2.   继承第二个及以上，使用 with 关键字

### 3.4、Match 模式匹配

模式匹配类似 Java 中的 switch，它不仅可以匹配值，也可以匹配类型

```scala
def testMatch(o:Any): Unit = {
    o match {
        case 1 => println("value is 1")
        case num: Int => println(s"type is Int, value is $num")
        case f:Double => println(s"type is Double, value is $f")
        case _ => println("no match...")
    }
}
```

>   1.   定义一个普通的函数，里面使用模式匹配
>   2.   使用关键字 match 
>   3.   可以匹配值或者类型
>   4.   case _ 是啥都没匹配上的情况，放到最后，类似Java中的 default
>   5.   各case从上往下执行，匹配成功，结束
>   6.   方法的大括号可以不写，match相当于一行

### 3.5、偏函数（PartialFunction）

偏函数和模式匹配类似，但是它只能匹配值。它是传入一个参数，匹配后返回一个值。定义偏函数的时候不能写参数。

```scala
def testPartialFunc: PartialFunction[String, Int] = {
    case "abc" => 3
    case "ab" => 2
    case "a" => 1
    case _ => 0
}
```

### 3.6、case class 样例类

创建样例类的对象，可以不使用 new 关键字。样例类自动实现了 `toString/equals/copy/hashCode` 方法，还有getter和setter方法（val只有getter方法）。

```scala
case class Person(xname:String, xage: Int) {
    val name = xname
    val age = xage
}
```

## 四、隐式转换（implicit）

### 4.1、隐式值和隐式参数

使用 `implicit` 修饰的变量叫作隐式值

使用 `implicit` 修饰的函数参数叫作隐式参数

调用含有隐式参数的函数，如果没有给它传参，它就会在调用的作用域内找到一个唯一的类型相同的隐式值作为参数。

```
def demo(implicit name:String): Unit = {
    ...
}
implicit val name = "zhangsan"
demo
```

>   1.   函数含有隐式参数
>   2.   没有给函数传参
>   3.   作用域内有同类型的隐式值
>   4.   有且只有一个

如果函数既有隐式参数，也有非隐式参数，他们必须分开写，使用柯里化函数的形式，将隐式参数写在后面。

```scala
def demo(age:Int)(implicit name:String): Unit = {
    ...
}
implicit val name = "zhangsan"
demo(18)
```

### 4.2、隐式转换函数

隐式转换函数其实就相当于系统自动给你调用的类型转换。这个类型转换函数必须你自己写出来，然后用 implicit 修饰。

例如类A有一个eat方法，类B没有eat方法，如果类B想调用eat方法，需要类型转换成类A，这里就用到了隐式转换函数。

```scala
class A() {
    def eat(): Unit = {
        
    }
}
class B() {
    
}
implicit def BToA(b:B): A = {
    ...
}
// 
val b = new B()
b.eat()
```

>   1.   在作用域内只能定义一个参数和返回值相同的隐式转换函数

### 4.3、隐式类

和上面的隐式转换函数类似，一个类如果没有某个方法，但它又想调用，那就定义一个隐式类。

这个隐式类只能传入一个参数，这个参数就是没有但想调用的对象的类。在隐式类中定义那个被调用的方法。

例如，类A想调用eat方法，但是它没有，但是隐式类B有，隐式类B需要将类A作为参数传入。

```scala
```

>   1.   隐式类只能创建在class或者object中
>   2.   同一个作用域只能有一个相同参数的隐式类

## 五、Actor Model

Actor Model是线程通信模型，Spark1.6之前使用akka实现分布是集群之间的通信，akka底层就是使用的Actor，1.6 - 2.0使用 akka 和netty混和，Spark2.0之后就完全使用netty。

无论是akka还是netty，他们实现线程通信的模型是一样的。

Actor Model 实现线程间通信没有了锁的概念，替代是使用消息队列的形式。

锁实现线程间通信是：线程间通信，先上锁，其他线程没有锁只能等待，所以线程会阻塞，通信过程会一直占用链接。

Actor Model实现线程间通信：与某个线程通信，就把消息放到这个线程的消息队列中，自己该干嘛干嘛。被访问的线程会依次读取消息队列中的任务，完成后会通知发送消息的线程，也把消息放到它的消息队列中。发送消息的线程会定期检查自己的消息队列，看看有没有返回消息。

### 5.1、给线程发消息

首先定义一个类，这个类要继承Actor Trait，重写里面的act方法。

然后创建这个类的实例，启动actor。

最后想actor发送消息，actor就能处理消息了。

```
class MyActor extends Actor {
    override def act(): Unit = {
        while(true) {
            receive {
                case s:String => println(s)
                case _ => println("no match")
            }
        }
    }
}

// 调用
val actor = new MyActor()
actor.start()
actor ! "hello"
actor ! 100
```

>   1.   act函数类似偏函数，使用模式匹配
>   2.   发送消息使用 `!`

### 5.2、使用样例类通信

```scala
package cn.xpleaf.bigdata.p5.myactor

import scala.actors.Actor

/**
  *
  */
object GreetingActor {
    def main(args: Array[String]): Unit = {
        val ga = new GreetingActor
        ga.start()

        ga ! Greeting("小美")
        ga ! WorkContent("装系统")
    }
}

case class Greeting(name:String)
case class WorkContent(content:String)

class GreetingActor extends Actor {
    override def act(): Unit = {
        while(true) {
            receive {
                case Greeting(name) => println(s"Hello, $name")
                case WorkContent(content) => println(s"Let's talk about sth. with $content")
            }
        }
    }
}
```

定义样例类，通信时，将样例类发送给actor。Actor定义处理样例类的模式匹配。

>   reference: 
>
>   Scala笔记整理（九）：Actor和AKKA
>
>   https://blog.51cto.com/xpleaf/2107108

### 5.3、线程间通信

使用上面样例类的方法，定义样例类，Actor在处理消息时，将消息发送给另一个Actor。因此，需要对方的Actor作为参数传入，这样才能进行通信。

```scala
package cn.xpleaf.bigdata.p5.myactor

import scala.actors.Actor

/**
  * actor之线程间，互相通信
  *
  * studentActor
  *     向老师问了一个问题
  *
  * teacherActor
  *     向学生做回应
  *
  * 通信的协议：
  * 请求，使用Request(内容)来表示
  * 响应，使用Response(内容)来表示
  */
object _03CommunicationActorOps {
    def main(args: Array[String]): Unit = {
        val teacherActor = new TeacherActor()
        teacherActor.start()
        val studentActor = new StudentActor(teacherActor)
        studentActor.start()

        studentActor ! Request("老李啊，scala学习为什么这么难啊")
    }
}

case class Request(req:String)
case class Response(resp:String)

class StudentActor(teacherActor: TeacherActor) extends Actor {
    override def act(): Unit = {
        while(true) {
            receive {
                case Request(req) => {
                    // 向老师请求相关的问题
                    println("学生向老师说：" + req)
                    teacherActor ! Request(req)
                }
                case Response(resp) => {
                    println(resp)
                    println("高！")
                }
            }
        }
    }
}

class TeacherActor() extends Actor {
    override def act(): Unit = {
        while (true) {
            receive {
                case Request(req) => {  // 接收到学生的请求
                    sender ! Response("这个问题，需要如此搞定~")
                }
            }
        }
    }
}
```

>   reference: 
>
>   Scala笔记整理（九）：Actor和AKKA
>
>   https://blog.51cto.com/xpleaf/2107108

## 六、Spark Word Count

```scala
import org.apache.spark.{SparkConf, SparkContext}

object WordCount {
  def main(args: Array[String]): Unit = {
    // 输入文件地址
    val inputFile = "file:///E:/Project/java/scala_wordcount/data/word.txt"
    // 定义Saprk的工作配置
    val conf = new SparkConf().setAppName("WordCount").setMaster("local")
    // 创建Saprk实例，只有这个实例能够与Spark集群进行通信
    val sc = new  SparkContext(conf)
    val textFile = sc.textFile(inputFile)
    /**
     * textFile是RDD[String]类型的，类似集合
     * 将没一行按空格分割，然后处理到一个RDD[String] 中
     * map 将每一个单词创建一个二元组 ("word", 1) 
     * reduceByKey 这个函数进行了两步操作，第一步是根据key将数据分组，第二步是执行reduce操作
     * 对每一个分区的 value 进行处理，a 一个key的value，b另一个key的value
     * a + b 结束后，结果成为下一个执行的 a，直到最终加完。如果只有一个，根本不会进入这个lambda函数
     */
    val wordCount = textFile.flatMap(line => line.split(" "))
      .map(word => (word, 1)).reduceByKey((a, b) => a + b)
    wordCount.foreach(println)

  }
}
```

