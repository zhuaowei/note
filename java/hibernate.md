# Hibernate

## 简介

hibernate是java语言中ORM解决方案的一种，类似的还有Mybatis，spring data jpa。



## 第一个hibernate程序

首先，新建一个maven工程，导入依赖 pom.xml

```xml
<!-- hibernate -->
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-core</artifactId>
    <version>5.4.12.Final</version>
</dependency>

<!-- 为了能够加载java包下的xml文件，需要在maven配置中添加路径 -->
<build>
    ...
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
    </resources>
</build>
```

然后根据数据库的表和表之间的关系编写实体类。

>   User.java

```java
package com.zhuaowei.boottest.entity;

import lombok.Data;
import java.util.Set;

@Data
public class User {
    private int id;
    private String username;
    private String nickname;
    private String password;
    private String salt;

    private Set roles;
}
```

>   Role.java

```java
package com.zhuaowei.boottest.entity;

import lombok.Data;

import java.util.Set;

/**
 * @ClassName: Role
 * @Description: TODO add a description
 * @Author: zhuaowei
 * @Date: 2021/9/16
 * @Version: 1.0
 */
@Data
public class Role {
    private int id;
    private String name;
    private String description;

    private Set users;
}
```

然后新建hibernate配置文件。

>   src/main/resources/hibernate.cfg.xml

```xml
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <!-- 配置数据源 -->
        <property name="connection.url">jdbc:mysql://localhost:3306/blog?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8</property>
        <property name="connection.username">root</property>
        <property name="connection.password">zhuaowei0521++</property>
        <property name="connection.driver_class">com.mysql.jdbc.Driver</property>

        <!-- 数据库方言 -->
        <property name="dialect">org.hibernate.dialect.MySQLDialect</property>
        <property name="c3p0.acquire_increment">10</property>
        <property name="c3p0.idle_test_period">10000</property>
        <property name="c3p0.min_size">5</property>
        <property name="c3p0.max_size">20</property>
        <property name="c3p0.max_statements">10</property>
        <property name="c3p0.timeout">5000</property>
        
        <property name="show_sql">true</property>
        <property name="format_sql">true</property>
        <property name="hibernate.hbm2ddl.auto"></property>

        <!-- mapping 每配置一个mapping文件都要添加 -->
        <mapping resource="com/zhuaowei/boottest/entity/User.hbm.xml"></mapping>
        <mapping resource="com/zhuaowei/boottest/entity/Role.hbm.xml"></mapping>
    </session-factory>
</hibernate-configuration>
```

然后对所有需要关系映射的实体类配置mapping

>   User.hbm.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>
    <class name="com.zhuaowei.boottest.entity.User">
        <id name="id" type="java.lang.Integer">
            <column name="id"></column>
            <generator class="identity"></generator>
        </id>
        <property name="username" type="java.lang.String">
            <column name="username"></column>
        </property>
        <property name="nickname" type="java.lang.String">
            <column name="nickname"></column>
        </property>
        <property name="password" type="java.lang.String">
            <column name="password"></column>
        </property>
        <property name="salt" type="java.lang.String">
            <column name="salt"></column>
        </property>
        <set name="roles" table="user_role">
            <key column="user_id"></key>
            <many-to-many class="com.zhuaowei.boottest.entity.Role" column="role_id"></many-to-many>
        </set>
    </class>
</hibernate-mapping>
```

>   Role.hbm.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>
    <!-- 实体类 -->
    <class name="com.zhuaowei.boottest.entity.Role">
        <!-- id属性 -->
        <id name="id" type="java.lang.Integer">
            <column name="id"></column>
            <generator class="identity"></generator>
        </id>
        <property name="name" type="java.lang.String">
            <column name="name"></column>
        </property>
        <property name="description" type="java.lang.String">
            <column name="description"></column>
        </property>
        <!-- 关系映射，table是映射关系表 -->
        <set name="users" table="user_role">
            <!-- key是自己的id -->
            <key column="role_id"></key>
            <!-- many-to-many 多对多关系 -->
            <!-- 找到映射的实体类和对应的关系表中的id -->
            <many-to-many class="com.zhuaowei.boottest.entity.User" column="user_id"></many-to-many>
        </set>
    </class>
</hibernate-mapping>
```

