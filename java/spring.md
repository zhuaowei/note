## spring-framework

这部分主要介绍Spring的IoC容器和AOP编程（面向切面）。这些技术涵盖了java开发的大部分需求。

### 1、IoC 容器

#### 1.1、IoC和Beans介绍

IoC（Inversion of Control）也称依赖注入（Denpendency Injection，DI）。依赖就是类中依赖的对象，包括成员变量和使用的对象。注入过程使用带参数的构造函数，工厂方法或者通过工厂方法和构造函数后的set方法设置属性。当容器创建这些Bean的时候就完成了依赖注入。

依赖注入是将类之间的依赖关系配置在外部xml文件中，由Spring框架创建和管理Bean实例，而不是直接在构造函数中直接传入，这样降低了类之间的耦合性。

BeanFactory 提供了配置框架和基本功能，ApplicationContext 增加了更多企业特定的功能。一个Bean就是被Spring IoC容器实例化，组装和管理的类。Bean及其之间的依赖关系通过外部文件配置。

#### 1.2、容器概述

<code>org.springframework.context.ApplicationContext</code> 接口负责实例化、配置和组装Bean对象。容器通过读取元数据来获取要实例化、配置和组装的指令，元数据可以来自xml配置，java注解或者java代码。

通常通过实例化 <code>ClassPathXMLApplicationContext</code>> 类或者 <code>FileSystemXmlApplicationContext</code> 类来获得 ApplicationContext 对象。实例化完成后你就可以使用容器了。

```tex
                     | Beans
                    \/
 配置元数据   |-----------|     
-------->   | Spring IoC |  ------------> 使用
              |-----------|
                     
```

配置元数据通过XML中的bean标签配置，也可以使用java注解，在使用了@Conffiguration注解的类中使用@Bean。

#### 1.3、使用 Spring IoC

1.  添加maven依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.6.RELEASE</version>
</dependency>
```

依赖包含了 beans，context，core，expression和jcl（日志相关）等库。

2.  创建Apple类和Child类

Child类依赖Apple类。两个类包含属性的Getter和Setter，toString，无参和有参构造器。

3.  配置元数据

为bean实体类配置实例化属性。

```xml
<!-- applicationContext.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- id:实例唯一标识， class：类名-->
    <bean id="apple1" class="com.zhuaowei.bean.Apple">
        <!-- property:设置属性 title：属性名 value：属性值 -->
        <property name="title" value="红富士" />
        <property name="color" value="红色" />
        <property name="origin" value="中国" />
    </bean>
    <bean id="child1" class="com.zhuaowei.bean.Child">
        <!-- ref：连接，使用定义的bean -->
        <property name="name" value="lily"/>
        <property name="apple" ref="apple1"/>
    </bean>
</beans>
```

4.  使用容器

可以直接编写主类进行测试，也可以使用junit创建测试类。

```java
    @Test
    public void test() {
        // ApplicationContext 是高级工厂的接口，维护不同bean及其依赖关系的注册
        ApplicationContext context = new 
            // 加载xml元数据
            ClassPathXmlApplicationContext("applicationContext.xml");
        // 通过getBean注册实例
        Apple apple1 = context.getBean(Apple.class);
        System.out.println(apple1);
        Child child1 = context.getBean(Child.class);
        System.out.println(child1);
    }
```

#### 1.4、初始化IoC容器

配置Bean有三种方式：

1.  使用XML配置
2.  使用能够Java注解
3.  使用Java代码

##### 1.4.1、XML配置

实例化方式：

1.  构造方法
2.  静态工厂
3.  工厂实例方法

###### 1、构造方法

使用applicationContext.xml

```xml
<!-- applicationContext.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- id:实例唯一标识，相当于变量名， class：类名-->
    <!-- bean 标签中没有设置参数，会使用无参构造方法 -->
    <bean id="apple1" class="com.zhuaowei.bean.Apple">
    </bean>
</beans>
```

容器通过构造方法实例化

```java
ApplicationContext context = new ClassPathXMLApplicationContext("classpath:applicationContext.xml");
```

>   也可以传入一个字符串数组，容器会按照字符串顺序加载xml配置。

如何实现有参构造呢？

```xml
<!-- applicationContext.xml -->
<!-- 使用带参构造 -->
<bean id="apple1" class="com.zhuaowei.bean.Apple">
    <!-- 使用参数名 name(推荐使用) -->
    <constructor-arg name="title" value="红富士" />
    <!-- ... -->
</bean>

<bean id="apple1" class="com.zhuaowei.bean.Apple">
    <!-- 使用带参构造 index -->
    <constructor-arg index="0" value="红富士" />
    <!-- ... -->
