# Spring Security 

## 1、简介

## 2、第一个Spring Security程序

1、新建一个spring boot项目，导入依赖

>   pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.zhuaowei</groupId>
    <artifactId>securityDemo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>securityDemo</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <!-- spring boot web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>2.2.1.RELEASE</version>
        </dependency>

        <!-- security -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
            <version>2.1.7.RELEASE</version>
        </dependency>

        <!-- test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- log -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

2、编写一个controller类

>   TestController.java

```java
package com.zhuaowei.securitydemo.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @ClassName: TestController
 * @Description: TODO add a description
 * @Author: zhuaowei
 * @Date: 2021/9/17
 * @Version: 1.0
 */

@RestController
@RequestMapping("/test")
public class TestController {
    @GetMapping("hello")
    public String hello() {
        return "hello! spring security!";
    }
}
```

3、启动spring boot

在浏览器输入对应的url，然后会弹出一个spring security的登录页面，登录成功之后才能访问url。

登录的默认用户名是 `user` ，密码在运行控制台会打印。

## 3、基本原理

spring security 本质就是一个过滤器链。

### 常见的过滤器

1、FilterSecurityInterceptor

一个方法级的权限过滤器，基本位于过滤器的最底部

2、ExceptionTranslationFilter

异常过滤器，用来处理在认证授权过程中抛出的异常

3、UsernamePasswordAuthenticationFilter

对/login的post平求做拦截，校验表单中的用户名密码。

### 过滤器如何加载？

1、首先使用Spring Security配置过滤器，DelegatingFilterProxy类中的doFilter方法。doFilter中调用了initDelegate方法，initDelegate()方法中获取了FilterChainProxy类，这个是内置的。

2、在FilterChainProxy中，嗲用doFilter()，doFilter中调用了doFilterInternel(), doFilterInternel中获取了所有的过滤器。

>   (DelegatingFilterProxy类中) doFilter -> initDelegate -> (FilterChainProxy ) doFilter -> doFilterInternel -> filters(List)

### 重要接口

1、UserDetailsService

 查询数据库的过程，返回的对象是 UserDetails，里面包含了用户登录的基本信息和认证方法。

查询完成后交给前面提到的UsernamePasswordAuthenticationFilter过滤器，在这个过滤器中调用attemptAuthentication方法进行验证。如果成功，执行successfulAuthentication方法，不成功执行unsuccessfulAuthentication方法。

如果自己实现用户认证功能，需要继承UsernamePasswordAuthenticationFilter类，重写里面的三个方法。还需要实现UserDetailsService接口，编写查询数据库的方法，返回一个UserDetails对象。

2、PasswordEncoder

对密码进行加密，用于返回UserDetails中方的密码加密。

## 4、web权限方案

#### 1、认证

##### 1、如何设置登录的用户信息

###### 1、通过配置文件

>   application.properties

```properties
spring.security.user.name=admin
spring.security.user.password=123456
```

###### 2、过配置类

>   config.SecurityConfig.java

```java
package com.zhuaowei.securitydemo.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

/**
 * @ClassName: SecurityConfig
 * @Description: security 配置类
 * @Author: zhuaowei
 * @Date: 2021/9/17
 * @Version: 1.0
 */

@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
        String password = passwordEncoder.encode("123");
        auth.inMemoryAuthentication().withUser("tom").password(password).roles("admin");
    }

    /**
     * @Author zhuaowei
     * @Description 进行用户加密时需要用到这个类，要配置bean，否则无法登录
     * @Date 2021/9/17 18:22
     * @Param []
     * @Return org.springframework.security.crypto.password.PasswordEncoder
     */
    @Bean
    PasswordEncoder password() {
        return new BCryptPasswordEncoder();
    }
}
```

###### 3、自定义实现类，即上面提到的方法

>   config.SecurityConfig.java

```java
package com.zhuaowei.securitydemo.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

/**
 * @ClassName: SecurityConfig
 * @Description: security 配置类
 * @Author: zhuaowei
 * @Date: 2021/9/17
 * @Version: 1.0
 */

@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 使用注入的userDetailsService来认证登录
        auth.userDetailsService(userDetailsService).passwordEncoder(password());
    }

    /**
     * @Author zhuaowei
     * @Description 进行用户加密时需要用到这个类，要配置bean，否则无法登录
     * @Date 2021/9/17 18:22
     * @Param []
     * @Return org.springframework.security.crypto.password.PasswordEncoder
     */
    @Bean
    PasswordEncoder password() {
        return new BCryptPasswordEncoder();
    }
}
```

>   service.MyUserDetailsService.java

