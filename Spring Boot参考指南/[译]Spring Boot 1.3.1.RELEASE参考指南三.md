# 第三部分、使用Spring Boot

这部分会更详细的介绍如何使用Spring Boot。包括构建系统、自动配置和如何运行应用，还包含了一些Spring Boot的最佳实践。虽然Spring Boot没有什么特别的地方（它只是另一个你可以使用的库），如果你听取这些建议，你的开发过程会变得简单。

## 13.构建系统

强烈建议你选择一个支持依赖管理的构建系统，其中就可以使用artifacts发布到“Maven Central”仓库。我们推荐你选择Maven或Gradle。其他构建系统也可以使用（比如Ant），只是没有获得特别的支持。

### 13.1.依赖管理

每一个Spring Boot的发布版本都提供了一个依赖支持的列表。实际上，在构建配置中你根本不需要为这些依赖提供版本信息，因为Spring Boot已经为你管理了。当你升级Spring Boot的时候，这些依赖也会跟着升级。

>如果你觉得需要指定版本，那么你依然可以指定版本来覆盖Spring Boot的建议版本。

<!--more-->

列表中包含了所有的spring模块，你可以和使用第三方类库一样使用Spring Boot。列表像一个标准的物料清单(spring-boot-dependencies)一样可用，额外的支持对于Maven和Gradle可用。

>每一个Spring Boot的发布版本都带有一个Spring Framework的版本，我们强烈建议你不要自己指定版本。

### 13.2.Maven

Maven用户可以继承**spring-boot-starter-parent**项目获取适合的默认设置。父项目提供了以下特性：