-   class 对应的实体类
-   id 设置主键属性
-   generator 主键生成策略 increment表示自增，数据库中不需要设置AUTO_INCREMENT，identity需要数据库设置自增。
-   property 实体类属性
-   column 对应的数据库中的列
-   set 设置集合属性
-   key 本实例id属性
-   many-to-many 多对多属性，除此之外还有 one-to-many, many-to one，这两个只需要在 one 的部分设置，而且不需要写 column属性。

配置完成后开始运行测试

>   Test.java

```java
package com.zhuaowei.boottest;

import com.zhuaowei.boottest.entity.Role;
import com.zhuaowei.boottest.entity.User;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;
import java.util.Set;

import java.util.HashSet;


/**
 * @ClassName: Test
 * @Description: TODO add a description
 * @Author: zhuaowei
 * @Date: 2021/9/16
 * @Version: 1.0
 */

public class Test {
    public static void main(String[] args) {
        // 加载hibernate配置，默认是 hibernate.cfg.xml
        Configuration configuration = new Configuration().configure();
        // 创建session工厂
        SessionFactory sessionFactory = configuration.buildSessionFactory();
        // 创建session
        Session session = sessionFactory.openSession();

        // 创建用户
        User user = new User();
        user.setUsername("zhangsan");
        user.setNickname("张三");
        user.setPassword("123");
        user.setSalt("99");

        // 创建角色
        Role role = new Role();
        role.setName("member");
        role.setDescription("会员");

        Set<User> users = new HashSet<>();
        users.add(user);

        // 添加关系
        role.setUsers(users);

        // 保存
        session.save(user);
        session.save(role);
        session.beginTransaction().commit();

        session.close();
    }
}
```

至此，第一个hibernate程序就完成了。

## 关系映射



## Hibernate 延迟加载

惰性加载，懒加载

使用延迟加载可以提高程序的运行效率，java程序与数据库交互的频次越低，程序运行的效率越高。

不使用延迟加载，hibernate在查询主表的时候会自动查询从表，无论你是否用到从表的数据。

使用延迟加载会根据你的需求选择只查询主表还是主表从表一起查。

在多对多的关系中，不使用延迟加载会使用关联查询。

如何使用延迟加载呢?在主表set关系映射配置中使用lazy属性，并设置true。

>   xxx.hbm.xml

```xml
<set name="roles" table="user_role" lazy="true">
            <key column="user_id"></key>
            <many-to-many class="com.zhuaowei.boottest.entity.Role" column="role_id"></many-to-many>
        </set>
```

-   lazy除了可以设置true和false，还可以设置extra
-   extra是比true更智能的，更懒的方法，例如，你要查询roles的长度，它可以直接使用sql函数只查询长度。而不查询其他信息。
-   no-proxy 
-   proxy

测试

>   Test.java

```java
package com.zhuaowei.boottest;

import com.zhuaowei.boottest.entity.Role;
import com.zhuaowei.boottest.entity.User;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;
import java.util.Set;

import java.util.HashSet;


/**
 * @ClassName: Test
 * @Description: TODO add a description
 * @Author: zhuaowei
 * @Date: 2021/9/16
 * @Version: 1.0
 */

public class Test {
    public static void main(String[] args) {
        // 加载hibernate配置，默认是 hibernate.cfg.xml
        Configuration configuration = new Configuration().configure();
        // 创建session工厂
        SessionFactory sessionFactory = configuration.buildSessionFactory();
        // 创建session
        Session session = sessionFactory.openSession();

        // 查询
        User user = session.get(User.class, 3);
        System.out.println(user);
//        System.out.println(user.getRoles());
//        System.out.println(user.getRoles().size());

        session.close();
    }
}
```

>   DEBUG:
>
>   在进行查询时由于对象之间的相互引用，会导致无限查询，最终导致堆栈溢出。
>
>   user -> roles -> users -> roles -> ...
>
>   主要原因是toString方法中引用了关联对象，导致数据库一直在查询。
>
>   解决方法：使用 Getter和Setter注解，重写toString方法，不要包含关联对象。使用@Data会覆盖现有的hashCode()和toString()方法。

