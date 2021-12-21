# MyBatis

### 1、简介

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

-   持久层框架 数据持久化（内存 -> 数据库）
-   免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作
-   通过简单的 XML 或注解来配置

### 2、入门

#### 2.1、安装

使用 maven 导入依赖

```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>x.x.x</version>
</dependency>
```

#### 2.2、使用 xml 配置获取 sqlSession

##### 1、xml 

mybatis-config.xml：MyBatis 的核心配置，可以起其他名字。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">

<!-- MyBatis configuration 核心配置文件 -->
<configuration>
    <!-- 默认环境 -->
    <environments default="development">
        <!-- 创建环境，可以创建多个环境 -->
        <environment id="development">
            <!-- 事务管理 使用 JDBC -->
            <transactionManager type="JDBC"/>
            <!-- 默认使用连接池 -->
            <dataSource type="POOLED">
                <!-- 设置 驱动 url user 和 password -->
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>

    <!-- 很重要，找到 Mapper 的位置 -->
    <mappers>
        <!-- 每个Mapper.xml 都需要在这里注册 -->
        <mapper resource="org/demo/DAO/UserMapper.xml"/>
    </mappers>
</configuration>
```

##### 2、Utils 类

获取sqlSession 的类，使用类静态方法直接创建 sqlSession

```java
public class MyBatisUtil {
    /** 提升作用域，将局部提升至全局 */
    private static SqlSessionFactory sqlSessionFactory;

    // 静态块，给类初始化，优先于 main 执行，且只执行一次，都是局部变量
    static {
        try {
            // Mybatis 配置位置 读取它
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);

            // 使用 FactoryBuilder 建设一个工厂（工厂模式）
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 使用工厂创建一个 SqlSession 对象
     * @return 返回 SqlSession 对象
     */
    public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession();
    }
}
```

##### 3、实体类 DAO 和 Mapper.xml

DAO接口中有许多需要实现的方法，Mapper是映射文件，将DAO里的接口方法映射成sql语句。

UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- 命名空间 建议使用全限定名 -->
<mapper namespace="org.demo.DAO.UserDAO">
    <!-- id 就是要实现的方法。resultType 是返回类型，如果是泛型，使用里面的（注意全限定名） -->
    <select id="getUserList" resultType="org.demo.bean.User">
        select * from mybatis.user
    </select>

    <select id="getUserById" resultType="org.demo.bean.User" parameterType="int">
        select * from mybatis.user where id = #{id}
    </select>

    <insert id="addUser" parameterType="org.demo.bean.User">
        insert into mybatis.user (id, userName, pwd) value (#{id}, #{userName}, #{pwd})
    </insert>

    <update id="update" parameterType="org.demo.bean.User">
        update mybatis.user set userName = #{userName}, pwd = #{pwd} where id = #{id}
    </update>

    <delete id="deleteUserById" parameterType="int">
        delete from mybatis.user where id = #{id}
    </delete>
</mapper>
```

##### 4、测试

万事具备，测试一下是否可行。编写一个测试类，在里面写上一下代码。

```java
    @Test
    public void test() {

        // 使用工具类获取连接数据库的session，session有执行sql操作的所有方法。
        try(SqlSession sqlSession = MyBatisUtil.getSqlSession()) {
            // 方法一 推荐 更安全，直观
            // 获取实体Mapper接口，执行里面的方法
            UserDAO userDAO = sqlSession.getMapper(UserDAO.class);
            List<User> users = userDAO.getUserList();

            // 方法二
//        List<User> users = sqlSession.selectList("org.demo.DAO.UserDAO.getUserList");

            for (User user : users) {
                System.out.println(user.toString());
            }
            sqlSession.close();
        }
    }
```

### 3、配置

#### 3.1、核心配置文件

```xml
<configuration>
	
</configuration>
```

有以下属性：

1.  properties（属性）
2.  settings（设置）
3.  typeAliases（类型别名）
4.  typeHandlers（类型处理器）
5.  objectFactory（对象工厂）
6.  plugins（插件）
7.  environments（环境配置）
8.  environment（环境变量）
9.  transactionManager（事务管理器）
10.  dataSource（数据源）
11.  databaseIdProvider（数据库厂商标识）
12.  mappers（映射器）