</bean>
```

>   Spring 会根据参数个数和类型自动选择构造方法。
>
>   Spring会根据参数类型对传入的参数进行类型转换。例如字符型转浮点型。

###### 2、工厂方法

工厂分为静态工厂和工厂方法。

1、静态工厂

创建实例的方法是静态的（static）。目的是隐藏创建对象的细节，用户只需要根据需求调用相应的方法即可。另外，可以在实例化代码时添加一些额外的东西，例如日志。

创建静态工厂

```java
public class AppleStaticFactory() {
    public static Apple createSweetApple() {
        Apple apple = new Apple();
        apple.setTitle();
        // ...
        return apple;
    }
}
```

xml文件

```xml
<bean id="apple3" class="com.demo.factory.AppleStatic.Facotory" factory-method="createSweetApple" ></bean>
```

2、工厂实例方法

与静态方法有一点不同，就是方法不用static修饰，这样，创建bean实例的时候就需要先创建一个工厂，然后在经过工厂对象调用方法实例化bean。

配置xml也需要作出对应的改变，需要配置两个bean，先配置工厂，再实例化对象。

```xml
<bean id="" class="" ></bean>
<bean id="" factory-bean="" factory-method=""></bean>
```

##### 1.4.2、注解

优势：

-   摆脱繁琐的XML的bean与依赖注入配置。

-   适合轻量级的现代企业应用

-   有更好的可读性。

1.  组件类型注解：声明当前类的功能与职责
2.  自动装配注解：根据属性特征自动注入对象
3.  元数据注解：更细化的辅助IoC容器管理对象

###### 1、组件类型注解

1.  @Component：将被IoC容器实例化并管理
2.  @Controller：MVC中的控制器类
3.  @Service：业务服务类
4.  @Repository：数据持久层，Dao类

要想使用注解，需要在XML配置中开启组件扫描

```xml
<context:component-scan base-package="com.demo">
    <!-- 排除 -->
    <context:exclude-filter type="regex" expression="com.demo.exl.*" />
</context:component-scan>
```

xml配置

```xml
<!-- applicationContext.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.demo">
        
    </context:component-scan>

</beans>
```

>   组件注解默认id是==类名的首字母小写==
>
>   对于不好分类的，可以使用 @Component

```java
// java 类
@Repository("userDao")
public class UserDao() {
    
}
```

###### 2、自动装配注解

分为两种：

1.  按类型装配
    1.  @Autowired
    2.  @Inject
2.  按名称装配（使用Bean的名称，最常用）
    1.  Named
    2.  Resource（最强大）

1、按类型装配（Autowired)

按类型注入，

```java
public class UserService() {
    @Autowired
    // 在属性上注解，会把修饰符改为public，然后直接赋值
    private UserDao userDao;
    public UserService(){
    }
    
    @Autowired
    // 在set方法上注解，会调用set方法
    public void setUserDao(UserDao userDao){
        this.userDao = userDao;
    }
}
```

>   如果有多个子类对象，就不知道使用哪个，这时候使用 @Primary 设置主要的对象，冲突时会使用这个。

2、按名称装配（@Resource）

@Resource 匹配过程：

1.  设置name，按name在容器中将Bean注入。
2.  如果没有设置name：
    1.  以属性名作为name，在容器中匹配Bean，注入。
    2.  按属性名未匹配，按类型匹配，这时候与@Autowired相同。

>   建议：使用name属性或保证属性名与bean名相同

```java
public class DepartmentService() {
    
    @Resource(name = "userDao")
    private UserDao userDao;
}
```

###### 3、元数据注解

辅助IoC容器管理对象，常见的有5个：

1.  @Primary：优先被注入
2.  @PostConstruct：初始化注解 init-method
3.  @PreDestroy：销毁注解 destroy-method
4.  @Scope：设置 scope属性
5.  @Value：注入静态数据

```java
@PostConstruct
public void init() {
    
}

@PreDestroy
public void destroy() {
    
}

@Scope("prototype")
public class UserService() {
}

// 直接注入
@Value("张三")
private String name;
```

@Value可以通过读取xml文件进行动态注入

```java
// 通过变量
@Value("${name}")
private String name;

@Value("${connection.password}")
private String password;
```

config.properties

```properties
name=张三
connection.driver=
connection.url=
connection.username=
connection.password=
```



##### 1.4.3、JavaConfig

优势：

1.  完全摆脱 XML，使用独立的Java类管理对象依赖。
2.  对注解进行集中管理
3.  可在编译时检查，不易出错

四个注解：

1.  @Configuration：描述类，说明当前类是Java Config配置类。
2.  @Bean：描述方法，方法返回对象被IoC容器管理，beanId默认为方法名。
3.  @ImportResource：描述类，加载静态文件，可通过@Value获取。
4.  ComponentScan：描述类，与XML的\<context:component-scan\>相同。

com.demo.config

```java
// 必须使用@Configuration，说明是配置文件
@Configuration
public class Config() {
    @Bean
    // 使用方法创建bean对象，放入IoC容器，beanId = 方法名
    public UserDao userDao() {
        UserDao userDao = new UserDao();
        return userDao;
    }
}
```

依赖注入

```java
@Configuration
public class Config() {
    @Bean
    public UserDao userDao() {
        UserDao userDao = new UserDao();
        return userDao;
    }
    