## 配置文件详解

-   hibernate.xml
-   xxx.hbm.xml



#### 一、配置文件

>   hibernate.xml 全局环境配置

1、schama

```xml
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <!-- 环境配置在这里 -->
    </session-factory>
</hibernate-configuration>
```

2、数据源

```xml
<property name="connection.url">jdbc:mysql://localhost:3306/blog?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8</property>
<property name="connection.username">root</property>
<property name="connection.password">zhuaowei0521++</property>
<property name="connection.driver_class">com.mysql.jdbc.Driver</property>

```

3、集成c3p0，配置数据库连接池

```xml
<property name="c3p0.acquire_increment">10</property>
<property name="c3p0.idle_test_period">10000</property>
<property name="c3p0.min_size">5</property>
<property name="c3p0.max_size">20</property>
<property name="c3p0.max_statements">10</property>
<property name="c3p0.timeout">5000</property>
```

4、数据库方言，打印sql语句等

```xml
<property name="dialect">org.hibernate.dialect.MySQLDialect</property>
<property name="show_sql">true</property>
<property name="format_sql">true</property>
<property name="hibernate.hbm2ddl.auto"></property>
```

-   hibernate.hbm2ddl.auto 是否自动生成数据库
    -   update 如果有表，则直接使用，如果没有则创建
    -   create 无论有没有表，都会重新创建
    -   create-drop 初始化创建表，运行结束删除表
    -   validate 如果表关系与映射文件不符，则直接报错

5、注册实体关系映射文件

```xml
<mapping resource="com/zhuaowei/boottest/entity/User.hbm.xml"></mapping>
<mapping resource="com/zhuaowei/boottest/entity/Role.hbm.xml"></mapping>
```



#### 二、hbm文件

>   xxx.hbm.xml 实体关系映射文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping package="com.zhuaowei.boottest.entity">
    <class name="">
        <id name="id" type="java.lang.Integer">
            <column name="id"></column>
            <generator class="identity"></generator>
        </id>
        <property name="username" type="java.lang.String">
            <column name="username"></column>
        </property>
        <property name="nickname" type="java.lang.String">
            <column name="nickname"></column>
        </property>
        <property name="password" type="java.lang.String">
            <column name="password"></column>
        </property>
        <property name="salt" type="java.lang.String">
            <column name="salt"></column>
        </property>
        <set name="roles" table="user_role" lazy="true">
            <key column="user_id"></key>
            <many-to-many class="Role" column="role_id"></many-to-many>
        </set>
    </class>
</hibernate-mapping>
```

**hibernate-mapping属性**

-   package 实体类的包名，设置之后可以直接写实体类的名字，如果有重名的，建议写全限定名
-   schema 数据库schema名称
-   catalog 数据库catalog名称
-   default-cascade 默认的级联关系，默认为none
-   default-access hibernate用来访问属性的策略
-   default-lazy 没有指定lazy属性的java属性和集合类，hibernate会采用指定的加载风格。默认为true
-   auto-import 指定是否可以在查询语句中使用全限定名，默认为true。如果项目中有两个重名的持久化类，最好在对应的映射文件中配置为false

**class属性**（会覆盖hibernate-mapping）

-   name 实体类名
-   table 数据表名
-   schema 
-   datalog
-   proxy 指定一个接口，在延迟加载时作为代理使用
-   dynamic-update 动态更新。更新时只更新改变的属性，提高sql运行效率
-   dynamic-insert 动态添加。添加时，只添加对象中有值的属性
-   where 添加hql查询条件

```xml
<class name="User" table="user" dynamic-update="true" dynamic-insert="true" where="id = 3">
    ...