#### 3.2、环境 environments

一个环境配置里可以有多个环境变量，在环境配置里指定默认的环境。

```xml
    <!-- 默认环境 -->
    <environments default="development">
        <!-- 创建环境，可以创建多个环境 -->
        <environment id="development">
            <!-- 事务管理 使用 JDBC，还有 MANAGED 基本没什么用 -->
            <transactionManager type="JDBC"/>
            <!-- 默认使用连接池 还有 UNPOOLED JNDI。连接数据库 dbcp c3p0 druid-->
            <dataSource type="POOLED">
                <!-- 设置 驱动 url user 和 password -->
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
```

#### 3.3、属性

##### 1、properties

配置数据库连接属性，从外部文件引入

```xml
<!-- 使用 properties 文件，读取其中的参数 -->
<properties resource="database.properties">
  	<!-- 优先使用 properties 文件，如果文件里没有，再使用这里的 -->
    <property name="username" value="dev_user"/>
  	<property name="password" value="F2Fa3!33TYyg"/>
</properties>
```

外部文件格式

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://47.98.103.25:3306/mybatis?useSSL=false&useUnicode=true&characterEncoding=UTF-8
username=root
password=123456
```

##### 2、settings

settings 非常重要，它定义了程序运行时的行为。

具体属性和含义参考官方网站。

>   https://mybatis.org/mybatis-3/zh/configuration.html#settings

完整的settings应该是这样的

```xml
<settings>
  <setting name="cacheEnabled" value="true"/>
  <setting name="lazyLoadingEnabled" value="true"/>
  <setting name="multipleResultSetsEnabled" value="true"/>
  <setting name="useColumnLabel" value="true"/>
  <setting name="useGeneratedKeys" value="false"/>
  <setting name="autoMappingBehavior" value="PARTIAL"/>
  <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
  <setting name="defaultExecutorType" value="SIMPLE"/>
  <setting name="defaultStatementTimeout" value="25"/>
  <setting name="defaultFetchSize" value="100"/>
  <setting name="safeRowBoundsEnabled" value="false"/>
  <setting name="mapUnderscoreToCamelCase" value="false"/>
  <setting name="localCacheScope" value="SESSION"/>
  <setting name="jdbcTypeForNull" value="OTHER"/>
  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings>
```

##### 3、typeAliases 别名

就是类型名的缩写，减少配置的冗余。仅用于xml配置。

```xml
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>
</typeAliases>
```

除了类名缩写，还有报包名。这样就可以为包下面的类编写别名。如果没有使用注解，就使用类名的首字母小写作为别名，如果使用了注解，则使用注解里的。

```xml
<typeAliases>
  <package name="domain.blog"/>
</typeAliases>
```

```java
    // 使用 author 作为别名
	@Alias("author")
    public class Author {
        ...
    }
```

>   java内置类型已经做好了别名，大小写不敏感。参考官方文档。

##### 4、typeHandlers

从数据库取出记录或值，都会用类型处理器以合适的方式转换为java类型。

MyBatis 自带一些 typeHandlers，将对应的数据库类型转换为java类型。

你可以重写已有的类型处理器或创建你自己的类型处理器来处理不支持的或非标准的类型。 具体做法为：实现 `org.apache.ibatis.type.TypeHandler` 接口， 或继承一个很便利的类 `org.apache.ibatis.type.BaseTypeHandler`， 并且可以（可选地）将它映射到一个 JDBC 类型。比如：

```java
// ExampleTypeHandler.java
@MappedJdbcTypes(JdbcType.VARCHAR)
public class ExampleTypeHandler extends BaseTypeHandler<String> {

  @Override
  public void setNonNullParameter(PreparedStatement ps, int i, String parameter, JdbcType jdbcType) throws SQLException {
    ps.setString(i, parameter);
  }

  @Override
  public String getNullableResult(ResultSet rs, String columnName) throws SQLException {
    return rs.getString(columnName);
  }