    // 依赖注入
    // 首先按名称注入，如果没有匹配，按类型注入
    public UserService userService(UserDao userDao){
        UserService userService = new UserService();
        // 使用set方法注入
        userService.setUserDao(userDao);
        return userService;
    }
}
```

>   Java Config同样可以使用@Primary，@Scope等注解辅助管理bean.
>
>   为了兼容其他注解类，可以使用@ComponentScan注解加载其他注解类

```java
@Configuration
// 加载 com.demo 包下的所有注解类
@ComponentScan(basePackages = "com.demo")
public class Config() {
	// 同样可以使用上面的方法进行依赖注入
}
```



#### 1.5、从容器中获取Bean

使用容器的getBean方法获取bean。两种方式本质相同，但推荐使用方式一。

```java
// 方式一
context.getBean("id", Apple.class);

// 方式二
(Apple)context.getBean("id")
```

bean属性 id和name的区别。

-   相同点：都是唯一标识，同一文件中不允许重复，多个文件中可以重复，==新对象覆盖旧对象==。
-   不同点：

id更严格，==一次只能定义一个==

name可以一次定义多个，用逗号或者空格分隔。

>   命名是有意义的，使用驼峰命名法

```xml
<bean name="apple1,apple2" class="com.zhuaowei.bean.Apple">
    <!-- 使用带参构造 index -->
    <constructor-arg index="0" value="红富士" />
    <!-- ... -->
</bean>
```

>   如果 id和name都没有，要使用类的全限定名获取。

#### 1.6、路径表达式

实际项目加载文件的根路径不是 resources，而是target目录下的classes。

配置书写格式：

```text
不包含jar包
classpath:config.xml

包含jar
classpath*:config.xml

绝对路径
file:c/config.xml
```

>   还有模糊搜索，按ascii码升序排列加载
>
>   com/config-*.xml
>
>   com/**/config.xml

#### 1.7、依赖注入

依赖就是两个对象之间的关系，注入就是将依赖关系关联起来。

两种方式

1.  setter方法注入对象
2.  构造方法注入对象

###### 1、setter

默认的property就是使用setter方法注入对象。

如果注入自定义对象，需要先实例化自定义对象，然后再注入。

```xml
<!-- id:实例唯一标识， class：类名-->
<bean id="apple1" class="com.zhuaowei.bean.Apple">
    <!-- property:设置属性 title：属性名 value：属性值 -->
    <property name="title" value="红富士" />
    <property name="color" value="红色" />
    <property name="origin" value="中国" />
</bean>
<bean id="child1" class="com.zhuaowei.bean.Child">
    <!-- ==ref==：连接，使用定义的bean -->
    <property name="name" value="lily"/>
    <property name="apple" ref="apple1"/>
</bean>
```

##### 2、构造方法

参考上面的XML配置。

#### 1.8、IoC实际项目

使用IoC容器实现对象间的解耦，将DAO层和Service层工作分开。

DAO层

当项目发生变化时，例如数据由MySQL转移到Oracle，需要修改DAO层代码，重新实现DAO接口，不需要对Service层进行修改。

```xml
<!-- 当业务发生变化，只需要修改类即可 -->
<bean id="bookDao" class="com.zhuaowei.dao.BookDaoMysqlImpl">
    
</bean>
```

Service层

只需要知道Dao层实例化对象的id即可。

```xml
<!-- 当业务发生变化，只需要修改类即可 -->
<bean id="bookDao" class="com.zhuaowei.service.BookStore">
    <property name="BookDao" ref="BookDao"></property>
</bean>
```

#### 1.9、注入集合对象

集合对象List Set Map等

###### 1、注入List

```xml
<bean>
    <property name="">
        <list>
            <!-- 每一个value都是一个值 -->
            <value>具体值</value>
            <ref></ref>
        </list>
    </property>
    <!-- Set与之类似，只是把list标签换成set，值不会重复 -->
    <!-- set会自动去重 LinkedHashSet -->
</bean>
```

注入Map

```xml
<property name="">
    <!-- LinkedHashMap -->
    <map>
        <entity key="" value-ref="" />
        <entity key="" value-ref="" >
            <bean>
                <!-- 可以在这里设置bean，不用写id和name，这样不会被其他引用 -->
            </bean>
        </entity>
    </map>
</property>
```

注入Properties（键值对）

```xml
<property name="">
    <props>
        <!-- 必须都是 Stirng，常存储信息 -->
        <prop key="">value1</prop>
        <prop key="">value2</prop>
    </props>
