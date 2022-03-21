# Maven

## 一、命令行

### 1.1、核心概念

#### 1.1.1、坐标

在maven仓库中确定jar的唯一标识，有三个维度，分别是groupId、artifactId、versionId。

1.   groupId：公司或组织域名、项目名，也是生成项目默认的包名。com.baidu.cloud
2.   artifactId：一个项目或一个模块的id，创建项目时创建的文件夹名。pro01
3.   versionId：版本，默认创建是1.0-SNAPSHOT。1.1-RELEASE

### 1.2、Java工程

#### 1.2.1、archetype

创建项目命令：`mvn archetype:generate`

archetype是maven的一个插件，负责创建项目。maven还有很多其他的插件，有这些插件负责maven的各项功能。

generate 插件执行的目标。

命令执行后进入交互页面，一次填写groupId、artifactId、versionId和protype，前两项为必填，后面直接回车使用默认值。完成后就创建了一个JavaWeb项目。目录结构如下：

```
E:\PROJECT\MAVEN-PROJECT\BLOG
│--- pom.xml （maven配置文件）
└───src
    ├───main （主文件夹）
    │   └───java
    │       └───top
    │           └───zhuaowei
    │               └───maven
    │                       App.java
    │
    └───test （测试文件夹）
        └───java
            └───top
                └───zhuaowei
                    └───maven
                            AppTest.java
```

#### 1.2.2、pom.xml

maven的核心配置文件，项目的坐标、依赖文件、测试、打包等都在这里配置。

```xml
<!-- project 表示对当前工程进行配置 -->
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <!-- maven 配置模板的版本，从2.0版本以后就一直是这个 -->
  <modelVersion>4.0.0</modelVersion>

  <!-- 坐标 -->
  <groupId>top.zhuaowei.maven</groupId>
  <artifactId>blog</artifactId>
  <version>1.0-SNAPSHOT</version>
  <!-- 打包方式 jar/war -->
  <!-- 如果是pom，说明是管理其他工程的工程 -->
  <packaging>jar</packaging>

  <name>blog</name>
  <url>http://maven.apache.org</url>

  <!-- 定义属性值 -->
  <properties>
    <!-- 构建时，从源码读取文件使用的字符集 -->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <!-- 依赖列表 -->
  <dependencies>
    <!-- 引入一个依赖 -->
    <dependency>
      <!-- 坐标 -->
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <!-- 作用范围 -->
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

#### 1.2.3、POM

Project Object Model（POM）是将工程抽象为一个模型，然后用程序中的对象来描述这个模型，就可以使用程序管理项目了。

具体体现就是pom.xml，它是maven项目的核心配置文件。

#### 1.2.4、目录结构

```
E:\PROJECT\MAVEN-PROJECT\BLOG
│   pom.xml
│
└───src （源码目录）
    ├───main （主程序目录）
    │   ├───java （Java源码文件夹）
    │   │   └───top （package目录）
    │   │       └───zhuaowei
    │   │           └───maven
    │   │                   App.java
    │   │
    │   └───resources （资源、配置文件夹）
    └───test （测试程序目录）
        └───java （Java源码文件夹）
            └───top （package目录）
                └───zhuaowei
                    └───maven
                            AppTest.java
```

另外，在构建时，会在项目目录下生成target文件夹，存放构建完成的文件。

#### 1.2.5、compile

对工程进行编译。

使用命令时，要先进入pom.xml所在的目录，执行 `mvn compile` ，就会对工程进行构建，构建结果放在了target/classes目录下。

对测试进行编译，执行 `mvn test-compile` ，编译结果放在了target/test-classes目录下。

#### 1.2.6、clean

删除构建的目录，即target目录。

#### 1.2.7、test

执行测试程序，执行测试的报告存放在target/surefire-reports目录下。

#### 1.2.8、package

将工程打成jar包，存放到tar目录下。

`mvn package`

#### 1.2.9、install

编译后，将生成的jar包存入到本地仓库中，位置在坐标所写位置。

### 1.3、Web工程

生成Web项目需要使用相应的archetype，即maven-archetype-webapp。下面是maven创建项目的命令模板。

```
mvn archetype:generate 
	-DgroupId={project-packaging}
	-DartifactId={project-name}
	-DarchetypeArtifactId={maven-template} 
	-DinteractiveMode=false