- 将Java 1.6作为默认编译等级。
- 源码为UTF-8编码。
- 对于一般依赖，依赖管理部分继承了**spring-boot-dependencies**POM，则允许省略&lt;version>标签，
- 合理的[资源过滤](https://maven.apache.org/plugins/maven-resources-plugin/examples/filter.html)
- 合理的插件配置（[exec plugin](http://www.mojohaus.org/exec-maven-plugin/), [surefire](http://maven.apache.org/surefire/maven-surefire-plugin/), [Git commit ID](https://github.com/ktoso/maven-git-commit-id-plugin), [shade](http://maven.apache.org/plugins/maven-shade-plugin/)）
- 对于**application.properties**和**application.yml**合理的资源过滤

最后一点：由于默认的配置文件支持Spring风格的占位符**(${…​})**，Maven过滤改为**@..@**占位符（你可以通过修改**resource.delimiter**覆盖它）。

#### 13.2.1.继承starter parent

设置**；parent**继承**spring-boot-starter-parent**来配置你的项目
<pre class="prettyprint">
   &lt;!-- Inherit defaults from Spring Boot -->
    &lt;parent>
        &lt;groupId>org.springframework.boot&lt;/groupId>
        &lt;artifactId>spring-boot-starter-parent&lt;/artifactId>
        &lt;version>1.3.1.RELEASE&lt;/version>
    &lt;/parent>
</pre>
>你只需指定Spring Boot的版本号。如果你导入额外的starters，你可以安全地省略版本号设置。

你也可以通过重写你项目中的property来覆盖你的依赖。例如：升级另一个Spring Data版本，你需要添加以下到你的**pom.xml**。
<pre class="prettyprint">
    &lt;properties>
        &lt;spring-data-releasetrain.version>Fowler-SR2&lt;/spring-data-releasetrain.version>
    &lt;/properties>
</pre>
>检查[**spring-boot-dependencies**pom](http://github.com/spring-projects/spring-boot/tree/v1.3.1.RELEASE/spring-boot-dependencies/pom.xml)中支持的properties列表。

#### 13.2.2.使用不带有父POM的Spring Boot

不是所有人都喜欢继承**spring-boot-starter-parent**。你可能有你们公司的标准的parent需要使用，或许你喜欢显示地声明所有的Maven配置。

如果你不想使用**spring-boot-starter-parent**，通过使用**scope=import**依赖，你依然可以享受依赖管理带来的好处（但不是插件管理）：
<pre class="prettyprint">
    &lt;dependencyManagement>
         &lt;dependencies>
            &lt;dependency>
                &lt;!-- Import dependency management from Spring Boot -->
                &lt;groupId>org.springframework.boot&lt;/groupId>
                &lt;artifactId>spring-boot-dependencies&lt;/artifactId>
                &lt;version>1.3.1.RELEASE&lt;/version>
                &lt;type>pom&lt;/type>
                &lt;scope>import&lt;/scope>
            &lt;/dependency>
        &lt;/dependencies>
    &lt;/dependencyManagement>
</pre>
这个设置不允许你使用如上所述的一个属性覆盖个人依赖。为了达到相同的效果，你需要在**spring-boot-dependencies**项目之前的**dependencyManagement**中增加一个项目。例如：升级到另一个Spring Data版本,你需要增加以下配置到你的**pom.xml**。
<pre class="prettyprint">
    &lt;dependencyManagement>
        &lt;dependencies>
            &lt;!-- Override Spring Data release train provided by Spring Boot -->
            &lt;dependency>
                &lt;groupId>org.springframework.data&lt;/groupId>
                &lt;artifactId>spring-data-releasetrain&lt;/artifactId>
                &lt;version>Fowler-SR2&lt;/version>
                &lt;scope>import&lt;/scope>
                &lt;type>pom&lt;/type>
            &lt;/dependency>
            &lt;dependency>
                &lt;groupId>org.springframework.boot&lt;/groupId>
                &lt;artifactId>spring-boot-dependencies&lt;/artifactId>
                &lt;version>1.3.1.RELEASE&lt;/version>
                &lt;type>pom&lt;/type>
                &lt;scope>import&lt;/scope>
            &lt;/dependency>
        &lt;/dependencies>
    &lt;/dependencyManagement>
</pre>

>在上边的例子中，我们指定了BOM，但是任何依赖类型都可以覆盖。

#### 12.2.3.更改Java版本

**spring-boot-starter-parent**对于Java兼容非常保守。如果你想听取我们的建议，选择最新的Java版本，你可以增加**java.version**属性。
<pre class="prettyprint">
    &lt;properties>
        &lt;java.version>1.8&lt;/java.version>
    &lt;/properties>
</pre>
#### 12.2.4.使用Spring Boot Maven插件

Spring Boot包含一个Maven插件，它可以把项目打包成一个可执行的jar。如果你想使用它，请增加插件到**&lt;plugins>部分：
<pre class="prettyprint">
    &lt;build>
        &lt;plugins>
            &lt;plugin>
                &lt;groupId>org.springframework.boot&lt;/groupId>
                &lt;artifactId>spring-boot-maven-plugin&lt;/artifactId>
            &lt;/plugin>
        &lt;/plugins>
    &lt;/build>
</pre>

>如果你使用Spring Boot的 starter parent pom，你只需要增加插件，不需要配置，除非你想改变parent中定义的设置。

### 13.3.Gradle

Gradle用户可以直接在**dependencies**部分导入“starter POMs”。不像Maven，Gradle没有“super parent”来导入共享配置。
<pre class="prettyprint">
    apply plugin: 'java'

    repositories {
        jcenter()
    }

    dependencies {
        compile("org.springframework.boot:spring-boot-starter-web:1.3.1.RELEASE")
    }
</pre>

**spring-boot-gradle-plugin**也可以创建可执行的jar，从源码运行项目。它也提供了依赖管理，包括允许你忽略Spring Boot管理的依赖的版本号。
<pre class="prettyprint">
    buildscript {
        repositories {
            jcenter()
        }

        dependencies {
            classpath("org.springframework.boot:spring-boot-gradle-plugin:1.3.1.RELEASE")
        }
    }

    apply plugin: 'java'
    apply plugin: 'spring-boot'

    repositories {
        jcenter()
    }

    dependencies {
        compile("org.springframework.boot:spring-boot-starter-web")
        testCompile("org.springframework.boot:spring-boot-starter-test")
    }
</pre>

### 13.4.Ant

使用 Apache的Ant+Ivy也可以构建Spring Boot项目。**spring-boot-antlib**“AntLib”模块也可以让ant创建可执行的jar。

声明一个典型的**ivy.xml**文件，大概像这样：
<pre class="prettyprint">
    &lt;ivy-module version="2.0">
        &lt;info organisation="org.springframework.boot" module="spring-boot-sample-ant" />
        &lt;configurations>
            &lt;conf name="compile" description="everything needed to compile this module" />
            &lt;conf name="runtime" extends="compile" description="everything needed to run this module" />
        &lt;/configurations>
        &lt;dependencies>
            &lt;dependency org="org.springframework.boot" name="spring-boot-starter"
                rev="${spring-boot.version}" conf="compile" />
        &lt;/dependencies>
    &lt;/ivy-module>
</pre>
一个典型的**build.xml**像这样：
<pre class="prettyprint">
    &lt;project
        xmlns:ivy="antlib:org.apache.ivy.ant"
        xmlns:spring-boot="antlib:org.springframework.boot.ant"
        name="myapp" default="build">

        &lt;property name="spring-boot.version" value="1.3.0.BUILD-SNAPSHOT" />

        &lt;target name="resolve" description="--> retrieve dependencies with ivy">
            &lt;ivy:retrieve pattern="lib/[conf]/[artifact]-[type]-[revision].[ext]" />
        &lt;/target>

        &lt;target name="classpaths" depends="resolve">
            &lt;path id="compile.classpath">
                &lt;fileset dir="lib/compile" includes="*.jar" />
            &lt;/path>
        &lt;/target>

        &lt;target name="init" depends="classpaths">
            <mkdir dir="build/classes" />
        &lt;/target>

        &lt;target name="compile" depends="init" description="compile">
            &lt;javac srcdir="src/main/java" destdir="build/classes" classpathref="compile.classpath" />
        &lt;/target>

        &lt;target name="build" depends="compile">
            &lt;spring-boot:exejar destfile="build/myapp.jar" classes="build/classes">
                &lt;spring-boot:lib>
                    &lt;fileset dir="lib/runtime" />
                &lt;/spring-boot:lib>
            &lt;/spring-boot:exejar>
        &lt;/target>
    &lt;/project>
</pre>

>如果你不想使用**spring-boot-antlib**查看[Section 79.8, “Build an executable archive from Ant without using spring-boot-antlib”](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#howto-build-an-executable-archive-with-ant)“How-to”。

### 13.5.Starter POMs

Starter POM是包含在应用中的一系列方便的依赖描述。你可以得到所有你需要的Spring和相关技术的一站式服务，而无需通过搜索示例、复制粘贴大量的依赖描述。例如：如果你准备使用Spring和JPA进行数据访问，只需要在项目中包含**spring-boot-starter-data-jpa**依赖。你就可以顺利的开始了。

starters包含了大量的你搭建项目、快速运行所需要的依赖，它具有一致,支持传递依赖关系管理。

>**名字的含义**
>所有**官方**的starters遵循这样的命名规则；**spring-boot-starter-\***，**\***是一个特定类型的应用。这种命名结构旨在帮你快速找到所需的starter。Maven已经集成在大多数IDE中，并允许你通过名称搜索依赖。例如，在安装了插件的Eclipse或者STS中，你可以在POM编辑器中点击**ctrl-space**，然后输入“spring-boot-starter”来获取一个完整的列表。
>因为在Creating your own starter的部分的解释，第三方starters不应该以**spring-boot**开头，它应该留给官方的Spring Boot artifacts。第三方starter **acme**应该被命名为**acme-spring-boot-starter**。

Spring Boot在**org.springframework.boot** group下提供了以下应用starters：

**表13.1.Spring Boot应用starters**

<table>
    <tr>
        <td>名称</td>
        <td>描述</td>
    </tr>
    <tr>
        <td>spring-boot-starter</td>
        <td>核心Spring Boot starter，包括自动配置、logging 和YAML的支持</td>
    </tr>
    <tr>
        <td>spring-boot-starter-actuator</td>
        <td>生产准备特性，帮你监视和管理应用</td>
    </tr>
    <tr>
        <td>spring-boot-starter-amqp</td>
        <td>通过spring-rabbit支持“高级消息队列协议”</td>
    </tr>
    <tr>
        <td>spring-boot-starter-aop</td>
        <td>支持面向切面编程，包括spring-aop和AspectJ</td>
    </tr>
    <tr>
        <td>spring-boot-starter-artemis</td>
        <td>通过Apache Artemis支持“Java Message Service API” </td>
    </tr>
    <tr>
        <td>spring-boot-starter-batch</td>
        <td>支持“Spring Batch”，包括HSQLDB数据库</td>
    </tr>
    <tr>
        <td>spring-boot-starter-cache</td>
        <td>支持Spring的Cache abstraction</td>
    </tr>
    <tr>
        <td>spring-boot-starter-cloud-connectors</td>
        <td>支持“Spring Cloud Connectors”，简化了对于Cloud Foundry和Heroku等云平台服务的连接</td>
    </tr>
    <tr>
        <td>spring-boot-starter-data-elasticsearch</td>
        <td>支持Elasticsearch的搜索和分析引擎，包括spring-data-elasticsearch</td>
    </tr>
    <tr>
        <td>spring-boot-starter-data-gemfire</td>
        <td>支持gemfire分布式存储，包括spring-data-gemfire</td>
    </tr>
    <tr>
        <td>spring-boot-starter-data-jpa</td>
        <td>支持 “Java Persistence API”，包括spring-data-jpa，spring-orm和Hibernate</td>
    </tr>
    <tr>
        <td>spring-boot-starter-data-mongodb</td>
        <td>支持MongoDB NoSQL数据库，包括spring-data-mongodb</td>
    </tr>
    <tr>
        <td>spring-boot-starter-data-rest</td>
        <td>通过spring-data-rest-webmvc支持使用REST暴露Spring Data仓库</td>
    </tr>
    <tr>
        <td>spring-boot-starter-data-solr</td>
        <td>支持 Apache Solr搜索平台，包括spring-data-solr</td>
    </tr>
    <tr>
        <td>spring-boot-starter-freemarker</td>
        <td>支持FreeMarker模版引擎</td>
    </tr>
    <tr>
        <td>spring-boot-starter-groovy-templates</td>
        <td>支持Groovy模版引擎</td>
    </tr>
    <tr>
        <td>spring-boot-starter-hateoas</td>
        <td>通过spring-hateoas支持HATEOAS-based RESTful服务</td>
    </tr>
    <tr>
        <td>spring-boot-starter-hornetq</td>
        <td>通过HornetQ支持“Java Message Service API”</td>
    </tr>
    <tr>
        <td>spring-boot-starter-integration</td>
        <td>支持常用的spring-integration模块</td>
    </tr>
    <tr>
        <td>spring-boot-starter-jdbc</td>
        <td>支持JDBC数据库</td>
    </tr>
    <tr>
        <td>spring-boot-starter-jersey</td>
        <td>支持Jersey RESTful web服务框架</td>
    </tr>
    <tr>
        <td>spring-boot-starter-jta-atomikos</td>
        <td>通过于Atomikos支持JTA分布式事务</td>
    </tr>
    <tr>
        <td>spring-boot-starter-jta-bitronix</td>
        <td>通过于Bitronix支持JTA分布式事务</td>
    </tr>
    <tr>
        <td>spring-boot-starter-mail</td>
        <td>支持javax.mail</td>
    </tr>
    <tr>
        <td>spring-boot-starter-mobile</td>
        <td>支持spring-mobile</td>
    </tr>
    <tr>
        <td>spring-boot-starter-mustache</td>
        <td>支持Mustache模版引擎</td>
    </tr>
    <tr>
        <td>spring-boot-starter-redis</td>
        <td>支持REDIS key-value数据存储，包括spring-redis</td>
    </tr>
    <tr>
        <td>spring-boot-starter-security</td>
        <td>支持spring-security</td>
    </tr>
    <tr>
        <td>spring-boot-starter-social-facebook</td>
        <td>支持spring-social-facebook</td>
    </tr>
    <tr>
        <td>spring-boot-starter-social-linkedin</td>
        <td>支持spring-social-linkedin</td>
    </tr>
    <tr>
        <td>spring-boot-starter-social-twitter</td>
        <td>支持spring-social-twitter</td>
    </tr>
    <tr>
        <td>spring-boot-starter-test</td>
        <td>支持常用的测试依赖，包括JUnit、Hamcrest和Mockito还有spring-test模块</td>
    </tr>
    <tr>
        <td>spring-boot-starter-thymeleaf</td>
        <td>支持Thymeleaf模版引擎，包括Spring的集成</td>
    </tr>
    <tr>
        <td>spring-boot-starter-velocity</td>
        <td>支持Velocity模版引擎</td>
    </tr>
    <tr>
        <td>spring-boot-starter-web</td>
        <td>支持web的全栈开发，包括Tomcat和spring-webmvc</td>
    </tr>
    <tr>
        <td>spring-boot-starter-websocket</td>
        <td>支持WebSocket开发</td>
    </tr>
    <tr>
        <td>spring-boot-starter-ws</td>
        <td>支持Spring Web Services</td>
    </tr>
</table>

以下这些额外的应用starters，可用于增加[生产准备](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready)特性。

**Table 13.2. Spring Boot 生产准备starters**

<table>
    <tr>
        <td>名称</td>
        <td>描述</td>
    </tr>
    <tr>
        <td>spring-boot-starter-actuator</td>
        <td>增加生产准备特性，比如指标和监控</td>
    </tr>
    <tr>
        <td>spring-boot-starter-remote-shell</td>
        <td>增加远程ssh shell支持</td>
    </tr>
</table>

最后，如果你想要排除或者交换一些特定的技术面，Spring Boot也包括了这些starters供你使用。

<table>
    <tr>
        <td>名称</td>
        <td>描述</td>
    </tr>
    <tr>
        <td>spring-boot-starter-jetty</td>
        <td>导入Jetty HTTP引擎（通常作为Tomcat的代替）</td>
    </tr>
    <tr>
        <td>spring-boot-starter-log4j</td>
        <td>支持Log4J日志框架</td>
    </tr>
    <tr>
        <td>spring-boot-starter-logging</td>
        <td>导入Spring Boot默认的日志框架(Logback)</td>
    </tr>
    <tr>
        <td>spring-boot-starter-tomcat</td>
        <td>导入Spring Boot默认的HTTP引擎(Tomcat)</td>
    </tr>
    <tr>
        <td>spring-boot-starter-undertow</td>
        <td>导入Undertow HTTP引擎(通常作为Tomcat的代替)</td>
    </tr>
</table>

>对于社区贡献的额外的starter POM，在GitHub上**spring-boot-starters**模块查看[README文件](http://github.com/spring-projects/spring-boot/tree/master/spring-boot-starters/README.adoc)

## 14.组织你的代码

Spring Boot不需要特定的代码结构，但是还是有些有用的最佳实践。

### 14.1.使用“default”包

一个类不包含**package**声明，它被认为是在“default package”中。通常“default package”是不建议使用的，而且应该避免。因为每个jar的每个类都要被读取，所以对于使用@ComponentScan、@EntityScan或者@SpringBootApplication注解，它可能导致一些问题。

>我们建议你遵循Java建议的包命名规范，使用反转的域名（比如com.example.project）

### 14.2.定位main应用类

我们通常建议把main应用类放到其他类之上的根包中。**@EnableAutoConfiguration**注解经常放在main类中，它为某些项目隐式的定义了一个基本“search package”。例如，你正在写一个JPA应用，**@EnableAutoConfiguration**注解类所在的包被用于搜索**@Entity**项目。

使用根包允许你使用**@ComponentScan**注解而不需要指定**basePackage**属性。如果你的main类在根包，你也可以使用**@SpringBootApplication**注解。

下面是一个典型的项目结构：

    com
     +- example
         +- myproject
             +- Application.java
             |
             +- domain
             |   +- Customer.java
             |   +- CustomerRepository.java
             |
             +- service
             |   +- CustomerService.java
             |
             +- web
                 +- CustomerController.java

**Application.java**文件声明了**main**方法，还有基本的**@Configuration**注解。
<pre class="prettyprint">
    package com.example.myproject;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
    import org.springframework.context.annotation.ComponentScan;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    @EnableAutoConfiguration
    @ComponentScan
    public class Application {

        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

    }
</pre>

## 15.Configuration类

Spring Boot喜欢基于Java的配置。虽然可以配置XML源并调用SpringApplication.run()，但是建议你以使用**@Configuration**配置源为主。通常，对于定义**main**方法的类，**@Configuration**也是一个很好的选择。

>网上已经有很多使用XML作为Spring配置的例子。尽可能的尝试使用等效的基于Java的配置。搜索**enable\***注解是一个好的开始。

### 15.1.导入额外的配置类

你不需要把所有的**@Configuration**放到一个类中。**@Import**注解可以导入额外的配置类。或者，你可以使用**@ComponentScan**注解自动收集所有的Spring组件，包括**@Configuration**类。

### 15.2.导入XML配置

如果你必须使用基于XML的配置，建议你仍然以**@Configuration**类开始。你可以使用**@ImportResource**注解装载额外的XML配置文件。

## 16.Auto-configuration

Spring的Boot auto-configuration尝试根据你加入的jar包依赖自动配置你的Spring应用。例如，如果**HSQLDB**在你的classpath上，你不需要手动的配置数据库连接bean，Spring会自动配置一个内存数据库。

把**@EnableAutoConfiguration**或者**@SpringBootApplication**注解加到你的**@Configuration**类上，就实现了自动装配。

>你应该只需赠加**@EnableAutoConfiguration**注解，我们建议你把它加到主**@Configuration**类上。

### 16.1.平滑替换auto-configuration

Auto-configuration是非入侵的，任何时候你都可以用你自己的配置替换自动配置的某个部分。例如，如果你加入了**DataSource**bean，默认的内嵌数据库支持会被移除。

如果你需要找出当前应用有哪些自动配置，以及原因，你可以在运行应用时使用**--debug**。自动配置报告会被记录到控制台上。

### 16.2.禁用特定的自动配置

如果你不想使用某些特定的自动配置，你可以使用**@EnableAutoConfiguration**的exclude禁用它们。
<pre class="prettyprint">
    import org.springframework.boot.autoconfigure.*;
    import org.springframework.boot.autoconfigure.jdbc.*;
    import org.springframework.context.annotation.*;

    @Configuration
    @EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
    public class MyConfiguration {
    }
</pre>

如果类没在classpath中，你可以使用**excludeName**属性指定全名。最后，你还可以通过**spring.autoconfigure.exclude**property控制自动配置类的排除列表。

>你可以在注解等级或者使用property来定义排除。

##　17.Spring Beans和依赖注入

你可以自由使用标准Spring Framework技术定义bean和依赖注入。为了简单起见，我们经常使用**@ComponentScan**来发现bean，并结合**@Autowired**构造器注入来更好的使用。

如果你按照建议（把application放在根包）组织你的代码，你可以添加不带参数的**@ComponentScan**。你的应用的所有组件（**@Component**, **@Service**, **@Repository**, **@Controller**等等）会自动注册为Spring Beans。

下面是一个**@Service**Bean的例子，使用构造器注入取得需要的**RiskAssessor**bean。
<pre class="prettyprint">
    package com.example.service;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;

    @Service
    public class DatabaseAccountService implements AccountService {

        private final RiskAssessor riskAssessor;

        @Autowired
        public DatabaseAccountService(RiskAssessor riskAssessor) {
            this.riskAssessor = riskAssessor;
        }

        // ...

    }
</pre>

>就像上边的代码，允许*riskAssessor*标记为*final*，表明之后不能被修改。

## 18.使用@SpringBootApplication注解

很多Spring Boot开发者经常使用*@Configuration*, *@EnableAutoConfiguration*和 *@ComponentScan*来注解main类。

