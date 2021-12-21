### JSP

#### 介绍

Java Server Pages，动态网页技术，可以在网页中插入java代码。

执行过程中，网页中的 jsp 代码首先会被翻译成 java代码，然后编译成class文件，最后由服务器执行，返回给客户端。

#### 格式

网页中的jsp代码用 <%、%> 包裹。

```jsp
<% out.println("hello"); %>
```

有等号的变量会被输出到网页，相当于 response.getWriter() 在执行。

```jsp
<%=new Date().toString();%>

<%-- 这是jsp注释 --%>
<%-- 会输出当前的日期 --%>
```

#### 指令和动作

```jsp
<%-- 这是指令的格式。 --%>
<%@page %>

<%-- 这是动作的格式。 --%>
<jsp:include />
```

#### include

在一个网页中包含另一个网页。

1. 指令包含

```jsp
<%@include file="footer.jsp" %>
```

2. 动作包含

```jsp
<jsp:include page="footer.jsp" />
```

> 两者的区别：
>
> 指令包含最终只会编译一个文件，相当于直接写在另一个网页中，并且参数共用。
>
> 动作包含会生成编译两个文件，且参数不共用，需要传参。

#### 传参

例：

```jsp
<%-- 传参给footer.jsp --%>
<jsp:include page="footer.jsp">
    <jsp:param  name="year" value="2017" />
</jsp:include>

<%-- 在 footer.jsp 中获取参数 --%>
<%= reqesut.getParameter("year"); %>
```

#### 跳转

1. 客户端跳转

```jsp
<%-- 与java代码相同 --%>
<%
    response.sendRedirect("hello.jsp");
%>
```

2. 服务端跳转

    1. 第一种

    ```jsp
    <%-- 与java代码相同 --%>
    <%
    	request.getRequestDispatcher("hello.jsp").forward(request, response);
    %>
    ```

    2. 第二种

    ```jsp
    <%-- 使用 forward 动作 --%>
    <jsp:forward page="hello.jsp"/>
    ```

#### Cookie

cookie 是由服务器创建，但是暂时保存在本地的用户数据，便于针对用户向服务器发送请求。

- 创建 cookie

```jsp
<%-- 1. 创建一个 cookie 对象 --%>
<%
	Cookie c = new Cookie("name", "Gareen");
%>

<%-- 2. 设置生命周期，下面表示保存一天 --%>
<%
	c.setMaxAge(24 * 60 * 60);
%>

<%-- 3. 设置访问路径，只有访问此路径才会提交 cookie --%>
<%
	c.setPath("/a");
%>

<%-- 4. 发送给客户端 --%>
<%
	response.addCookie(c);
%>
```

- 获取 cookie

```jsp
<%-- 1. 访问对应路径 --%>
<%
	http:localhost:8080/a
%>

<%-- 2. 获取所有 cookie --%>
<%
	Cookie[] cookies  = request.getCookies();
%>

<%-- 3. 对 cookie 进行操作 --%>
<%
    if (null != cookies ) {
        for (int d = 0; d < cookies.length; d++) {
        	out.print(cookies[d].getName() + ":" + cookies[d].getValue() + "<br>");
        }
    }
%>
```

#### Session

session ：一个用户进入网站登录，他在浏览器中的所有连接都对应在该账户下，无论打开多少连接，都属于同一个session。

1. 设置session

```jsp
<%
   session.setAttribute("name", "teemo");
%>
 
<a href="getSession.jsp">跳转到获取session的页面</a>
```

2. 获取session

```jsp
<%
    String name = (String)session.getAttribute("name");
%>
 
session中的name: <%=name%>
```

> session 使用过程：当浏览器访问网页时，会将 cookie 中的信息发送给服务端。如果是第一次访问，服务端会新建一个会话，并将此信息发送给客户端。如果浏览器禁用了 cookie，可以将参数写到url中，服务端就会根据参数识别是哪个用户。
>
> session 状态保存时间一般是 30 分钟，也就是说，30 分钟内，重启浏览器再打开网页，还是登录状态。这个时间可以通过修改toncat配置进行修改。

- 使用 url

```jsp
response.encodeURL("getSession.jsp")
```

#### 作用域

jsp 有 4 种作用域。分别是 当前页面、一次请求、当前会话、全局（所有用户共享）

pageContext、request、session、application

1. 当前页面

```jsp
<%-- 设置值 --%>
<% pageContext.setAttribute(key, value); %>

<%-- 取值 --%>
<% pageContext.getAttribute(key); %>
```

2. 一次请求

接下来的都与上面类似

```jsp
<%-- 设置值 --%>
<% request.setAttribute(key, value); %>

<%-- 取值 --%>
<% request.getAttribute(key); %>
```