</property>
```

>   获取信息：getProperty(key);

#### 1.10、查看容器内对象



```java
// 返回所有Bean的id列表。
String[] beanNames = context.getBeanDefinitionNames();
```

>   内部bean（写在内部不设置id和name）不会获取。
>
>   不写id和name的外部bean会使用类名创建id -> className#0,
>
>   通过上面的 id获取bean，如果不写#0，会获取第一个。不要使用这个特性。

#### 1.11、scope属性

-   scope属性决定对象何时被创建与作用范围
-   scope属性将影响容器内对象个数
-   默认是单例，作用于全局

一共有6个属性值

1.  singleton：默认单例模式，唯一，全局共享，多线程（注意线程安全）
2.  protoype：多例，每次使用创建。线程安全，占用资源多。
3.  request：一次请求
4.  session：一个session
5.  application：一个ServletContext
6.  websocket：每次websocket连接

后面四个与web有关

>   prototype在容器初始化时不会创建，当其他实例引用它时才会创建。
>
>   单例模式的bean是按照顺序创建的。
>
>   如果某个bean被创建后，其属性不会发生变化，就可以使用单例模式。

#### 1.12、生命周期

IoC容器准备初始化解析XML =>  对象实例化，执行构造方法 => 为对象注入属性 => ==调用 init-method 初始化方法== => IoC容器初始化完毕 => 执行业务代码 => IoC 容器准备销毁 => ==调用 destroy-method 释放资源== => IoC容器销毁完毕。

###### 1、init-method

初始化方法：在属性注入完成后进行的初始化工作。

在bean类中实现 init 方法，然后在bean实例化配置中添加 init-method方法。

```xml
<bean id="" class="" init-method="init">
    <property name="" value></property>
</bean>
```

###### 2、destroy-method

释放资源方法

实现 destroy 方法，释放相关资源，然后在bean实例化配置中添加 destroy-method 方法，最后让容器调用 registerShutdownHook() 方法，对象就会自动调用destroy方法。

```xml
<bean id="" class="" init-method="init" destroy-method="destroy">
    <property name="" value></property>
</bean>
```

```java
// 调用销毁容器方法
(ClassPathXmlApplicationContext context).registerShutdownHook();
```

### 2、Spring Test

-   Spring Test是Spring开发中用于测试的模块。
-   Spring Test对JUnit单元测试框架有良好的整合。
-   在单元测试时自动加载IoC容器。

Maven工程依赖Spring-test，利用@RunWith和@ContextConfiguration描述测试用例类。

测试执行过程：

1、导入测试依赖

```xml
<denpendency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.2.6.RELEASE</version>
</denpendency>
```

```java
// 测试程序
// 加载Spring 测试模块
@RunWith(SpringJUnit4ClassRunner.class)
// 加载xml配置依赖
@ContextConfiguration(locations = {"classpath:applicationContext.xml"})
public class SpringTest() {
    // 这个时候就当作IoC容器已经加载完毕
    @Resource
    private UserService userService;
    
    @Test
    public void test() {
        // 可以直接调用IoC容器中的对象
    }
}
```

### 3、AOP面向切面编程

#### 3.1、介绍

指软件运行过程中，运行前后都有可扩展的功能。

AOP(Aspect-Oriented Programming)其实是OOP(Object-Oriented Programing) 思想的补充和完善。我们知道，OOP引进"抽象"、"封装"、"继承"、"多态"等概念,对万事万物进行抽象和封装，来建立一种对象的层次结构，它强调了一种完整事物的自上而下的关系。

但是具体细粒度到每个事物内部的情况，OOP就显得无能为力了。比如日志功能。日志代码往往水平地散布在所有对象层次当中，却与它所散布到的对象的核心功能毫无关系。对于其他很多类似功能，如事务管理、权限控制等也是如此。这导致了大量代码的重复，而不利于各个模块的重用。  

而AOP技术则恰恰相反，它利用一种称为"横切"的技术，能够剖解开封装的对象内部，并将那些影响了多个类并且与具体业务无关的公共行为 封装成一个独立的模块（称为切面）。更重要的是，它又能以巧夺天功的妙手将这些剖开的切面复原，不留痕迹的融入核心业务逻辑中。这样，对于日后横切功能的编辑和重用都能够带来极大的方便。

```tex
Spring中的可插拔组件技术

          |            |
          |            |
[       权限切面 （Aspect）       ]
          |            |
    [软件模块A]    [软件模块B]
          |            |
   [         日志切面         ]
          |            |
```

权限切面控制着是否有权限运行下一模块

AOP就相当于一个插件，原有的模块不会感知到插件的存在，去掉插件后，只需要稍微修改配置即可。

-   将通用的、与业务无关的功能抽象封装为切面类。
-   切面可配置在目标方法执行钱、后运行，真正做到即插即用。
-   在不修改源码的情况下对程序行为进行扩展。

#### 3.2、初识AOP

maven依赖

```xml
<!-- AOP的底层依赖 -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.5</version>
</dependency>
```

applicationContext.xml 的 schema

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">
    
</beans>
```



com.demo.aspect.MethodAspect

```java
// 切面类
public class MethodAspect() {
    
    // 切面方法
    // JoinPoint 连接点，通过连接点获取目标类/方法的信息
    public void printExecutionTime(JoinPoint joinPoint) {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss SSS");
        String now =  sdf.format(new Date());
        String className = joinPoint.getTarget().getClass().getName();
        String methodName = joinPoint.getSignature().getName();
        
    }
}
```

AOP配置

