# JAP & Spring Data

## JPA

### JPA概述

我们使用JDBC的例子说明。最早的时候，出现了很多数据库，MYSQL，Oracle，SQLServer等，操作这些数据库有不同的SQL语句，差别很大，java程序调用数据库的时候很难做到统一，于是JDBC出现了，它是一组连接数据库的规范，只包含接口，没有具体实现，具体实现是由各家数据库厂商实现的，就是连接数据库的驱动，放到网上供大家使用，java程序调用JDBC就可以了。

JPA（Java Persistence API）是用于对象持久化的API。Java EE5.0平台标准的ORM规范，是的应用程序以统一的方式访问持久层。JPA具体实现有Hibernate，TopLink等。

Hibernate除了是ORM框架，也是JPA的一种实现。

### 第一个JPA程序

1、创建程序，导入依赖

>   pom.xml

```xml
<!-- 单元测试 -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
<!-- hibernate -->
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-core</artifactId>
    <version>5.4.12.Final</version>
</dependency>
<!-- mysql -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.34</version>
</dependency>
<!-- 日志 -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

2、创建persistence.xml 环境基本信息



3、创建实体类，使用注解

4、测试 CURD

## Spring Data JPA

### 1、CRUD

#### 1、查询

```java
userDao.findAll();
userDao.findById(Integer);
```



#### 2、增改

```java
userDao.save(User)
```

>   新增或更新
>
>   如果数据库中id存在，则数据更新
>
>   如果数据库中没有或者实体类对象中没有设置id则新增。

#### 3、删除

```java
userDao.delete(Integer);
UserDao.delete(User);
```

可以传入id或者一个对象

### 2、高级查询

#### 1、分页查询

```java
Page<Banner> page = bannerRepository.findAll(PageRequest.of(0, 5));
```

使用findAll方法，传入的参数是一个PageRequest对象，`PageRequest.of(0, 5)` 两个参数分别代表查询的页码和每页大小。

返回对象是一个Page对象，里面包含分页的所有信息。

```java
page.getTotalElements();        // 记录总数
page.getContent();              // 当前页对象列表
page.getNumber();               // 当前页码
page.getNumberOfElements();     // 当前页有多少记录
page.getSize();                  // 每页大小
page.getTotalPages();           // 总页数
```

>   1、使用 new的方式创建PageRequest对象已经过时，
>
>   2、查询分页的起始页码为 0，
>
>   3、Page继承java.util.List，所以可以直接对page进行遍历。page.forEach()

#### 2、分页且排序

```java
Sort sort = Sort.by(Sort.Direction.DESC, "id");
bannerRepository.findAll(PageRequest.of(0, 5, sort));
```

使用Sort对象实现排序，`Sort.by(Sort.Direction.DESC, "id")` 返回一个Sort对象，传入分页查询条件中。参数分别是排序方向和排序字段。

>   使用 new Sort(order)的方式已经过时。

#### 3、条件查询

在持久层根据规则创建条件查询方法，jpa会帮我们自动实现查询方法。

```
List<User> findAllByNameLikeOrIdGreaterThanEquals(String name, Integer id);
```

规则如下：

```
* 1> 以 find...By开头，中间是查询的范围，例如全部，不重复的记录，findAllBy
* 2> 在跟实体类的属性名，和判断条件
*      Like
*      Equals
*      Between
*      LessThan
*      LessThanEquals
*      GreaterThan
*      GreaterThanEquals
* 3> 多个条件之间使用 And Or 连接
* 4> 方法参数的个数和类型和顺序与属性对应
* 5> 方法名遵守驼峰命名法
```

>   使用like查询的时候，传入的参数中 % 代表0 - n的字符。

#### 4、Query注解

使用上面的条件查询方法名太长且易错。

使用注解可以随意定义方法的名字，注解中使用Jpql书写查询条件。

```java
@Query("select b from Banner b where b.bannerName like ?1 or b.bannerName like ?2")
List<Banner> findBannerNameLike(String name1, String name2);
```

jpql类似sql语句，不一样的是它查询时不能写 `select *`， 而且查询的不是表名，而是实体类名，查询的字段名要写成对应实体类的属性名。

#### 5、执行原生sql语句

```java
@Query(nativeQuery = true, value = "select count(*) from banner")
Integer findCount();
```

使用原生的sql语句查询，注解中写的是sql语句，不是jpql，所以要写表名和字段名。

启用原生sql语句使用`nativeQuery = true`，它默认是false