> 如果请求发生了服务端跳转，这算一次请求，还是能访问变量。如果发生了服务端跳转，就不是一次请求，如果需要使用参数，就要使用传参。

3. 当前会话

```jsp
<%-- 设置值 --%>
<% session.setAttribute(key, value); %>

<%-- 取值 --%>
<% session.getAttribute(key); %>
```

4. 全局

```jsp
<%-- 设置值 --%>
<% application.setAttribute(key, value); %>

<%-- 取值 --%>
<% application.getAttribute(key); %>
```

#### 隐式对象

jsp 一共有 9 种隐式对象，通过上面的学习，我们已经掌握了大部分。他们分别是：

1. request response out

这三种分别是负责请求、响应和输出的。

2. pageContext session appliction

这三种负责不同作用域下变量的设置和获取。

3. page

与 pageContext 不同，它是代表 jsp 对象自身，相当于 this。

4. config

获取 web.xml 中的参数，具体使用有些复杂。

```jsp
<%
	config.getInitParameter("database-ip");
%>
```

web.xml 文件

```xml
<servlet>
    <!-- 把 testconfig.jsp配置为一个 servlet -->
    <servlet-name>testconfig</servlet-name>
    <jsp-file>/testconfig.jsp</jsp-file>
    <!-- 配置初始化参数 -->
    <init-param>
        <param-name>database-ip</param-name>
        <param-value>127.0.0.1</param-value>
    </init-param>
</servlet>
<!-- 将路径 testconfig映射到testconfig.jsp -->
<servlet-mapping>
    <servlet-name>testconfig</servlet-name>
    <url-pattern>/testconfig</url-pattern>
</servlet-mapping>
```

5. exception

exception 对象只有当前页面的<%@page 指令设置为isErrorPage="true"的时候才可以使用。同时，在其他页面也需要设置 <%@page 指令 errorPage="" 来指定一个专门处理异常的页面。

例如：我们把期望正确执行的代码放在 index.jsp，异常处理代码放在 catch.jsp。需要在 index.jsp 设置异常处理页面，并且在 catch.jsp 中设置isErrorPage="true"。

#### JSTL

##### core

- 导包

JSTL.jar

standard.jar

pom.xml

```xml
<!-- https://mvnrepository.com/artifact/javax.servlet/jstl -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/taglibs/standard -->
<dependency>
    <groupId>taglibs</groupId>
    <artifactId>standard</artifactId>
    <version>1.1.2</version>
</dependency>
```

导入 jsp

```jsp
<%-- prefix="c" : 以 <c: 开头代表JSTL语句 --%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<c:set var="name" value="${'gareen'}" scope="request" />
```

- set out remove

```jsp
<%-- 定义变量，scope 是作用域 --%>
<c:set var="name" value="${'gareen'}" scope="request" />
<c:out value="${name}" />
<c:remove var="name" scope="request" />
```

- if else

```jsp
<%-- 引号里写判断语句 --%>
<c:if test="${hp<5}">
</c:if>

<%-- JSTL 没有else，使用 ！ --%>
<c:if test="${!(hp<5)}">
</c:if>
```

- choose

```jsp
<%-- 实现 else 功能 --%>
<c:choose>
    <c:when test="${hp<5}">
        if 部分
    </c:when>
    <c:otherwise>
        else 部分
    </c:otherwise>
</c:choose>
```

- forEach

```jsp
<%-- items： 要遍历的对象， var ： 遍历中的单个变量， vatStatus ： 遍历状态 --%>
<c:forEach items="${heros}" var="hero" varStatus="st"  >
    <c:out value="${st.count}"></c:>
</c:forEach>
```

- forTokens

```jsp
<%-- 拆分字符 --%>
<c:set var="heros" value="塔姆,艾克;巴德|雷克赛!卡莉丝塔" />
 
<c:forTokens items="${heros}" delims=":;|!" var="hero">
    <c:out value="${hero}" /> <br />
</c:forTokens>
```

##### fmt

格式化数据的库，例如时间、日期、数字。

引入到 jsp 中

```jsp
<%-- 引入 fmt ：以 <fmt: 开头，单标签 --%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt"  prefix='fmt' %>
```

- 格式化数字

```jsp
<%-- 
	formatNumber 格式化数字
	value 要格式化的数字
	minFractionDigits 至少保留的小数点位数
	maxFractionDigits 最多保留的小数点位数
    --%>
<fmt:formatNumber type="number" value="${money}" minFractionDigits="2"/>
```

- 格式化日期

