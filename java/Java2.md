# Java核心技术卷二

### IO

#### Reader/Writer

##### 从控制台读入

```java
// 从控制台获取输入
BufferedReader br = new BufferedReader(new 
                      InputStreamReader(System.in));

br.read();// 读取一个字符，返回 int
br.readLine();// 读取一行，返回 String
```

##### 从控制台输出

```java
// 会导致异常
System.out.write('a');
System.out.write('\n');
// 常用的是print() 和 println()
```



#### InputStream/OutputStream

FileInputStream

创建实例

```java
// 1 使用路径打开文件
FileInputStream fis = new FileInputStream("file/to/path");

// 2 使用file对象打开
File f = new File("file/to/path");
FileInputStream fis = new FileInputStream(f);
```

方法：

```java
fis.close();
fis.finalize();
fis.read(int r);
fis.read(byte[] r)
```

FileOutputStream

与FileInputStream类似

方法：

```java
fis.close();
fis.finalize();
fis.write(int r);
fis.write(byte[] r)
```

#### InputStreamReader/OutputStreamWriter

```java
// 读取
InputStreamReader reader = new InputStreamReader(fip, "UTF-8");
StringBuffer sb = new StringBuffer();
while (reader.ready()) {
    sb.append((char) reader.read());
    // 转成char加到StringBuffer对象中
}
System.out.println(sb.toString());

// 写入
OutputStreamWriter writer = new OutputStreamWriter(fop, "UTF-8");
writer.append();
// 添加完后关闭即可刷新缓冲区，自动写入
writer.close();
```

#### 目录

```java
// 实例化 File 对象
String dirname = "/tmp/user/java/bin";
File f1 = new File(dirname);
```

方法：

```java
f1.mkdirs(); // 将创建一个上面指定的目录
f1.isDirectory();// 判断是否是目录
f1.list();// 列出当前目录的所有和目录，返回字符转列表
f1.listFiles();// 列出当前目录的所有和目录，返回file对象列表
f1.delete();//删除文件或文件夹（文件必须是空才能删除）
```

#### Scanner

实例化

```java
Scanner scan = new Scanner(System.in);
```

方法：

```java
scan.hasNext();// 是否还有输入
scan.next();// 遇到有效字符才开始获取，之后遇到空白字符结束
scan.hasNextLine();
scan.nextLine();// 可以开始是空白，遇到回车结束

// 同理 int float double
scan.hasNextInt();
scan.nextInt();
```

### 异常

内置异常

异常方法：

```java
getMessage();// 异常详细信息
getCause();// 异常原因
toString();// 使用getMessage()的结果返回类的串级名字。
printStackTrace();// 打印toString()结果和栈层次到System.err，即错误输出流。
getStackTrace();// 返回一个包含堆栈层次的数组。下标为0的元素代表栈顶，最后一个元素代表方法调用堆栈的栈底。
fillInStackTrace();// 用当前的调用栈层次填充Throwable 对象栈层次，添加到栈层次任何先前信息中。
```

捕获异常

```java
//  方法一
try {
    // TODO 
    
} catch (ExceptionName e) {
    // TODO
} catch (ExceptionName f) {
    // TODO
}

// 方法二 (不推荐)
import java.io.*;
public class className
{
  public void deposit(double amount) throws RemoteException
  {
    // Method implementation
    throw new RemoteException();
  }
  //Remainder of class definition
}
```

finally

无论是否发生异常，代码都会执行。

```java
try {
    // TODO 
    
} catch (ExceptionName e) {
    // TODO
} finally {
    // TODO
}
```

自定义异常类

```java
// 文件名InsufficientFundsException.java
import java.io.*;
 
//自定义异常类，继承Exception类
public class InsufficientFundsException extends Exception
{
  //此处的amount用来储存当出现异常（取出钱多于余额时）所缺乏的钱
  private double amount;
  public InsufficientFundsException(double amount)
  {
    this.amount = amount;
  } 
  public double getAmount()
  {
    return amount;
  }
}
```

```java
// 文件名称 CheckingAccount.java
import java.io.*;
 
//此类模拟银行账户
public class CheckingAccount
{
  //balance为余额，number为卡号
   private double balance;
   private int number;
   public CheckingAccount(int number)
   {
      this.number = number;
   }
  //方法：存钱
   public void deposit(double amount)
   {
      balance += amount;
   }
  //方法：取钱
   public void withdraw(double amount) throws
                              InsufficientFundsException
   {
      if(amount <= balance)
      {
         balance -= amount;
      }
      else
      {
         double needs = amount - balance;
         throw new InsufficientFundsException(needs);
      }
   }
  //方法：返回余额
   public double getBalance()
   {
      return balance;
   }
  //方法：返回卡号
   public int getNumber()
   {
      return number;
   }
}
```