```xml
<!-- 配置AOP -->
<beans>
    <!-- 首先实例化业务类和dao类 -->
    <bean></bean>
    <!-- 实例化切面类，与其他bean相同 -->
    <bean id="methodAspect" class="com.demo.aspect.MethodAspect"></bean>

    <!-- 配置AOP -->
    <aop:config >
        <!-- 描述切点的作用范围，com.demo包下的所有类和方法 -->
        <aop:pointcut id="pointcut" expression="execution(public * com.demo..*.*(..))"></aop:pointcut>
        <aop:aspect ref="methodAspect">
            <!-- before：通知，方法执行前 -->
            <aop:before method="printExecutionTime" point-ref="pointcut"></aop:before>
        </aop:aspect>
    </aop:config>
</beans>
```

#### 3.3、AOP关键概念

1.  Eclipse AspectJ：一种基于Java平台的面向切面编程的语言。
2.  pring AOP：使用AspectJWeaver实现类与方法匹配。确定作用范围
3.  Spring AOP利用代理模式实现对象运行时功能扩展（核心）

关键概念

1.  Aspect：切面，具体的功能类，通常一个切面只实现一个功能。
2.  Target Class/Method：目标类、方法，真正执行的业务代码。
3.  PointCut：切入点，使用execution表达式确定作用范围。
4.  JoinPoint：连接点，切面运行过程中是包含了目标类、方法元数据的对象。
5.  Advice：通知，说明具体的切面的执行时机，Spring包含了5中不同类型的通知。

AOP配置过程

1.  依赖AspectJ
2.  实现切面类、方法
3.  配置AspectBean
4.  定义PointCut
5.  配置Advice

schame

#### 3.4、JoinPoint

核心方法有三个：

1.  getTartget()：获取IoC容器内目标对象
2.  getSignature()：获取目标方法
3.  getArgs()：获取目标方法参数

​    对getArgs遍历可以得到每一个参数，对获取程序运行信息非常有用。

```java
Object[] args = joinPoint.getArgs();
for(Object arg : args) {
    System.out.println(args);
}
```

#### 3.5、PointCut

切点表达式，确定插件运行的范围。

对于一个普通的类方法，它的格式可能是这样的

`public void com.demo.service.UserService.createUser(arg1, arg2, ...)`

而且切点表达式就是对上述结构的匹配。例如：

`execution(public * com.demo..*.*(..))`

这个表达式有那些东西组成呢？

`execution(修饰符匹配式? 返回类型匹配式 类名匹配式? 方法名匹配式(参数匹配式) 异常匹配式?)`

带 ==?== 的代表可以没有，因此，必须要有的只有三个，

1.  返回类型
2.  方法名
3.  参数

符号含义：

1.  *：通配符，可以指代任何返回值类型，参数类型，包名，方法名，任意个数的字符串
2.  ..：当前包及其子包
3.  (..)：任意个数，任意类型的参数

>   public 是默认修饰符，不写也可以。

#### 3.6、Advice

 共有5种通知类型：

1.  before：方法执行前通知，可以获取参数
2.  after-running：方法执行正常后通知（try），可以返回对象
3.  after-throwing：方法抛出异常后通知（catch），可以获取异常对象
4.  after：方法执行后通知（finally），不能获取返回对象和异常对象。
5.  around：

除了通知，还有一种引介增强，它可以根据环境改变对象方法的参数，增加方法等，不常用，而且使用复杂，了解即可。

通知使用：

1.  before在之前已经演示过。
2.  after-running：

```java
// aspect类方法
public void doAfterRunning(JoinPoint joinPoint, Object ret) {
    System.out.println("<-" + ret);
}
```

3.  after-throwing:

```java
// aspect类方法
public void doAfterRunning(JoinPoint joinPoint, Throwable th) {
    System.out.println("<-" + th);
}
```

4.  after与before类似

上面两个的xml配置

```xml
<beans>
    <!-- 首先实例化业务类和dao类 -->
    <bean></bean>
    <!-- 实例化切面类，与其他bean相同 -->
    <bean id="methodAspect" class="com.demo.aspect.MethodAspect"></bean>

    <!-- 配置AOP -->
    <aop:config >
        <!-- 描述切点的作用范围，com.demo包下的所有类和方法 -->
        <aop:pointcut id="pointcut" expression="execution(public * com.demo..*.*(..))"></aop:pointcut>
        <aop:aspect ref="methodAspect">
            <!-- before：通知，方法执行前 -->
            <aop:before method="printExecutionTime" point-ref="pointcut"></aop:before>
            <!-- after-running returning：返回对象 -->
            <aop:after-running method="doAfterRunning" returning="ret" point-ref="pointcut"></aop:before-running>
            <!-- after-throwing throwing：异常对象 -->
            <aop:after-throwing method="doAfterThrowing" throwing="th" point-ref="pointcut"></aop:after-throwing>
            <!-- after：与before类似 -->
            <aop:after method="printExecutionTime" point-ref="pointcut"></aop:after>
        </aop:aspect>
    </aop:config>
</beans>
```

5.  around环绕通知

最强大的通知，可以完成上面的所有功能。