```jsp
<%-- 
	formatDate 格式化日期
	value 要格式化的日期
	pattern 格式
    --%>
<fmt:formatDate value="${now}" pattern="G yyyy年MM月dd日 E"/>
```

需要指定格式，具体字母含义如下

```tex
yyyy 表示年份
MM 表示月份
dd 表示日期
E 表示星期几

a 表示是上午还是下午
HH 表示小时
mm 表示分钟
ss 表示秒
S 表示毫秒
z 表示时区
```

##### fn

用于处理字符串的库，用法类似java标准库的String类。

引入及用法

```jsp
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
${fn:substring(name, 0, 5)}
<%-- 还有很多，具体参考 String 类 --%>
```

#### EL 表达式语言

为了确保能够使用 EL 表达式语言，在文件开头指令添加 isELIgnore=“false”。

1. 取值

```jsp
<%-- 就这么简单，取值并输出 --%>
${name}
```

2. 作用域优先级

pageContext -> request -> session -> application

3. java Bean

java 中一种类的类型，这种类包含 1> 无参构造器 2> 对应属性的 public Getter、Setter 3> boolean 类型属性的 is方法和 Setter 方法

4. 访问 Bean类型 对象的属性

```jsp
<%--  --%>
${hero.name}
```

5. 结合 JSTL 使用



6. 取参

```jsp
<%-- 获取 url 中的参数 --%>
${param.name}
```

7. 判断

> 判断大小，相等与 linux shell脚本语言相同

<table>
    <tbody>
        <tr>
            <th>表达式</th>
            <th>全写</th>
            <th>含义</th>
        </tr>
        <tr>
            <td>eq</td>
            <td>equal</td>
            <td>等于</td>
        </tr>
        <tr>
            <td>ne / neq</td>
            <td>not equal</td>
            <td>不等于</td>
        </tr>
        <tr>
            <td>gt</td>
            <td>greater than</td>
            <td>大于</td>
        </tr>
        <tr>
            <td>ge / gte</td>
            <td>greater than or equal</td>
            <td>大于等于</td>
        </tr>
        <tr>
            <td>lt</td>
            <td>less than</td>
            <td>小于</td>
        </tr>
        <tr>
            <td>le / lte</td>
            <td>less than or equal</td>
            <td>小于等于</td>
        </tr>
        <tr>
            <td>not</td>
            <td>not</td>
            <td>非</td>
        </tr>
        <tr>
            <td>mod</td>
            <td>modulo</td>
            <td>取模</td>
        </tr>
        <tr>
            <td>is [not] div by</td>
            <td></td>
            <td>是否能整除</td>
        </tr>
        <tr>
            <td>is [not] even</td>
            <td>modulo</td>
            <td>是否为偶数</td>
        </tr>
        <tr>
            <td>is [not] odd</td>
            <td></td>
            <td>是否为奇数</td>
        </tr>
    </tbody>
</table>
### Filter

#### Filter 简单介绍

filter 可以对用户请求和响应进行拦截，然后对这些东西进行修改后再发送。Filter 可以有多层，一层过滤之后交给下一层。

Filter 提供了一些接口，编写的 Filter 类必须实现这些接口。具体包括：

1. init() 服务器打开，创建 Filter 实例时执行。只执行一次。

    如果 Filter 启动失败，整个web项目就不会启动。

2. doFilter() 接受到请求或响应时执行。可以被执行多次。

3. destroy() 服务器关闭时执行。

```tex
|--|            |          |         |--|  
|客|   ------> |          | -----> |服| 
|户|             |filter  |         |务| 
|端|   <------ |          | <----- |端| 
|--|            |          |         |--|
```

> https://www.php.cn/java-article-418861.html

#### 配置 web.xml

与 servlet 类似，也需要配置 web.xml，格式与 servlet 类似。

```xml
<filter>
    <filter-name>EncodingFilter</filter-name>
    <filter-class>filter.EncodingFilter</filter-class>
</filter>
 
<filter-mapping>
    <filter-name>EncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

对所有的请求调用 filter.EncodingFilter 类。

#### 使用

一般使用

```java
// 对象类型不一致，需要转换
HttpServletRequest request = (HttpServletRequest) req;
HttpServletResponse response = (HttpServletResponse) res;

request.getRemoteAddr();
// 返回类型转字符串
request.getRequestURL().toString();

// 交给下一个Filter
chain.doFilter(request, response);
```

处理中文

```java
request.setCharacterEncoding("UTF-8");
// 让所有请求都走这个过滤器
```

登录验证

```java
// 通过session获取用户信息，如果没有，则说明没有登录，这时候让用户登录。
// 如果没有登录，返回登录页面后，后面的请求就不用再执行了，直接 return。
HttpServletRequest request = (HttpServletRequest) req;
HttpServletResponse response = (HttpServletResponse) res;