</class>
```



**id属性**

-   name 实体类属性名

-   type 实体类属性数据类型

>   java实体类的属性数据类型必须与数据库字段数据类一致。
>
>   java数据类型映射到hibernate映射类型，在映射到sql数据类型

-   column 数据表的主键字段名
-   generator 主键生成策略
    -   hilo算法
    -   increment hibernate自增
    -   identity 数据库自增
    -   native 本地策略：根据底层数据库自动选择书简的生成策略
    -   uuid.hex算法
    -   select算法

**property属性**

-   name 实体类的属性名
-   column 数据库表字段名
-   type java数据类型
-   update 是否可以修改 默认true
-   insert 是否可以添加 默认true
-   lazy 延迟加载策略

**实体关系映射文件**

1、inverse 放弃维护

是否将维护权交给对方，默认false，双方都在维护

一对多：一个用户可以有多个订单，java中使用 set 表示。

多对一：多个订单可以对应一个用户

一对多和多对一是相对的，如果两方都要维护这个关系，会造成重复的主外键约束关系。

如何避免出现这种情况？

1、java代码中去掉一方的维护关系代码。

2、通过配置解决，让一方放弃维护。

一般是多的一方维护，所以如果是客户和订单的关系，客户一方放弃维护，`inverse=“true”`

```xml
<set name="orders" table="orders" lazy="extra" inverse="true">
    <key column="cid"></key>
    <one-to-many class="Order"></one-to-many>
</set>
```

2、cascade 级联删除

当我们删除有外键约束的记录时，会因为外键约束而删除失败。例如我们删除客户数据，订单数据需要客户数据，不让删除。

如何解决呢？

1、删除从表的记录

删除之前先删除从表外键约束记录，最后再删除主表数据。例如，删除客户数据时，先查询它的订单数据，全部删除后再删除客户数据。

```java
Customer customer = session.get(Customer.class, 1);
Iterator<Order> orders = customer.getOrders().iterator();
while(orders.hasNext()) {
    session.delete(orders.next());
}
session.delete(customer);
```

2、cascade 使用级联删除

在多的一方设置 `cascade="delete"`

```xml
<set name="orders" table="orders" lazy="extra" inverse="true" cascade="delete">
    <key column="cid"></key>
    <one-to-many class="Order"></one-to-many>
</set>
```

这样就不需要迭代，直接删除customer

## Hibernate HQL

HQL（Hibernate Query Language）是一种hibernate提供的一种查询机制，与sql类似，不同的是HQL是面向对象的。

HQL不能直接参与数据库的交互，而是一个中间层语言。

java -> HQL ->Hibernate -> SQL -> DB

HQL只能完成查询、修改、删除，无法实现新增操作。

1、查询语句

查询表中的所有数据，自动完成对象的封装，返回list集合。

HQL查询from后面必须使用类名，不能使用表名。可以使用全限定名，否则会警告，可忽略。

```java
package com.zhuaowei.boottest;

import com.zhuaowei.boottest.entity.User;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;
import org.hibernate.query.Query;
import org.junit.jupiter.api.Test;

import java.util.List;

/**
 * @ClassName: HQLTest
 * @Description: TODO add a description
 * @Author: zhuaowei
 * @Date: 2021/9/16
 * @Version: 1.0
 */