```

使用模板生成相应的Java Web工程。

```
mvn archetype:generate -DgroupId=com.mkyong.web -DartifactId=java-web-project -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
```

生成的目录结构：

```
E:\PROJECT\MAVEN-PROJECT\MY-APP
│   pom.xml
│
└───src
    └───main
        └───webapp
            │   index.jsp
            │
            └───WEB-INF
                    web.xml
```

少很多目录，需要手动添加：

```
E:\PROJECT\MAVEN-PROJECT\MY-APP
│   pom.xml
│
└───src
    ├───main
    │   ├───java
    │   │   └───com
    │   │       └───mycompany
    │   │           └───app
    │   ├───resources （资源配置文件夹）
    │   └───webapp （jsp文件夹）
    │       │   index.jsp
    │       │
    │       └───WEB-INF （web配置文件夹）
    │               web.xml
    │
    └───test
        └───java
            └───com.mycompany.app
```

#### 1.1.12、pom.xml（Web）

与Java工程大同小异。

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <name>my-app Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!-- 使用的java版本 -->
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <!-- 
    build 节点，
   -->
</project>
```

#### 1.1.13、构建Web工程

1.   添加java目录和package目录。
2.   创建servlet目录，创建Servlet类，编写代码处理请求。
3.   配置web.xml，注册servlet。
4.   在index.jsp中编写链接。
5.   直接编译会出错，因为没有servlet-api.jar。所以在编译之前还要导入servlet的依赖。

```xml
<!-- servlet-api，正常使用HttpServlet -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>
```

6.   编译。`mvn compile`
7.   部署到tomcat的webapps目录下
8.   启动tomcat，访问本地8080端口和项目名。

为了方便访问，建议将项目打成的war包改个简单的名字，例如demo。访问 `http://localhost:8080/demo/index.jsp`

### 1.4、dependency

在web工程中依赖Java工程，即引入Jar包。Java工程可以引用Java工程，但不能引入Web工程；Web工程只能引入Java工程。

使用 dependency插件可以查看工程引入所有的Java工程依赖，包括间接的。`mvn dependency:list`

也可以使用树形结构查看各依赖之间的引用关系。`mvn dependency:tree`

>   `mvn dependency:list`

```
[INFO] --- maven-dependency-plugin:2.8:list (default-cli)
 @ my-app ---
[INFO]
[INFO] The following files have been resolved:
[INFO]    org.hamcrest:hamcrest-core:jar:1.3:test
[INFO]    javax.servlet:javax.servlet-api:jar:3.1.0:provided
[INFO]    junit:junit:jar:4.12:test
```

>   `mvn dependency:tree`

```
[INFO] --- maven-dependency-plugin:2.8:tree (default-cli)
 @ my-app ---
[INFO] com.mycompany.app:my-app:war:1.0-SNAPSHOT
[INFO] +- junit:junit:jar:4.12:test
[INFO] |  \- org.hamcrest:hamcrest-core:jar:1.3:test
[INFO] \- javax.servlet:javax.servlet-api:jar:3.1.0:provided
```

### 1.5、依赖范围 （scope）

#### 1.5.1、取值

可选值：compile（默认值）/test/provided/system/runtime/import

1.   compile：一直有效。
2.   test：只在test目录下有效，切部署到服务器无效。
3.   provided：所有目录有效，部署到服务器无效。意思是已提供的，服务器会提供，打包时不再打包。

#### 1.5.2、传递性

A依赖B，B依赖C（A -> B -> C），A能否使用C取决于B引入C的时候使用什么范围。

compile：可以。

test/provided：不可以。

#### 1.5.3、阻断（exclusion）

当jar包依赖不同版本的Jar包时，有可能会发生错误，所以就需要将其中的Jar包进行阻断。例如图中的B

```
     -x-> B  -> D-1.0
A  /  --> C -> D-2.0
```

```
<dependency>
    ...
    <!-- 配置排除的依赖 -->
    <exclusions>
      <!-- 配置一个排除项 -->
      <exclusion>
        <!-- 坐标不需要写versionId，会排除指定的所有版本 -->
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
      </exclusion>
    </exclusions>
</dependency>
```

### 1.6、继承

B工程继承A工程，实际上是B工程的pom.xml继承A工程的pom.xml。A工程就是B工程的父工程，B就是A的子工程。在IDEA中父工程叫Project，子工程叫Module。

#### 1.6.1、创建一个父子工程