  @Override
  public String getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
    return rs.getString(columnIndex);
  }

  @Override
  public String getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
    return cs.getString(columnIndex);
  }
}
```

将其映射到 MyBatis 配置中。

```xml
<!-- mybatis-config.xml -->
<typeHandlers>
  <typeHandler handler="org.mybatis.example.ExampleTypeHandler"/>
</typeHandlers>
```

##### 5、处理枚举类型



##### 6、对象工厂

每次 MyBatis 创建结果对象的新实例时，它都会使用一个对象工厂（ObjectFactory）实例来完成实例化工作。 默认的对象工厂需要做的仅仅是实例化目标类。如果想覆盖对象工厂的默认行为，可以通过创建自己的对象工厂来实现。

```java
// ExampleObjectFactory.java
public class ExampleObjectFactory extends DefaultObjectFactory {
  public Object create(Class type) {
    return super.create(type);
  }
  public Object create(Class type, List<Class> constructorArgTypes, List<Object> constructorArgs) {
    return super.create(type, constructorArgTypes, constructorArgs);
  }
  public void setProperties(Properties properties) {
    super.setProperties(properties);
  }
  public <T> boolean isCollection(Class<T> type) {
    return Collection.class.isAssignableFrom(type);
  }}
```

```xml
<!-- mybatis-config.xml -->
<objectFactory type="org.mybatis.example.ExampleObjectFactory">
  <property name="someProperty" value="100"/>
</objectFactory>
```

##### 7、插件

##### 8、数据库厂商标识

##### 9、映射器（mappers）

加载 mapper 的映射文件。

```xml
<!-- 一共有四种方式引入映射文件 -->
<mappers>
   <!-- 使用映射文件相对路径 -->
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
    <!-- 使用映射文件绝对路径 -->
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
    <!-- 使用接口加载映射文件，映射文件名必须与接口文件名相同 -->
  <mapper class="org.mybatis.builder.BlogMapper"/>
    <!-- 将包内的 映射器接口实现 全部注册为映射器 -->
  <package name="org.mybatis.builder"/>
</mappers>

```

### 4、XML 映射文件

#### 4.1、XML 映射器

让用户专注于 sql 代码。xml 映射器有一下几个标签，除了常见的 CURD （insert, update, delete, select），还有 sql, resultMap, cache, cache-ref。

##### 1、CURD

-   select

```xml
<!-- 参数为 int 可以不写 -->
<select <!--  -->
  id="selectPerson" <!-- 映射方法名 -->
  parameterType="int" <!-- 方法参数类型 -->
  parameterMap="deprecated" <!--  -->
  resultType="hashmap" <!-- 返回类型 -->
  resultMap="personResultMap" <!-- 返回 map -->
  flushCache="false"
  useCache="true"
  timeout="10"
  fetchSize="256"
  statementType="PREPARED"
  resultSetType="FORWARD_ONLY">
<!-- insert update delete 返回类型都是int 可以不写（也没有这个属性） -->
```

在xml映射文件中使用方法参数

```xml
<select id="selectPerson" parameterType="int" resultType="hashmap">
    <!-- #{} 代表参数，名字必须和 方法里的参数相同 或 实体类的属性相同 -->
  SELECT * FROM PERSON WHERE ID = #{id}