```java
package com.zhuaowei.securitydemo.service;

import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.AuthorityUtils;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * @ClassName: MyUserDetailsService
 * @Description: TODO add a description
 * @Author: zhuaowei
 * @Date: 2021/9/17
 * @Version: 1.0
 */

// 名字要与依赖注入的名字对应
@Service("userDetailsService")
public class MyUserDetailsService implements UserDetailsService {
    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        // 获取角色列表
        List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("admin");
        // 创建一个User对象，包括用户名，密码，角色
        return new User("jerry", new BCryptPasswordEncoder().encode("123"), auths);
    }
}
```

###### 4、通过数据库获取认证信息

首先导入需要的依赖，使用Mybatis-Plus演示

>   pom.xml

```xml
<!-- mybatis-plus -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.3</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.34</version>
</dependency>

<!-- lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
```

创建需要的user数据库

>   init.sql

```sql
create database  security_demo
use security_demo;
create table `user` (
                        `id` BIGINT(20) AUTO_INCREMENT NOT NULL COMMENT '用户id',
                        `username` varchar(32) NOT NULL COMMENT '用户名',
                        `password` VARCHAR(32) NOT NULL COMMENT '密码',
                        PRIMARY KEY (`id`)
) ENGINE = INNODB, DEFAULT CHARSET utf8;
```

配置数据库基本信息

>   application.properties

```properties
# 数据库基本信息配置
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://47.98.214.74:3306/bookstore?useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8
spring.datasource.username=root
spring.datasource.password=zhuaowei0521++
```

编写entity类，编写mapper文件

>   entity.User.java

```java
@Getter
@Setter
public class User {
    private Integer id;
    private String username;
    private String password;

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

>   mapper.UserMapper.java

```java
@Repository
public interface UserMapper extends BaseMapper<User> {
}
```

使用mapper查询数据库，然后把结果交给UserDetails处理

>   service.MyUserDetailsService.java

```java
@Autowired
private UserMapper userMapper;

@Override
public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
    // 方法传入的参数就是从前台获取的用户名
    // 使用这个用户名从数据库中查询信息，然后校对
    QueryWrapper<com.zhuaowei.securitydemo.entity.User> queryWrapper = new QueryWrapper<>();
    queryWrapper.eq("username", username);
    com.zhuaowei.securitydemo.entity.User user = userMapper.selectOne(queryWrapper);
    if (user == null) {
        throw new UsernameNotFoundException("用户不存在");
    }
    // 获取角色列表
    List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("admin");
    // 创建一个User对象，包括用户名，密码，角色
    return new User(username, new BCryptPasswordEncoder().encode(user.getPassword()), auths);
}
```

>   访问过程
>
>   /login.html -(提交表单)-> /user/login -(成功)-> /test/index

##### 2、设置登录页面

>   config.SecurityConfig.java 添加方法

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.formLogin()    // 自定义自己编写的登录页面
    .loginPage("/login.html")   // 登录页面设置
    .loginProcessingUrl("/user/login")  // 登录访问路径
    .defaultSuccessUrl("/test/index").permitAll()   // 登录成功后跳转路径
    .and().authorizeRequests()
            // 不要验证也可以访问的路径
            .antMatchers("/", "/test/hello", "/user/login").permitAll()
    .anyRequest().authenticated()
            .and().csrf().disable();    // 关闭csrf防护
}
```

>   login.html

```html
<form action="/user/login" method="post">
    用户名： <input type="text" name="username" placeholder="username"><br>
    密码： <input type="password" name="password" placeholder="password"><br>
    <input type="submit" value="登录">
</form>
```

>   访问过程
>
>   /login.html -(提交表单)-> /user/login -(成功)-> /test/index

#### 2、授权

##### 1、基于角色或权限进行访问

登录成功后必须是指定的角色才可以访问。

1、hasAuthority()

`http.antMatchers("/test").hasAuthority("admin")`

admin角色对/test有访问权限，如果要访问/test，需要在UserDetailsService中设置用户的角色。

```
List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("admin");
```

2、hasAnyAuthority()

`http.antMatchers("/test").hasAnyAuthority("admin,manager")`

这个方法与上面的同理，不过可以设置多个角色，角色之间用 `,` 隔开，只要访问的用户具有其中一个角色就可以访问。

3、hasRole()

`http.antMatchers("/test").hasRole("admin")`

与hasAuthority()类似，底层角色字符串与上面的不一样，它会把用户传入的字符串前面拼接一个 `ROLE_`，所以在赋予用户角色的使用需要在角色面前加一个 `ROLE_`，admin -> ROLE_admin

```java
List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("admin,ROLE_manager");
```