1.   首先创建一个普通的工程。`mvn archetype:generate` 
2.   修改pom.xml，将打包方式改为 pom。意思是该工程不包含代码，只是作为管理其他工程的工程。
3.   进入pom.xml所载文件夹，再次创建几个模块工程。`mvn archetype:generate`
4.   在父工程添加依赖管理。
5.   在子工程添加依赖。

>   父工程的pom.xml

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>top.zhuaowei.maven</groupId>
  <artifactId>pro03</artifactId>
  <version>1.0-SNAPSHOT</version>
  <!-- 修改打包方式为pom，意为管理其他工程，不包含源代码 -->
  <packaging>pom</packaging>

  <name>pro03</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <!-- 依赖管理，统一配置依赖的版本 -->
  <!-- 这里配置了，不代表模块可以使用，模块还需要自己配置 -->
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <!-- 创建模块时，自动添加的配置，表示该工程有以下3个模块 -->
  <modules>
	<module>pro04-module</module>
    <module>pro05-module</module>
    <module>pro06-module</module>
  </modules>
</project>
```

注意：父工程里的依赖并不会直接引入到子工程中，而是子工程需要什么依赖就写什么依赖，只是可以简写版本号，便于管理依赖版本。如果写了版本号，则以子工程中的为准。

>   子工程的pom.xml

```xml
<?xml version="1.0"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <modelVersion>4.0.0</modelVersion>

  <!-- 指向父工程的坐标 -->
  <parent>
    <groupId>top.zhuaowei.maven</groupId>
    <artifactId>pro03</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>
  
  <!-- groupId 和 version 如果与父工程相同，可以省略不写 -->
  <!-- <groupId>top.zhuaowei.maven</groupId> -->
  <artifactId>pro04-module</artifactId>
  <!-- <version>1.0-SNAPSHOT</version> -->
  
  <name>pro04-module</name>
  <url>http://maven.apache.org</url>
  
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12/version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

这样每个模块根据自己的需要，选择不同的依赖，依赖版本在父工程统一管理。

#### 1.6.2、更该依赖版本

如果要修改父工程依赖的版本，依赖版本太多，修改起来很麻烦，例如要引入spring的各种包的版本要修改，这时候可以定义父工程的属性，把版本作为一个变量，依赖版本里写下这个变量就可以了。

使用属性标签时，标签名作为属性名，内容作为属性值。

`<pro.spring.version>5.1.3.RELEASE</pro.spring.version>`

使用时，用 `${pro.spring.version}` 使用该属性值。

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>top.zhuaowei.maven</groupId>
  <artifactId>pro03</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>

  <name>pro03</name>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!-- 定义属性值，代表spring的版本 -->
    <pro.spring.version>5.1.3.RELEASE</pro.spring.version>
  </properties>

  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <!-- 使用定义的属性，获取对应版本的依赖 -->
        <version>${pro.spring.version}</version>
      </dependency>
      
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <!-- 使用定义的属性，获取对应版本的依赖 -->
        <version>${pro.spring.version}</version>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <modules>
	<module>pro04-module</module>
    <module>pro05-module</module>
    <module>pro06-module</module>
  </modules>
</project>
```

### 1.7、聚合

当公司项目做大的时候，一个父工程有很多子工程，子工程又有很多子工程，...这些工程组合成一个完整的项目。

#### 1.7.1、好处

1.   便于项目管理
2.   一键执行。项目中的各种关系有maven来管理，自动按顺序执行install等命令。一个工程在install时，如果有父工程，必须对父工程先install。

#### 1.7.2、配置

在父工程配置 module 即可

#### 1.7.3、循环

如果依赖关系出现循环，则会报错，所以在工作时要避免循环。

### 1.8、生命周期

#### 1.8.1、作用

为了完成自动化操作，Maven定义了三个生命周期。每一个生命周期中的一个环节对应一个操作。

maven会从头执行命令到指定的生命周期。

#### 1.8.2、三个生命周期

1.   clean
2.   site
3.   default

#### 1.8.3、插件

Maven执行命令时调用插件，插件执行的目标，也叫插件的功能，与生命周期一一对应。这些插件的功能是由Jar文件实现的。

#### 1.8.4、仓库

1.   本地仓库
2.   远程仓库
     1.   局域网仓库，局域网的Maven仓库，Nexus技术搭建
     2.   中央仓库
     3.   镜像仓库，与中央仓库内容保持一致，分担压力。