```java
// 
public class MethodChecker {
    /**
     * 测试round环绕通知方法
     * @param pjp JoinPoint的高级版
     * @return
     */
    public Object check(ProceedingJoinPoint pjp) throws Throwable {

        try {
            // 运行前的工作
            long start = System.currentTimeMillis();
            // 运行方法，如果不写，就不会运行，返回值是执行方法的返回值
            Object ret = pjp.proceed();
            // 运行后的工作
            long end = System.currentTimeMillis();
            long runTime = end - start;
            System.out.println(pjp.getTarget().getClass().getName() +
                    "." + pjp.getSignature().getName() +
                    " - runtime:" + runTime + "ms");

            return ret;
        } catch (Throwable throwable) {
            // 丢出异常，不自己消化，便于排错
            throw throwable;
        }
    }
}
```

xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 业务等 -->
    <bean id="userDao" class="com.zhuaowei.dao.UserDao"></bean>

    <bean id="userService" class="com.zhuaowei.service.UserService">
        <property name="userDao" ref="userDao"></property>
    </bean>

    <bean id="methodChecker" class="com.zhuaowei.aspect.MethodChecker"></bean>

    <!-- 配置AOP -->
    <aop:config>
        <aop:pointcut id="pointCut" expression="execution(public * com.zhuaowei..*.*(..))"/>
        <aop:aspect ref="methodChecker">
            <aop:around method="check" pointcut-ref="pointCut"></aop:around>
        </aop:aspect>
    </aop:config>
```

#### 3.7、注解

注解模式只需在xml中配置一行即可，然后就可以在java中使用注解。

```xml
<beans>
    <!-- 注册IoC容器 -->
    <context:componetn-scan base-package="com.demo"/>
    
    <!-- aop自动代理，启用注解模式 -->
    <aop:aspectj-autoproxy />
</beans>
```



```
// 标记为组件
@Component
// 标记为切面类
@Aspect
public class MethodChecker {
    /**
     * 测试round环绕通知方法
     * @param pjp JoinPoint的高级版
     * @return
     */
     // 环绕通知，其他通知也可以用，不过大部分是使用这个，指定范围
     @Around("execution(* com.demo..*.*(..))")
    public Object check(ProceedingJoinPoint pjp) throws Throwable {

        try {
            // 运行前的工作
            long start = System.currentTimeMillis();
            // 运行方法，如果不写，就不会运行，返回值是执行方法的返回值
            Object ret = pjp.proceed();
            // 运行后的工作
            long end = System.currentTimeMillis();
            long runTime = end - start;
            System.out.println(pjp.getTarget().getClass().getName() +
                    "." + pjp.getSignature().getName() +
                    " - runtime:" + runTime + "ms");

            return ret;
        } catch (Throwable throwable) {
            // 丢出异常，不自己消化，便于排错
            throw throwable;
        }
    }
}
```

#### 3.8、AOP底层原理

AOP底层使用代理模式实现功能的动态扩展，包括两种形式：

1.  目标类实现接口，通过JDK动态代理实现功能扩展
2.  没有接口，通过CGLib组件实现功能扩展。

##### 1、什么是代理模式？

通过代理对象对源对象的实现功能扩展。例如租房中介，租户找中介租房，房东找中介管理钥匙。

代理类和功能类继承相同的接口，代理类将功能类对象作为属性，在实现接口时添加额外的功能。因为代理类和功能类实现相同的接口，在使用时可以用代理类1作为参数实例化代理类2，无限套娃。类似下面这种

`new UserServiceProxy1(new UserServiceProxy2(new UserServiceImpl()))`

##### 2、静态代理，动态代理

在代理类中手动创建委托类的方法称为静态代理。自动创建的是动态代理，应用java的反射机制，可以动态地改变委托类。

具体实现案例

```java
// UserService 接口
public interface UserService {
    void addUser();
}
```

```java
// UserServiceImpl 实现接口
// 必须实现接口
public class UserServiceImpl implements UserService {
    @Override
    public void addUser() {
        System.out.println("添加用户");
    }
}
```

```java
// 代理类 及测试主类
public class ProxyInvocationHandler implements InvocationHandler {
    // 目标对象
    private Object target;
    private ProxyInvocationHandler(Object target) {
        this.target = target;
    }