4、hasAnyRole()

同理，这个可以设置多个角色。

`http.antMatchers("/test").hasAnyRole("admin,manager")`

##### 2、自定义403界面

```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 403页面，路径怎么取决于是静态资源还是动态资源，thymeleaf
        http.exceptionHandling().accessDeniedPage("/error");
    }
```

##### 3、注解

认证授权注解使用

使用注解开启认证授权时，需要在启动类中启用全局方法安全验证。

```java
@EnableGlobalMethodSecurity(securityEnabled=true)
```

###### 1、Secured

使用方法：在controller类的方法中加上此注解，注解中可以设置多个角色，角色名要以 `ROLE_` 开头。设置完成后，需要在UserDetailsService中设置角色才可以访问。

```java
@GetMapping("update")
@Secured({"ROLE_admin", "ROLE_manager"})
public String update() {
    return "hello Secured!";
}
```

###### 2、PreAuthorize

使用该注解需要在启动类上启用。`prePostEnabled=true` 此方法会在进入方法之前进行验证

```java
@EnableGlobalMethodSecurity(securityEnabled=true,prePostEnabled=true)
```

>   TestController.java

```java
@GetMapping("update")
// 可以使用上面提到的四种方法，注意引号
@PreAuthorize("hasAnyAuthorize('admin')")
public String update() {
    return "hello Secured!";
}
```

###### 3、PostAuthorize

这个注解与上面的类似，是在方法执行之后进行验证。

4、PreFilter

在方法执行前对数据进行过滤，只留下某些符合条件的数据，即过滤传入传入的参数。

```java
@GetMapping("modify")
@PreAuthorize("hasAnyAuthorize('admin')")
// 只保留username为zhangsan的数据，传入的数据是一个List或set
@PreFilter("filterObject.username=='zhangsan'")
public String delete() {
    return "delete!";
}
```

###### 5、PostFilter

与上面类似，是在方法执行后进行过滤，即过滤返回的数据。

```java
@GetMapping("modify")
@PreAuthorize("hasAnyAuthorize('admin')")
@PostFilter("filterObject.username=='zhangsan'")
public List<Member> modify() {
    ...
    return new List<Member>();
}
```

#### 3、记住我

使用cookie和数据库实现自动登录。

用户第一次登录验证成功时，会启用RememberMeService，服务生成加密token，并把这个Token分别存储到浏览器和服务器。

第二次登录时，浏览器带着Token访问URL，接着RememberMeService会在数据库中查找Token，Token比对成功后就认证成功。

Cookie会保存用户的登录信息。存储Token的数据库spring会帮我们创建，也可以自己创建。

1、创建数据表

需要创建存储token的数据表，如果不创建，可以使用spring帮我们创建。

>   jdbcTokenRepositoryImpl.java

```mysql
create table persistent_logins (
    username varchar(64) not null, 
    series varchar(64) primary key, 
    token varchar(64) not null,
    last_used timestamp not null
)
```

2、配置

配置数据源和Token方法

>   SecurityConfig.java

```java
// 注入数据源
@Autowired
private DataSource dataSource;

// 注册一个bean
@Bean
public PersistentTokenRepository persistentTokenRepository() {
    JdbcTokenRepositoryImpl jdbcTokenRepository = new JdbcTokenRepositoryImpl();
    jdbcTokenRepository.setDataSource(dataSource);
    // 这句代码会帮我们创建token数据库
    jdbcTokenRepository.setCreateTableOnStartup(true);
    return jdbcTokenRepository;
}
```

3、应用上面的配置

>   SecurityConfig.java
>
>   protected void configure(HttpSecurity http) throws Exception// 方法

```java
http
    .formLogin()    // 自定义自己编写的登录页面
    .loginPage("/login")   // 登录页面设置
    .loginProcessingUrl("/user/login")  // 登录访问路径
    .defaultSuccessUrl("/test/index").permitAll()   // 登录成功后跳转路径
    .and().authorizeRequests()
    // 不要验证也可以访问的路径
    .antMatchers("/", "/**").permitAll()
    .anyRequest().authenticated()
    
    /********** 开启RememberMe功能 *************/
    // 设置数据源
    .and().rememberMe().tokenRepository(persistentTokenRepository())
    // 设置cookie有效时间
    .tokenValiditySeconds(60)
    // 用户信息
    .userDetailsService(userDetailsService)
    
    .and().csrf().disable();    // 关闭csrf防护
```

4、修改前端页面

在前端页面中添加一个复选框，代表自动登录，复选框的name属性必须是remember-me

```
<input type="checkbox" name="remember-me" title="记住我"> 七天内免登录 <br>
```

