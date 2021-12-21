# thymeleaf

thymeleaf是一个html模板引擎，适合在基于MVC的web应用程序开发提供html。thymeleaf可以在脱机环境下展示html效果，真正实现前后端分离，thymeleaf旨在完全替代jsp。

#### 1、创建thymeleaf项目

新建一个spring boot项目，选择thymeleaf依赖，以及其他的依赖，例如spring web，mysql，lombok等。

```xml
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId>
    <version>3.0.11.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```



然后创建一个thymeleaf模板（file -> settings -> Editor -> file and code templates），然后新建一个模板，extension为html，最重要的是在html标签中添加属性 `xmlns:th="http://www.thymeleaf.org"`，然后就可以在文件中使用thymeleaf指令对页面进行渲染。

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8">
  <title>#[[$Title$]]#</title>
</head>
<body>
#[[$END$]]#
</body>
</html>
```

接着创建一个thymeleaf模板，使用thymeleaf指令接受后端传入的变量。

th:text指令可以使用传入的变量替代标签中的文本。

```xml
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title th:text="${title}">Home</title>
</head>
<body>
    <!-- 如果没有传入参数，页面不会被渲染，会显示标签中的默认值 -->
    <div th:text="${name}">hello</div>
</body>
</html>
```

最后创建一个controller，重定向到这个模板页面，然后传入一些数据。

```java
@Controller
public class TestController {
    @GetMapping("/index")
    public String toIndex(Model model) {
        model.addAttribute("title", "thymeleaf");
        model.addAttribute("name", "tom"); // 使用spring MVC中的对象传递参数
        return "/index"; // 这个是跳转的页面，指根目录下的index.html
    }
}
```

启动spring boot，在浏览器输入响应的地址就可以看到页面渲染后的结果。

#### 3、thymeleaf语法

##### 1、变量的使用

thymeleaf使用变量表达式`${}`引用变量，对于后台传入的对象，它有有三种获取变量的方法。

>   传入的变量是一个user对象，这个对象有username等属性。

1、`th:text="${user.username}"`

2、`th:text="${user['username']"`

3、`th:text="${user.getUsername()}"`

如果user的属性太多，可以使用另一种方式简化代码

```html
<div th:object="${user}">
    <p th:text="*{username}"></p>
    <p th:text="*{height}"></p>
    <p th:text="*{age}"></p>
</div>
```

使用选择表达式 `*{}` 可以访问使用 `th:object` 指令选择的对象，如果没有，他和变量表达式`${}`没什么区别。

##### 2、字符串拼接

这些变量也可以进行字符串拼接，有两种拼接方式。

第一种就是我们熟悉的使用加号拼接。

第二种是使用 `||` 包裹需要拼接的字符串。`th:text="|hello-${user.username}|"`

##### 3、控制语句

1、if 如果if里面的条件为true，这标签就会显示，如果为 false就不会显示

`th:if="${user.age} >= 18"`

2、for循环

```html
<ul >
    <li th:each="user:${users}" th:text="${user.username}"></li>
</ul>
```

3、switch

```html
<div th:switch="${user.gender}">
    <p th:case="1">男</p>
    <p th:case="2">女</p>
    <p th:case="3">未知</p>
</div>
```

4、map

```html
<div>
    <p th:text="${map.get('key')}"></p>
    <p th:text="${map['key']"></p>
</div>
<!-- 遍历map -->
<div th:each="item:${map}">
    <p th:text="${item.key}"></p>
    <p th:text="${item.value}"></p>
</div>
```

>   each在哪，就从哪部分开始循环，如果each在p标签中，最终只会有一个div。

##### 4、引入外部资源

1、引入外部资源需要使用超链接表达式`@{}`，

```html
<link rel="stylesheet/css" th:href="@{home.css}">
```

2、在javascript中使用thymeleaf

```html
<script th:inline="javascript">
    // 最后的花括号里面是默认值
    const user = /*[[${user}]]*/{};
    console.log(user);
</script>
```

同理，css也可以使用thymeleaf渲染

3、给标签追加class

```html
<ul>
    <!-- 如果是最后一个，则添加属性avtive -->
    <li th:each="user,stat:${users}" th:text="${user.username}"
        th:appendclass="${stat.last}?avtive"
        >
    </li>
</ul>
```

#### 3、组件

##### 1、组件使用

定义组件，然后将组件替换或插入一个html'文件中。

首先，创建一个文件，存放组件，这个文件可以存放多个组件，然后在另一个文件中国使用它。

>   component.html

```html
<!-- 第一种方式 使用 th:fragement -->
<div th:fragement="com1">
	com1
</div>

<!-- 第二种方式 使用id或其他任何属性 -->
<div id="com2">
	com2
</div>
```

在另一个文件中使用组件，引用语法为 `th:replace="~{文件名::组件名}"`

>   index.html

```html
<!-- 使用组件替换原有div -->
<div th:replace="~{component::com1}"></div>

<!-- 在原有div中插入组件，使用id加载组件 # -->
<div th:insert="~{component::#com2}">
```

##### 2、扩展

1、对组件添加注释，让它知道我们引用对象的属性。

```html
<div th:fragment="com1">
    <!-- 使用 alt + enter 对报错进行修正，这样就不报红了 -->
    <!--/*@thymesVar id="user" type="com.zhuaowei.boottest.entity.User"*/-->
    <h2 th:text="${user.username}"></h2>
</div>
```

2、传递参数

```html
<!-- 使用组件 -->
<div th:insert="~{component::com3('传递参数')}">
    
</div>

<!-- 组件，接收参数 -->
<div th:fragment="com3(message)">
    <p th:text="${message}">
        
    </p>
</div>
```

##### 3、局部替换

将html元素作为参数传递给组件

```html
<!-- index.html -->
<div th:insert="~{component::com4(~{::#message})}">
    <p id="message">替换的模板</p>
</div>

<!-- component.html -->
<div th:fragment="com4(message)">
    <div th:replace="${message}">        
    </div>
</div>
```

#### 4、工具类

在thymeleaf中可以使用jsp中的一些默认对象，也可以使用一些java中的类。例如session，dates。具体参考jsp教程。