// 是登录请求
String uri = request.getRequestURI();
if (uri.endsWith("login.html") || uri.endsWith("login")) {
    chain.doFilter(request, response);
    return;
}

// 不是登录请求，但没有登录
String userName = (String) request.getSession().getAttribute("userName");
    if (null == userName) {
    response.sendRedirect("login.html");
    return;
}

// 已登录，不是登录请求 -> 下一个过滤器
chain.doFilter(request, response);
```

#### 监听器

监听器的作用是用于监听 web应用的创建和销毁，以及在其上attribute发生的变化。web应用即ServletContext对象(jsp的隐式对象application)。

监听器监听两个方面，一是web应用的生命周期，二是 contextAttribute 的变化。

自己编写的监听类实现web监听器接口，主要是三三部分，分别是 ServletContextListener、HttpSessionListener和ServletRequestListener，分别监听Context(web应用的创建和销毁)、Session和request的生命周期。这三个又有对应的属性监听器，分别是 ServletContextAttributeListener、HttpSessionAttributeListener和ServletRequestAttributeListener，分别监听对应的属性变化。

他们都有各自的抽象方法。

```tex
监听生命周期的是创建和销毁。
ServletContextListener
	contextInitialized()
	contextDestroyed()
HttpSessionListener
	sessionCreated()
	sessionDestroyed()
ServletRequestListener
	requestInitialized()
	requestDestroyed()

监听属性的方法名完全相同，只是参数不同。分别监听属性增加，删除和替换。
attributeAdd()
attributeRemoved()
attributeReplaced()
```

除此之外，还有两种监听器，他们不需要在 web.xml 中配置。HttpSessionBindingListener和HttpSessionActivationListener。

TODO 要实现这两个接口的功能需要修改 tomcat 配置，以后再写。 

##### 配置 web.xml

监听事件源需要对 web.xml 进行配置。

```xml
<listener>
    <listener-class>listener.ContextListener</listener-class>
</listener>
```

#### JSON

使用 json 格式将数据传送。分为客户端和服务端，客户端使用 JSON 类，服务端使用 JSONObject 类。

##### 准备

web.xml

```xml
<servlet>
    <servlet-name>SubmitServlet</servlet-name>
    <servlet-class>SubmitServlet</servlet-class>
</servlet>

<servlet-mapping>
    <servlet-name>SubmitServlet</servlet-name>
    <url-pattern>/submitServlet</url-pattern>
</servlet-mapping>
```

pom.xml

```xml
<!-- https://mvnrepository.com/artifact/org.json/json -->
<dependency>
    <groupId>org.json</groupId>
    <artifactId>json</artifactId>
    <version>20210307</version>
</dependency>
```

##### 转化

- 客户端对象和json互转

```js
// JSON.stringfy() : 将对象(json)转化为字符串
JSON.stringfy(hero);

// JSON.parse(data) : 将字符串转化为json(对象)
JSON.parse(data);
```

- 服务端对象和json互转

```java
// TODO 没找到对应的类
```

#### JDBC

JDBC (Java Database Connectivity) 是用于连接数据库的。要使用这个库，首先要导入包，然后加载 JDBC 驱动，再连接数据库，最后对数据库进行操作。

maven 配置

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.37</version>
    <scope>runtime</scope>
</dependency>
```

代码

```java
// JDBC连接的URL, 不同数据库有不同的格式:
String JDBC_URL = "jdbc:mysql://localhost:3306/test";
String JDBC_USER = "root";
String JDBC_PASSWORD = "password";
// 获取连接:
Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD);
// 访问数据库...
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery("SELECT id, grade, name, gender FROM students WHERE gender=1");
while (rs.next()) {
    long id = rs.getLong(1); // 注意：索引从1开始
    long grade = rs.getLong(2);
    String name = rs.getString(3);
    int gender = rs.getInt(4);
}

// 关闭连接:
conn.close();
```

生下的代码都是对数据库的CURD 操作，根据自己的业务需求编写响应的 sql 查询代码。

实际开发中使用 MBatis 实现数据持久层的功能，即对数据库的CURD 操作。MyBatis 可以节省大量冗余的代码，让你专注于写sql语句，并且可以通过配置管理连接数据库。

#### MVC模式

MVC三个字母分别代表 Module（bean，dao）, View（jsp）, Controller（servlet）。module 就是从数据库的返回结果实体化的实体类对象，view 是前端代码，是展示给用户的视图，controller 是控制不同的数据显示在不同的视图上。

MVC 模式结合了 Servlet 和 JSP 的优点，既方便对数据进行操作，也方便编写前端代码。