    /**
     * 应用反射机制，执行方法
     * @param proxy 代理类对象
     * @param method 执行方法
     * @param args 方法的参数
     * @return 返回执行方法的返回值
     * @throws Throwable 目标方法抛出的异常
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // 扩展功能
        System.out.println("添加扩展功能");

        // 执行方法
        Object ret = method.invoke(target, args);
        return ret;
    }

    public static void main(String[] args) {
        // 创建目标对象
        UserService userService = new UserServiceImpl();
        ProxyInvocationHandler invocationHandler = new ProxyInvocationHandler(userService);
        // 动态创建代理类
        UserService userServiceProxy = (UserService)Proxy.newProxyInstance(
                userService.getClass().getClassLoader(),
                userService.getClass().getInterfaces(),
                invocationHandler
        );
        userServiceProxy.addUser();
    }
}
```

##### 3、CGLib组件实现功能扩展

Code Generate Lib 运行时字节码增强技术

Spring AOP扩展无接口类使用CGLib

AOP会运行时生成目标继承类字节码的方式进行行为扩展，即自动生成继承类，`Service` -> `Service$$EnhancerByCGLib extends Service` ，执行时会直接面向继承类。

>   后代版本继承名有变化 Service$$EnhancerBySpringCGLib

### 4、Spring JDBC与事务管理

#### 4.1、Spring JDBC 

-   用于处理关系型数据库的模块

-   对JDBC API进行封装，简化开发。

-   JdbcTemplate是Spring JDBC的核心类，提供CURD方法。

##### 1、配置过程

 使用步骤：导入依赖 => 配置 applicationContext.xml DataSource数据源 => 在dao层注入JdbcTeplate对象，实现CURD

1.  导入Maven依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.6.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.6.RELEASE</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <!-- 最好与mysql版本一致 -->
    <version>5.7.35</version>
</dependency>
```

2.  配置DataSource

```xml
<!-- 数据源 -->
<bean id="dataSource" class="org.springframework.jdbc.">
    <property name="driver" value="" />
    <!-- &符号要转义 -> &amp; -->
    <property name="url" value="" />
    <property name="username" value="" />
    <property name="password" value="" />
</bean>
<!-- JdbcTemplate提供数据CURD的API -->
<bean id="jdbcTemplate" class="org." >
    <property name="" ref="dataSource" />
</bean>
```

实现Employee实体类和EmployeeDao类

```java
// Employee包括属性的getter和setter，toString方法
// 省略

// EmployeeDao
public class EmployeeDao {
    // 实现CURD操作
    private JdbcTemplate jdbcTemplate;
    
    public User findUserById(Integer id) {
        String sql = "select * from mybatis.user where id = ?";
        // queryForObject
        Employee employee = (Employee)jdbcTemplate.queryForObject(sql, new Object[]{id}, new BeanPropertyRowMapper<Employee>(Employee.class));

        return employee;
    }
}
```

3.  注入JdbcTemplate

```java
<!-- 声明employeeDao -->
<bean id="employeeDao" class="com.demo.dao.EmployeeDao" >
    <!-- 注入JdbcTemplate -->
    <property name="jdbcTemplate" ref="jdbcTemplate" />
</bean>
```

##### 2、数据查询

###### 1、将每一行查询结果映射为一个实体类

```java
    public List<User> findAll(){
        String sql = "select * from mybatis.user";
        // query
        List<User> users = (List<User>) jdbcTemplate.query(sql, new BeanPropertyRowMapper<User>(User.class));
        return users;
    }
```

###### 2、映射为Map

```java
    public List<Map<String, Object>> findMapperById(Integer id) {
        String sql = "select userName as uname, pwd as password from mybatis.user where id = ?";
        // queryForList 不用写new BeanPropertyRowMapper<>()
        List<Map<String, Object>> maps = jdbcTemplate.queryForList(sql, new Object[]{id});
        return maps;
    }
```

##### 3、数据增删改

增删改都使用update。增删改都返回int，代表增删改的条数。

###### 1、insert

```java
public int insert(User user) {
    String sql = "insert into mybatis.user (userName, pwd) values (?, ?)";
    int count = jdbcTemplate.update(sql, new Object[]{
            user.getUserName(), user.getPwd()
    });
    return count;
}
```

###### 2、update

```java
public int update(User user) {
    String sql = "update mybatis.user set userName = ?, pwd = ? where id = ?";
    int count = jdbcTemplate.update(sql, new Object[]{
            user.getUserName(), user.getPwd(), user.getId()
    });
    return count;
}
```

###### 3、delete

```xml
public int delete(Integer id) {
    String sql = "delete from mybatis.user where id = ?";
    int count = jdbcTemplate.update(sql, new Object[]{id});
    return count;
}
```

#### 4.2、事务管理

引入事务区的概念，事务区作为sql语句的缓冲区。

##### 1、编程式事务

编程式事务是使用程序代码手动控制sql操作什么时候提交。

使用编程式事务要导入一个关键的bean，即事务管理器对象（TransactionManager）

下面声明它，使用IoC容器管理它。

```xml
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager" >
    <!-- 使用上面声明的DataSource -->
    <property name="dataSource" ref="dataSource"></property>
</bean>
```

然后修改Service类，将TransactionManager作为依赖注入到service类中，具体做法是：

1、将TransactionManager作为service类的属性，然后生成getter和setter方法，

2、在xml配置中注入service对象。

```xml
<bean id="userService" class="com.demo.service.UserService" >
    <property name="userDao" ref="userDao"></property>
    <property name="transactionManager" ref="transactionManager"></property>
</bean>
```

最后，使用代码管理事务的流程。

```java
public void batchInsert(){
    // 定义默认的事务配置
    DefaultTransactionDefinition definition = new DefaultTransactionDefinition();
    // 开始事务，并且返回一个状态，标记事务的状态
    TransactionStatus status = transactionManager.getTransaction(definition);

    try {
        for (int i = 0; i < 10; i++) {
            userDao.insert(new User(i, "name" + i, "pwd" + i));
        }
        // 所有的操作都被存储到事务缓存中，使用commit进行提交，需要传入一个状态码
        transactionManager.commit(status);
    } catch(Exception e) {
        e.printStackTrace();
        // 如果发生异常，则回滚，也是需要传入一个状态
        transactionManager.rollback(status);
    }

}
```

##### 2、声明式事务

在不修改源码的条件下配置自动事务控制，本质是AOP环绕通知。

###### 1、导入依赖

```xml
<!-- AOP的底层依赖 -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.5</version>
</dependency>
<!-- spring最基本的依赖 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.6.RELEASE</version>
</dependency>
<!-- mysql依赖 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.34</version>
</dependency>
```

###### 2、导入Schema

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

</beans>
```

###### 3、配置事务，绑定切点

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- Spring JDBC -->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver" />
        <!-- &符号要转义 -> &amp; -->
        <property name="url" value="jdbc:mysql://47.98.103.25:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8" />
        <property name="username" value="qyh" />
        <property name="password" value="qyh443012QYH#" />
    </bean>

    <!-- JdbcTemplate提供数据CURD的API -->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate" >
        <property name="dataSource" ref="dataSource" />
    </bean>

    <!-- 数据持久层和service层代码 -->
    <bean id="userDao" class="com.zhuaowei.dao.UserDao">
        <property name="jdbcTemplate" ref="jdbcTemplate"/>
    </bean>

    <bean id="userService" class="com.zhuaowei.service.UserService">
        <property name="userDao" ref="userDao"></property>
    </bean>

    <!-- 事务管理对象，引用连接池 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!-- AOP配置，作用范围 -->
    <aop:config>
        <aop:pointcut id="pointcut" expression="execution(* com.zhuaowei..*Service.*(..))"/>
        <!-- 绑定事务管理和pointcut -->
        <aop:advisor advice-ref="txAdvisor" pointcut-ref="pointcut"/>
    </aop:config>

    <!-- 配置事务管理，tx就代表事务管理 transaction -->
    <tx:advice id="txAdvisor" transaction-manager="transactionManager">
        <tx:attributes>
            <!-- 配置什么养的方法名，执行什么样的事务，propagation表示是否使用事务和使用方式 -->
            <tx:method name="batchInsert" propagation="REQUIRED"/>
            <!-- 对于所有的批量操作使用事务 -->
            <tx:method name="batch*" propagation="REQUIRED"/>
            <!-- 还可以批量设置，查找就不需要事务了，设置只读 -->
            <tx:method name="find*" propagation="NOT_SUPPORTED" read-only="true"/>
            <!-- get同样 -->
            <tx:method name="get*" propagation="NOT_SUPPORTED" read-only="true"/>
        </tx:attributes>
    </tx:advice>

</beans>
```

##### 3、事务传播方式

事务传播方式是事务在方法嵌套的时候处理事务的方式（行为），事务传播一共有7中类型，其中3中是常用的，其他了解即可。

1.  ==REQUIRED（默认）==：如果没有事务，则新建事务；如果存在事务，则加入事务。
2.  SUPPPORTS：支持当前事务，如果没有，则按非事务方式执行。
3.  MANDATORY：使用当前的事务；如果没有事务，则抛出异常。
4.  ==REQUIRES_NEW==：没有事务则新建事务；如果有事务，把事务挂起。
5.  ==NOT_SUPPORTED==：非事务方式执行；如果有事务，则挂起事务。
6.  NEVER：非事务方式执行，如果存在事务，则抛出异常。
7.  NESTED：如果当前存在事务，则在嵌套事务内执行；如果当前没有事务，则执行与REQUIRED类似的操作。

##### 4、注解形式声明式事务

使用注解方式就不用配置麻烦的xml，只需简单的几句即可。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- Spring JDBC 数据源 -->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver" />
        <!-- &符号要转义 -> &amp; -->
        <property name="url" value="jdbc:mysql://47.98.103.25:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8" />
        <property name="username" value="qyh" />
        <property name="password" value="qyh443012QYH#" />
    </bean>

    <!-- JdbcTemplate提供数据CURD的API -->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate" >
        <property name="dataSource" ref="dataSource" />
    </bean>

    <!-- 事务管理对象，引用连接池 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!-- 使用注解声明对象 -->
    <context:component-scan base-package="com.demo"/>
    
    <!-- 使用注解声明事务 -->
    <tx:annotation-driven transaction-manager="transactionManager"/>

</beans>
```

注解可以声明在类上面，也可以写在方法上面。

写在类上，类里面的方法都使用此方式管理事务，方法上只影响一个方法。如果方法和类上面都有，优先使用方法上的。

声明事务使用 `@Transactional` ，这个是默认使用 REQUIRED，还可以使用其他属性，例如

`@Transactional(propagation = Propagation.NOT_SUPPORTED, readOnly = true)`