</select>
```

其余插入更新删除也是类似。

```xml
    <select id="getUserById" resultType="org.demo.bean.User" parameterType="int">
        select * from mybatis.user where id = #{id}
    </select>

    <insert id="addUser" parameterType="org.demo.bean.User">
        insert into mybatis.user (id, userName, pwd) value (#{id}, #{userName}, #{pwd})
    </insert>

    <update id="update" parameterType="org.demo.bean.User">
        update mybatis.user set userName = #{userName}, pwd = #{pwd} where id = #{id}
    </update>

    <delete id="deleteUserById" parameterType="int">
        delete from mybatis.user where id = #{id}
    </delete>
```

插入时一般返回更新数据的id，而这个id一般是自增的。现在有两种方法返回id，一是selectKey标签，二是useGenerateKey属性。

第一种 selectKey。如果id的类型为varchar，则返回值永远是0。

```xml
    <insert id="addUser" parameterType="org.demo.bean.User">
        insert into mybatis.user (id, userName, pwd) value (#{id}, #{userName}, #{pwd})
        <!--
            keyProperty：返回对应bean对象的哪个属性
            resultType：返回值的类型
            order：定义执行顺序，是在执行sql之前还是之后
        -->
        <selectKey keyProperty="id" resultType="int" order="AFTER">
        	SELECT LAST_INSERT_ID();
        </selectKey>
    </insert>
```

第二种 使用 useGenerateKey属性。

```xml
    <!-- 
        useGeneratedKeys：开启自增
        keyProperty：自增返回数据名称
    -->
    <insert id="addUser" parameterType="org.demo.bean.User" useGeneratedKeys="true" keyProperty="id">
        
        insert into mybatis.user (id, userName, pwd) value (#{id}, #{userName}, #{pwd})
    </insert>
```



##### 2、sql语句

对于常用的sql语句，可以把他们定义为一个语句块，使用时可以用 id 名代替整个语句

```xml
<!-- sql 语句块 -->
<sql id="userColumns"> ${alias}.id,${alias}.username,${alias}.password </sql>
<!-- 使用 sql 语句块 -->
<select id="selectUsers" resultType="map">
  select
    <!-- include 引用语句块，property 赋值 -->
    <include refid="userColumns"><property name="alias" value="t1"/></include>,
    <include refid="userColumns"><property name="alias" value="t2"/></include>
  from some_table t1
    cross join some_table t2
</select>
```

##### 3、参数

除了设置参数，还可以映射

```tex
<!-- 设置参数时设置其他属性，可以指定转换 java 类型和 数据库类型，保留小数 -->
<!-- 可以自己编写类型转换程序，例如上文提到的 tpyeHandler，然后在这里指定 -->
#{height,javaType=double,jdbcType=NUMERIC,numericScale=2,typeHandler=MyTypeHandler}
```

##### 4、resultMap

多表查询的时候，返回值不能用单一类表示，这个时候使用 map 解决。返回的map的key是实体类的属性名，值是查询的结果。

上面使用的 resultType=“map”，其实就是隐式的map，下面是显示的使用 map。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="com.how2java.pojo">
    
    <!-- 定义 Map，返回的数据结构 -->
    <resultMap id="userResultMap" type="User">
        <id property="id" column="user_id" />
        <result property="username" column="user_name"/>
        <result property="password" column="hashed_password"/>
    </resultMap>
     
    <select id="selectUsers" resultMap="userResultMap">
        select user_id, user_name, hashed_password
        from some_table
        where id = #{id}
    </select> 
    </mapper>
```

##### 5、关联和集合

使用多表联合查询的时候，使用map非常方便，但是如果表的数量过多，也会变得非常麻烦。

```xml
<!-- 非常复杂的结果映射 -->
<resultMap id="detailedBlogResultMap" type="Blog">
    <!-- constructor 用于实例化类时，注入结果到构造方法中 -->
    <constructor>
        <!-- idArg ID 参数；标记出作为 ID 的结果可以帮助提高整体性能 -->
        <idArg column="blog_id" javaType="int"/>
        <!-- arg 将被注入到构造方法的一个普通结果 -->
    </constructor>
    <!-- result 注入到字段或javaBean 属性的普通结果， 前后分别是javaBean属性名和数据库列名 -->
    <result property="title" column="blog_title"/>
    
    <!-- 一个复杂类型的关联；许多结果将包装成这种类型 -->
    <!-- 嵌套结果映射 – 关联可以是 resultMap 元素，或是对其它结果映射的引用 -->
    <association property="author" javaType="Author">
        <!-- id: ID 参数；标记出作为 ID 的结果可以帮助提高整体性能 -->
        <id property="id" column="author_id"/>
        <result property="username" column="author_username"/>
        <result property="password" column="author_password"/>
        <result property="email" column="author_email"/>
        <result property="bio" column="author_bio"/>
        <result property="favouriteSection" column="author_favourite_section"/>
    </association>
    
    <!-- 一个复杂类型的集合 -->
    <collection property="posts" ofType="Post">
        <id property="id" column="post_id"/>
        <result property="subject" column="post_subject"/>
        <association property="author" javaType="Author"/>
        <collection property="comments" ofType="Comment">
            <id property="id" column="comment_id"/>
        </collection>
        <collection property="tags" ofType="Tag" >
            <id property="id" column="tag_id"/>
        </collection>
        <!-- 使用结果值来决定使用哪个 resultMap -->
        <discriminator javaType="int" column="draft">
            <!-- 基于某些值的结果映射 -->
            <!--嵌套结果映射 – case 也是一个结果映射，因此具有相同的结构和元素；或者引用其它的结果映射 -->
            <case value="1" resultType="DraftPost"/>
        </discriminator>
    </collection>
</resultMap>
```

属性

1、id：当前命名空间中的一个唯一标识，用于标识一个结果映射。

2、type：类的完全限定名, 或者一个类型别名

3、如果设置这个属性，MyBatis 将会为本结果映射开启或者关闭自动映射。 这个属性会覆盖全局的属性 autoMappingBehavior。默认值：未设置（unset）

4、property：映射列结果的字段或属性。

5、column：数据库列名或别名。

6、javaType：java类型的全限定名或别名。

7、jdbcType：JDBC 类型。只需要对表的内容修改的时候使用。

8、typeHandler：默认的类型处理器。



#### 4.2 预防 SQL 注入攻击

SQL 注入攻击是指黑客利用 SQL语句拼接漏洞，获取所有数据的操作。

例如 select * from user where name = ${}，正常使用是没有问题的，但是如果输入 `‘’ or 1=1` 这个时候 SQL语句就变成了<code>select * from user where name = ‘’ or 1=1</code>，这样就获取了全部的数据。

MyBatis 自带预防 SQl 注入攻击，只需要在传变量的时候使用 #{}，而不是${}。

##### 4.2.1、传值方式

有两种传值方式，一是${}，叫做原文传值，另一种是 #{}，叫预编译。

原文传值会将传入的字符串直接拼接到sql语句中，与编译会将传入的值进行预编译。例如上面的 sql语句就会变成<code>select * from user where name = “‘’ or 1=1”</code>

##### 4.2.2、使用场景

什么时候使用原文传值呢？在需要排序的时候，将排序的字符串直接拼接到sql语句中。

谨慎使用，防止SQL注入攻击。不要让用户写sql语句。

### 5、动态sql

给sql语句加上控制和判断语句，让sql语句在执行时有动态的加载功能。具体就是使用xml标签实现控制。

##### 1、if

判断语句 只有 if，没有 else，如果需要使用 else 功能，可以对上面的 if 判断条件进行取反。

```xml
<select id="listProduct" resultType="Product">
    select * from product_
    <!-- if 标签，判断的是传入的参数 -->
    <if test="name!=null">
        where name like concat('%',#{name},'%')
    </if>		 	
</select>
```

##### 2、where

where 标签相当于 where 在sql中的意思。当 where 标签里的条件都不成立时，就不会有 where 出现在sql语句中，避免出现 sql 语句出错。另外，它还会自动去掉后面条件语句中的 and 或者 or，所以你在写sql语句时，不必考虑这个语句出现在第一个判断条件还是后面。

```xml
<select id="listProduct" resultType="Product">
    select * from product_
    <!-- 如果条件都不成立，where 会被自动去掉，直接查询所有 -->
    <where>
        <!-- 如果条件成立，and 会被自动去掉 -->
        <if test="name!=null">
            and name like concat('%',#{name},'%')
        </if>		 	
        <if test="price!=null and price!=0">
            and price > #{price}
        </if>	
    </where>	 	
</select>
```

##### 3、set

update时使用的标签，和 where 类似。

```xml
<set>
    <if test="name != null">name=#{name},</if>
    <if test="price != null">price=#{price}</if>
</set>
```

##### 4、trim

trim 的意思是修建，可以使用这个标签自定义功能。例如用它实现 where 和set功能。

```xml
<!-- 它和 where 功能相同 -->
<trim prefix="WHERE" prefixOverrides="AND |OR ">
    ... 
</trim>

<!-- 同理，和 set 相同 -->
<trim prefix="SET" suffixOverrides=",">
    ...
</trim>
```

##### 5、choose

xml没有 else 标签，不过有 when-otherwise 可以实现差不多的功能，不过需要在外面套一层 choose 标签。

实际上它不是 if-else，而是 switch-case。实际运行中，它会逐个检测符合条件的 when 中的 sql语句，一旦有满足的when标签，就不再判读后面的。otherwise相当于 default。

```xml
<select id="listProduct" resultType="Product">
    SELECT * FROM product_ 
    <where>
        <choose>
            <when test="name != null">
            and name like concat('%',#{name},'%')
            </when>
            
            <when test="price !=null and price != 0">
            and price > #{price}
            </when>			  		
            <otherwise>
            and id >1
            </otherwise>
        </choose>
    </where>
</select>
```

##### 6、foreach

foreach 在批量处理数据的时候常常用到，例如批量插入

```xml
<select id="listProduct" resultType="Product">
    SELECT * FROM product_
    WHERE ID in
    <foreach item="item" index="index" collection="list"
        open="(" separator="," close=")">
        #{item}
    </foreach>
</select>
```

// TODO

##### 7、bind

bind 相当于拼接字符串的功能。常用于模糊查询。

```xml
<select id="listProduct" resultType="Product">
    <bind name="likename" value="'%' + name + '%'" />
    select * from   product_  where name like #{likename}
</select>
```

### 6、注解

注解可以直接在接口文件中写sql代码，这样做的好处是简化配置，传参和返回值一目了然，缺点是不适合写复杂的代码。只需要在 mybatis-config.xml 中注册映射即可。

```xml
<mappers>
    <mapper resource="com/how2java/pojo/Category.xml"/>
    <!-- 使用接口注册映射，上面的可以不用了 -->
    <mapper class="com.how2java.mapper.CategoryMapper"/> 
    <!-- 还可以使用 package进一步简化配置，包内所有接口都会被注册映射 -->
    <package name="com.how2java.mapper" />
</mappers>
```

mapper

```java
public interface CategoryMapper {
  
    // sql语句不变，行参前面加上对应的注解，匹配对应的变量
    @Select("select * from category where category_price between #{min} and #{max} limit 0, #{limt}")
    List<category> getCategory(@Param("min") float min, @Param("max") float max, @Param("limt") int limt);
    
    @Insert("insert into category values (#{item.id}, #{item.name})")
    @SelectKey(statement = "SELECT LAST_INSERT_ID()", keyProperty = "id", before = false, resultType = Integer.class)
    int insert(@Param("item") Category category);
    
    @Select("")
    /** 结果映射 */
    @Results({
            @Result()
    })
    List<Product> getProducts();
}
```

### 7、日志

日志就是程序运行和状态的记录。

#### 7.1、日志结构关系

##### 7.1.1、日志门面：规定日志的格式。(接口)

1、Simple Logging Facade For Java (SLF4J)

2、Apache Commons-Logging

这两种都常用，但接口不同。

##### 7.1.2、日志实现：具体实现方式，各不相同

1、log4j

2、logback

3、java.util.logging（jul）

#### 7.2、实现日志功能

使用 logback 实现。

1、添加maven依赖

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

然后运行程序就可以在控制台看到日志输出。

2、配置日志

添加 logback.xml 文件，在这个文件中配置实现日志的方式。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>
    <!-- appender: 追加器，在哪里输出日志 name: 起个名字 class: 类名 -->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <!-- 编码 -->
        <encoder>
            <!-- pattern: 日志输出格式 使用%引用变量 -->
            <pattern>
                [%thread] %d{HH:mm:ss.SSS} %-5level %logger{36} - %msg%n
            </pattern>
        </encoder>
    </appender>
    <!-- 日志输出的最低级别 -->
    <root level="debug">
        <appender-ref ref="console"></appender-ref>
    </root>
</configuration>
```

在 pattern 里定义日志输出的格式。

%thread ：哪个线程输出的

%d{HH:mm:ss.SSS}：输出时间

%-5level：日志级别，

%logger{36}：使用哪个类进行日志管理，最长36个字符，超过用简写

%msg：日志信息

%n：换行

3、 日志级别（从高到低）

error：错误，系统故障日志

warn：警告：存在风险，或使用不当

info：一般性消息

debug：内部调试

trace：跟踪信息（很少用，一般调试的临时打印）

### 8、缓存

##### 8.1、简介

MyBatis 缓存（缓冲，存储），优化运行效率。对获取的重复的记录存储在内存中。

Mybatis 有两级缓存，一级缓存是默认开启的，缓存的范围是SqlSession会话。二级缓存需要手动开启，范围是 MapperNamespace。二级缓存范围大于一级缓存，一级缓存存储时间短，利用率不高。

二级缓存默认会为所有查询操作开启缓存，在使用commit后强制清空，这样做是为防止出现数据不一致。

如果不使用缓存，可以在select标签属性中写上 <code>useCache=“false”</code>，如果返回结果很多，不建议加入缓存。

如果要强制清空缓存，可以使用 flushCache=true

##### 8.2、开启缓存

如何开启二级缓存？在mybatis-config.xml中添加配置

```xml
<mapper>
    <!--
        eviction：缓存策略
        flushInterval：清空缓存间隔时间（毫秒）
        size：最多缓存数量
        readOnly:只读
    -->
    <cache eviction="LRU" flushInterval="600000" size="512" readOnly="true"></cache>
</mapper>
```

##### 8.3、属性含义

###### 1、eviction

缓存的清除策略，一共有四种。

1.  LRU：最长时间未使用
2.  LFU：访问次数最少的
3.  FIFO：先进先出
4.  SOFT：软引用
5.  WEAK：弱引用

##### 2、flushInterval



##### 3、size



##### 4、readOnly

设置是否为只读。

如果为 true，从内存中去除对象本身，执行效率较高。

如果为 false，则创建缓存副本，取出的对象不同，安全性较高。

### 9、关联查询(association)



### 10、C3P0 连接池

在实际项目的开发中，一般不会使用MyBatis默认的连接池，这个时候我们就需要对MyBatis连接池进行扩展。

1、添加依赖

```xml
<denpendency>
    <groupId>com.mchange</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.5.4</version>
</denpendency>
```

2、创建一个新的包和类

org/demo/dataSource/C3p0DataSourceFactory.java

```java
public class C3p0DataSourceFactory extends UnpooledDataSourceFactory {
    public C3p0DataSourceFactory() {
        this.dataSource = new ComboPooledDataSource();
    }
}
```

3、修改MyBatis配置文件

```xml
<!-- type指向刚才创建的类 -->
<dataSource type="org.demo.dataSource.C3p0DataSourceFactory">
    <!-- 变量名已经变了 -->
    <property name="driverClass" value="${driver}"/>
    <property name="jdbcUrl" value="${url}"/>
    <property name="user" value="${username}"/>
    <property name="password" value="${password}"/>
    <!-- 还可以设置其他属性 -->
    <!-- 初始化连接数量和最大、最小连接数量 -->
    <property name="initialPoolSize" value="5"/>
    <property name="maxPoolSize" value="20"/>
    <property name="minPoolSize" value="5"/>
</dataSource>
```

### 11、批处理（collection）

演示批量插入的sql语句，使用 forEach

```xml
<insert id="" parameterType="java.util.List">
    INSERT INTO () VALUES
    <!-- 
        item：列表中的每一个对象
        index：索引
        collection：数据源，list，不能改变
    -->
    <foreach item="item" index="index" collection="list" separator=",">
        #{item.title}, #{item.name}...
    </foreach>
</insert>
```

>   批量插入可能会因为sql语句太长而无法传输，或者无法执行，在编写程序时要进行压力测试。

批量删除

```xml
<delete>
    DELETE FROM table WHERE id in
    <foreach item="item" index="index" collection="list" separator="," open="(" close=")">
        #{item}
    </foreach>
</delete>
```