### 数据结构

#### 枚举

enum

```java
// 像普通类一样有构造方法和方法
enum Color {
    // 默认 static final
    RED, Green, Blue
}
Color[] c = Color.values();
```

方法：

```java
Color.values();// 值
Color.ordinal();// 索引
Color.valueOf();// 根据值返回常量
```



#### 位集合

#### 向量

#### 栈(Stack)

```java
new Stack();
```

方法：

```java
empty();
peek();
pop();
push();
search();
```

#### 字典(Dictionary)

```java
keys();
elements();
get(key);
isEmpty();
put();
remove();
size();
```



#### 哈希表

#### 属性



### ArrayList

```java
ArrayList<E> objectName =new ArrayList<>();　 // 初始化
```

方法：

```java
add(e);
get(index);
set(index, e);
remove(index);
size();

sort();
```

### LinkedList

声明

```java
ArrayList<E> objectName =new ArrayList<>();　 // 初始化
```

方法：

```java
add();
get();
getFirst();
addFirst();
removeFirst();
getLast();
addLast();
removeLast();
```

### JavaWeb

#### 反射(Reflection)

反射有两个作用：

1、角色反转。不再由JVM（编译期）创建对象，而是在执行是动态地创建对象，对象是未知的。

2、解析类结构。解构类的属性和方法。

获取类：

```java
/* 1. 使用对象 */
Class c = new MyClass().getClass();
/* 2. 使用类 */
Class c = MyClass.class;
/* 3. 使用包名和类名 */
Class c = Class.forName("com.demo.MyClass");
```

创建对象：

```java
/* jdk9 之前 */
MyClass mc = (MyClass)c.newInstance();

/* jdk9 之后 */
MyClass mc = (MyClass)c.getConstructor.newInstance();
```

#### xml解析

xml 标记语言 类似 html， 用来传输 存储数据。

第一行是版本和编码格式

```xml
< ?xml version="1.0" encoding="UTF-8" ?>
<persons>
    <person>
        <name>张三</name>
        <age>18</age>
    </person>
</persons>
```

使用java的 org.xml.sax 进行解析. 常用类 ContentHandle

```tex
// org.xml.sax.ContentHandle
// 需要自己定义
startDocument() // 开始解析文档时做的工作
startElement(String uri, String localName, String qName, Attributes atts)// 解析元素
Characters(char[] ch, int start, int length) 
endElement(String uri, String localName, String qName)
endDocument() // 结束解析时做的

```

解析步骤

```java
import org.xml.sax.*;
import org.xml.sax.helpers.*;
import javax.xml.parsers.*;
/* 继承DefaultHandler 实现其中的方法 */
public class MyXMLReader extends DefaultHandler {
    java.util.Stack tags = new java.util.Stack();
    public MyXMLReader() {
    	super();
    }
    public static void main(String args[]) {
        long lasting = System.currentTimeMillis();
        try {
            /* 实例化，读取xml文件 */
            SAXParserFactory sf = SAXParserFactory.newInstance();
            SAXParser sp = sf.newSAXParser();
            MyXMLReader reader = new MyXMLReader();
            sp.parse(new InputSource("data_10k.xml"), reader);
        } catch (Exception e) {
            e.printStackTrace();
        }

        System.out.println("运行时间：" + (System.currentTimeMillis() - lasting) + "毫秒");
    }
    
    /* 在这里重写方法 */
    public void characters(char ch[], int start, int length) throws SAXException {
        String tag = (String) tags.peek();
        if (tag.equals("NO")) {
            System.out.print("车牌号码：" + new String(ch, start, length));
        }
        if (tag.equals("ADDR")) {
            System.out.println("地址:" + new String(ch, start, length));
        }
    }
 
    
    public void startElement(String uri,String localName,String qName,Attributes attrs) {
        tags.push(qName);
    }
}
```

### 设计模式

#### 单例模式

只允许有一个实例，不允许被实例化，使用静态方法获取实例。

- 只有一个空构造方法，使用 private 修饰。

- 有一个静态get方法，通过这个get方法获取实例。

#### 工厂模式

工厂模式（Factory Pattern）是 Java 中最常用的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。

在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

比如说，我要计算图形的面积，我只需知道一个不同形状的类名和同名的方法即可，方法的具体实现有各个类完成。即各个类共同实现同一个接口。