public class HQLTest {
    @Test
    public void hqlTest() {
        // 加载hibernate配置，默认是 hibernate.cfg.xml
        Configuration configuration = new Configuration().configure();
        // 创建session工厂
        SessionFactory sessionFactory = configuration.buildSessionFactory();
        // 创建session
        Session session = sessionFactory.openSession();

        /* HQL语句查询 */
        String hql = "from User";
        /* 创建查询条件 */
        Query<User> query = session.createQuery(hql);
        List<User> users = query.list();
        for (User user: users) {
            System.out.println(user);
        }

        session.close();
    }
}
```

2、paging 分页查询

HQL可以通过Query实现分页查询

用到的方法

1、setFirstResult() 设置分页的开始序号 0开始

2、setMaxResults() 设置分页大小

```java
@Test
public void pagingTest() {
    // 加载hibernate配置，默认是 hibernate.cfg.xml
    Configuration configuration = new Configuration().configure();
    // 创建session工厂
    SessionFactory sessionFactory = configuration.buildSessionFactory();
    // 创建session
    Session session = sessionFactory.openSession();

    /* HQL语句查询 */
    String hql = "from User";
    /* 创建查询条件 */
    Query<User> query = session.createQuery(hql);
    query.setFirstResult(1);
    query.setMaxResults(4);
    List<User> users = query.list();
    for (User user: users) {
        System.out.println(user);
    }

    session.close();
}
```

3、where 条件查询 

HQL直接追加where关键字作为查询条件，与SQl语句没有区别。

```java
@Test
public void pagingTest() {
    // 加载hibernate配置，默认是 hibernate.cfg.xml
    Configuration configuration = new Configuration().configure();
    // 创建session工厂
    SessionFactory sessionFactory = configuration.buildSessionFactory();
    // 创建session
    Session session = sessionFactory.openSession();

    /* ------------------- */
    String hql = "from User where id = 6";
    /* 创建查询条件 */
    Query<User> query = session.createQuery(hql);
    User user = query.list().get(0);// 如果没查到会报错
//        User user1 = query.uniqueResult();// 如果没查到，返回null
    System.out.println(user);

    session.close();
}
```

4、like 模糊查询

查询包含某字段的数据。使用like，与SQL无差。

```java
@Test
public void fuzzyTest() {
    // 加载hibernate配置，默认是 hibernate.cfg.xml
    Configuration configuration = new Configuration().configure();
    // 创建session工厂
    SessionFactory sessionFactory = configuration.buildSessionFactory();
    // 创建session
    Session session = sessionFactory.openSession();

    /* -------------------- */
    String hql = "from User where nickname like '%三%'";
    /* 创建查询条件 */
    Query<User> query = session.createQuery(hql);
    List<User> users = query.list();
    for (User user : users) {
        System.out.println(user);
    }

    session.close();
}
```

5、order by 排序

`from User order by id desc`

-   asc 升序
-   desc 降序

6、属性 查询实体类的某一属性

`select username from User`

```java
@Test
public void attrTest() {
    // 加载hibernate配置，默认是 hibernate.cfg.xml
    Configuration configuration = new Configuration().configure();
    // 创建session工厂
    SessionFactory sessionFactory = configuration.buildSessionFactory();
    // 创建session
    Session session = sessionFactory.openSession();

    /* HQL语句查询 */
    String hql = "select nickname from User where id = 4";
    /* 创建查询条件 */
    Query<User> query = session.createQuery(hql);
    // 直接进行强制类型转换，会报错
    String name = String.valueOf(query.getSingleResult());
    System.out.println(name);


    session.close();
}
```

7、`:` 占位符

-   使用占位符 `from User where username = :name`

-   赋值 `query.setString("name", "tom")`

```java
@Test
public void attrTest() {
    // 加载hibernate配置，默认是 hibernate.cfg.xml
    Configuration configuration = new Configuration().configure();
    // 创建session工厂
    SessionFactory sessionFactory = configuration.buildSessionFactory();
    // 创建session
    Session session = sessionFactory.openSession();

    /* HQL语句查询 */
    String hql = "select nickname from User where id = :id";
    /* 创建查询条件 */
    Query<User> query = session.createQuery(hql);
    query.setString("id", "3");
    String name = String.valueOf(query.getSingleResult());
    System.out.println(name);


    session.close();
}
```

8、级联关系查询

查询用户的所有角色

setEntity()

```java
@Test
public void attrTest() {
    // 加载hibernate配置，默认是 hibernate.cfg.xml
    Configuration configuration = new Configuration().configure();
    // 创建session工厂
    SessionFactory sessionFactory = configuration.buildSessionFactory();
    // 创建session
    Session session = sessionFactory.openSession();

    // 查询客户 name为张三
    String hql1 = "from Customer where name = :name";
    Query query1 = session.createQuery(hql1);
    query1.setString("name", "张三");
    // 查询结果
    Customer customer = query1.uniqueResult();
    
    // 查询订单 customer = （上面查询到的）张三
    String hql2 = "from order where customer = :customer";
    Query query2 = session.createQuery(hql2);
    // setEntity 设置实体，使用上面查询的customer
    query2.setEntity("customer", customer);
    // 查询订单结果
    List<Order> orders = query2.list();
    for (Order order: orders) {
        System.out.println(order);
    }

    session.close();
}
```

