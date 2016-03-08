如果你刚刚准备使用Spring Boot或者Spring，这部分是为你准备的。在这我们回答基本的“what”，“how”和“why”。你会看到一个很好的Spring Boot安装说明。我们将构建第一个Spring Boot应用，并讨论一些核心原则。

## 8.Spring Boot介绍

Spring Boot使创基于Spring的独立的、生产级的应用变得简单，你可以“just run”。我们对于Spring平台和第三方库已经有了固定的看法，所以一开始你可能会觉得有点奇怪。大多数的Spring Boot应用只需要很少的配置。

你可以使用Spring Boot创建能够使用java -jar运行的Java应用，或者更传统的war部署。我们也提供了运行“spring scripts”的命令行工具。

我们主要目标：

- 为所有Spring开发提供一个更快速、更广泛地易于开始的经验。
- 固执地开箱即用，但是避免了快速需求偏离默认值。
- 提供一系列为大多数项目共用的非功能性特性（例如embedded servers, security, metrics, health checks, externalized configuration）
- 绝对没有代码生成而且不需要XML配置。

## 9.系统需求

默认情况，Spring Boot 1.3.1.RELEASE需要Java 7和Spring Framework 4.1.5及以上。你可以使用Java 6，但是需要一些额外配置。详情见[Section 79.9, “How to use Java 6”](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#howto-use-java-6)。对于 Maven (3.2+)和Gradle (1.12+)提供支持。

> 虽然你可能使用Java 6或者7，但是如果可能的话，更推荐使用Java 8.

###　9.1.Servlet容器

以下嵌入Servlet容器支持开箱即用：<table>
<tbody>
<tr><td>Servlet容器名称&nbsp;&nbsp;&nbsp;&nbsp;</td><td>Servlet版本&nbsp;&nbsp;&nbsp;&nbsp;</td><td>Java版本</td></tr>
<tr><td>Tomcat 8</td><td>3.1</td><td>Java 7+</td></tr>
<tr><td>Tomcat 7</td><td>3.0</td><td>Java 6+</td></tr>
<tr><td>Jetty 9</td><td>3.1</td><td>Java 7+</td></tr>
<tr><td>Jetty 8</td><td>3.0</td><td>Java 6+</td></tr>
<tr><td>Undertow 1.1</td><td>3.1</td><td>Java 7+</td></tr>
</tbody>
</table>

你也可以部署Spring Boot到任何Servlet 3.0+兼容的容器。

## 10.安装Spring Boot

Spring Boot可以用经典的Java开发工具或者作为命令行工具安装。无论如何，你需要Java SDK v1.6或者更高。在你开始之前，你应该检查下你当前的Java版本：

    $ java -version

如果你是Java开发新手，或者你只想体验Spring Boot，你可以尝试[Spring Boot CLI](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-installing-the-cli)，否则，请继续阅读经典安装说明。

>虽然Spring Boot兼容Java 1.6，如果可能，你应该考虑使用最新的Java版本。

### 10.1.Java开发人员的安装介绍

你可以使用其他Java类库那样使用Spring Boot，在你的classpath下包含spring-boot-*.jar文件。Spring Boot不需要其他特殊的集成工具，所以你可以使用任何IDE或者文本编辑器；Spring Boot没有特殊的地方，你可以像其他Java程序那样运行或者调试。

虽然你可以复制Spring Boot jar，但是我们建议你使用依赖管理工具（比如Maven或者Gradle）。

#### 10.1.1.Maven安装

Spring Boot兼容Apache Maven 3.2或者更高版本。如果你没有安装Maven，你可以看这个介绍[ maven.apache.org](http://maven.apache.org/)。

>在大多数操作系统中，Maven可以通过包管理工具安装。如果你是 OSX Homebrew用户，使用**brew install maven**。Ubuntu用户可以运行**sudo apt-get install maven**。

Spring Boot依赖使用**org.springframework.boot groupId**。通常你的Maven pom文件将继承spring-boot-starter-parent项目，声明依赖一个或者多个 [“Starter POMs”](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter-poms)。Spring Boot也提供一个可选的[Maven plugin](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#build-tool-plugins-maven-plugin)来创建可执行的jar。

以下是典型的pom.xml文件：

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>com.example</groupId>
        <artifactId>myproject</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <!-- Inherit defaults from Spring Boot -->
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>1.3.1.RELEASE</version>
        </parent>
        <!-- Add typical dependencies for a web application -->
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
        </dependencies>
        <!-- Package as an executable jar -->
        &<build>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                </plugin>
            </plugins>
        </build>
    </project>

>**spring-boot-starter-parent**是一个很好的方式，但是可能是不适合所有的。有时你需要继承一个不同的父pom，或者你不喜欢默认设置。查看[Section 13.2.2, “Using Spring Boot without the parent POM](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-maven-without-a-parent)。

####10.1.2.Gradle安装

Spring Boot兼容Gradle 1.12或者更高版本。如果你没有安装Gradle，你可以查看介绍[www.gradle.org](http://www.gradle.org/)。

Spring Boot依赖可以使用**org.springframework.boot group**声明。通常，你的项目将声明一个或多个[“Starter POMs”](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter-poms)。Spring Boot提供一个有用的[Gradle plugin](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#build-tool-plugins-gradle-plugin)，可以用于简化依赖声明、创建可执行的jar。

>**Gradle Wrapper:** Gradle Wrapper提供一个很好的方式“获取”Gradle当你需要构建项目时。它是一个小的script和库，你可以一起提交代码来启动构建过程。详情见： [www.gradle.org/docs/current/userguide/gradle_wrapper.html](http://www.gradle.org/docs/current/userguide/gradle_wrapper.html)。

这是一个典型的build.gradle文件：

    buildscript {
        repositories {
            jcenter()
            maven { url "http://repo.spring.io/snapshot" }
            maven { url "http://repo.spring.io/milestone" }
        }
        dependencies {
            classpath("org.springframework.boot:spring-boot-gradle-plugin:1.3.1.RELEASE")
        }
    }

    apply plugin: 'java'
    apply plugin: 'spring-boot'

    jar {
        baseName = 'myproject'
        version =  '0.0.1-SNAPSHOT'
    }

    repositories {
        jcenter()
        maven { url "http://repo.spring.io/snapshot" }
        maven { url "http://repo.spring.io/milestone" }
    }

    dependencies {
        compile("org.springframework.boot:spring-boot-starter-web")
        testCompile("org.springframework.boot:spring-boot-starter-test")
    }

### 10.2.安装Spring Boot CLI

Spring Boot CLI是一个命令行工具，如果你想要快速构建Spring。它允许你运行[Groovy](http://groovy.codehaus.org/)，这意味这你有一个类Java语法的，没有太多样板代码。

在Spring Boot中CLI不是必须的，但是它确实是最快的方法得到一个Spring应用程序。

#### 10.2.1.安装手册

你可以从Spring软件仓库下载Spring CLI：

- [spring-boot-cli-1.3.1.RELEASE-bin.zip](http://repo.spring.io/release/org/springframework/boot/spring-boot-cli/1.3.1.RELEASE/spring-boot-cli-1.3.1.RELEASE-bin.zip)
- [spring-boot-cli-1.3.1.RELEASE-bin.tar.gz](http://repo.spring.io/release/org/springframework/boot/spring-boot-cli/1.3.1.RELEASE/spring-boot-cli-1.3.1.RELEASE-bin.tar.gz)

[snapshot distributions](http://repo.spring.io/snapshot/org/springframework/boot/spring-boot-cli/)也是可以使用的。

下载完成后，按照[INSTALL.txt](http://raw.github.com/spring-projects/spring-boot/v1.3.1.RELEASE/spring-boot-cli/src/main/content/INSTALL.txt)文件：总之，在**.zip**文件中的**bin/**目录有**spring**script（Windows中spring.bat）。或者，你可以使用**java -jar**和**。jar**文件（script会帮你确定classpath设置是否正确）。

#### 10.2.2.使用SDKMAN安装

SDKMAN!（软件开发工具管理器）可以用于管理不同二进制SDK的多个版本，包括Groovy和Spring Boot CLI。

从[sdkman.io](http://sdkman.io/)下载SDKMAN!，用以下命令安装：

    $ sdk install springboot
    $ spring --version
    Spring Boot v1.3.1.RELEASE

如果你正在为CLI开发特性，并且想要快速访问刚才建立的版本，按照下面的额外介绍

    $ sdk install springboot dev /path/to/spring-boot/spring-boot-cli/target/spring-boot-cli-1.3.1.RELEASE-bin/spring-1.3.1.RELEASE/
    $ sdk default springboot dev
    $ spring --version
    Spring CLI v1.3.1.RELEASE

这样会安装一个叫做**dev**实例的本地spring实例。它指向目标构建路径，所以当你每次重新构建Spring Boot，spring将变为最新的。

你可以这样做：

    $ sdk ls springboot

    ================================================================================
    Available Springboot Versions
    ================================================================================
    > + dev
    * 1.3.1.RELEASE

    ================================================================================
    + - local version
    * - installed
    > - currently in use
    ================================================================================

#### 10.2.3.OSX Homebrew安装

如果你使用Mac和[Homebrew](http://brew.sh/)，你只需要以下操作来安装Spring Boot CLI：

    $ brew tap pivotal/tap
    $ brew install springboot

Homebrew 将安装**spring**至**/usr/local/bin**。

>如果没有看到，可能是因为你安装的brew 过时了，请执行** brew update**然后重试。

#### 10.2.4.MacPorts安装

如果你使用Mac和[MacPorts](http://www.macports.org/)，你只需要以下操作来安装Spring Boot CLI：

	$ sudo port install spring-boot-cli

#### 10.2.5.命令行补全

Spring Boot CLI附带提供对[bash](http://en.wikipedia.org/wiki/Bash_%28Unix_shell%29)和[zsh](http://en.wikipedia.org/wiki/Zsh)命令补全。你可以在shell中**source**（也被叫做spring），或者把它放进你自己的或者系统的bash补全初始化。在Debian系统中，系统级scripts在**/shell-completion/bash**中，当一个新的shell启动，这个目录下的所有的script都会自动执行。例如，如果你已经使用SDKMAN安装：

    $ . ~/.sdkman/springboot/current/shell-completion/bash/spring
    $ spring <HIT TAB HERE>
      grab  help  jar  run  test  version

>如果你使用Homebrew或者MacPorts安装Spring Boot CLI，命令行补全scripts会自动注册到shell。

#### 10.2.6.快速启动Spring CLI例子

这是一个很简单的web应用，可以测试你的安装。创建名为app.groovy的文件。

    @RestController
    class ThisWillActuallyRun {
        @RequestMapping("/")
        String home() {
            "Hello World!"
        }
    }

接下来在shell中运行：

	$ spring run app.groovy

>首次运行应用时需要下载依赖，所以会花费些时间，随后的运行会更快。

在浏览器中打开localhost:8080，你会看到以下输出：

    Hello World!

### 10.3.从Spring Boot的早期版本升级

如果你打算从早期升级，请检查托管在[project wiki](http://github.com/spring-projects/spring-boot/wiki)上的“release notes”。你会看到升级说明中每一个发布版本的都带有“值得关注”的新特性。

如果打算升级存在的CLI安装，请使用适当的包管理命令（例如brew upgrade）或者，如果你是手动安装的CLI，请根据[标准说明](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-manual-cli-installation)安装，并记得更新你的环境变量，移除旧的引用。

## 11.开发你的第一个Spring Boot应用

让我们开发一个简单的“Hello World”web应用来强调一些Spring Boot的关键特性。由于大部分IDE都支持Maven，我们使用Maven构建项目。

>[spring.io](http://spring.io/)网站上包含了很多使用Spring Boot的“Getting Started”，如果你想解决一些特定的问题，请先看下这里。

在开始之前，打开终端检查Java版本和Maven版本是否可用。

    $ java -version
    java version "1.7.0_51"
    Java(TM) SE Runtime Environment (build 1.7.0_51-b13)
    Java HotSpot(TM) 64-Bit Server VM (build 24.51-b03, mixed mode)

    $ mvn -v
    Apache Maven 3.2.3 (33f8c3e1027c3ddde99d3cdebad2656a31e8fdf4; 2014-08-11T13:58:10-07:00)
    Maven home: /Users/user/tools/apache-maven-3.1.1
    Java version: 1.7.0_51, vendor: Oracle Corporation

>这个例子需要创建文件夹。然后假设你已经创建了文件夹，并且它已经是当前目录。

### 11.1.创建POM

我们从创建pom.xml文件开始。pom.xml是构建项目的关键。打开文本编辑器，添加：

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>com.example</groupId>
        <artifactId>myproject</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>1.3.1.RELEASE</version>
        </parent>
        <!-- Additional lines to be added here... -->
    </project>

这是一个可用的构建，你可以运行**mvn package**来测试（你可以忽略空jar--标记为没有内容）。

>现在你可以把项目导入IDE（大多数Java IDE内建了Maven支持）。简单起见，我们继续使用文本编辑器。

### 11.2.增加classpath依赖

Spring Boot提供了很多“Starter POMs”，这使得将jar加到classpath变得简单。我们的示例应用已经使用了**spring-boot-starter-parent**在POM的**parent**部分。**spring-boot-starter-parent**是一个特殊的starter ，提供了默认的Maven设置。它还提供了一个[**dependency-management**](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-dependency-management)部分，对于“blessed”的依赖你可以省略**version**标签。

当你开发一个特定类型的应用的时候，其他“Starter POMs”提供了你可能需要的依赖。由于我们正在开发一个web应用，我们将增加**spring-boot-starter-web**依赖--但是在这之前，我们看看现在我们有的。

    $ mvn dependency:tree
    [INFO] com.example:myproject:jar:0.0.1-SNAPSHOT

**mvn dependency:tree**命令输出你项目的树形依赖关系。你可以看到**spring-boot-starter-parent**自身并没有提供依赖。接下来，我们编辑pom.xml，增加**spring-boot-starter-web**依赖在**parent**部分。

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

再次运行**mvn dependency:tree**，现在你会看见很多额外的依赖，包括Tomcat web服务器和Spring Boot自身。

###　11.3.编写代码

为了完成我们的应用，我们需要创建一个Java文件。Maven将默认的从**src/main/java**编译源码，所以，你需要创建这个结构的文件夹，然后创建**src/main/java/Example.java**文件。

    import org.springframework.boot.*;
    import org.springframework.boot.autoconfigure.*;
    import org.springframework.stereotype.*;
    import org.springframework.web.bind.annotation.*;
    @RestController
    @EnableAutoConfiguration
    public class Example {
        @RequestMapping("/")
        String home() {
            return "Hello World!";
        }
        public static void main(String[] args) throws Exception {
            SpringApplication.run(Example.class, args);
        }
    }

虽然没有很多代码。接下来噩梦开始重要的部分。

#### 11.3.1.@RestController和@RequestMapping annotations注解

我们例子中的第一个注解是**@RestController**。这是一个*stereotype*注解。它为阅读代码提供提示，在Spring中，它扮演着重要的角色。我们的类是一个web的**@Controller**所以当处理进来的web请求时Spring会考虑到它。

@RequestMapping注解提供“路由”信息。它告诉Spring请求路径“/”的HTTP请求应该映射到**home**方法。**@RestController**注解告诉Spring渲染结果字符串直接返回给调用者。

#### 11.3.2.EnableAutoConfiguration注解

第二个类级的注解是@EnableAutoConfiguration。它告诉Spring Boot基于你添加的依赖去“猜测”你怎样去配置Spring。由于**spring-boot-starter-web**增加了Tomcat和Spring MVC，自动配置会假设你正在开发web应用，并一此配置Spring。

>**Starter POMs和Auto-Configuration**
>Auto-configuration被设计成和“Starter POMs”协同工作，但是这两个概念并不是直接相关的。你可以自由的选择starter POMs之外的依赖，Spring Boot仍然能够很好的自动配置你的应用。

#### 11.3.3."main"方法

应用的最终部分是main方法。这是一个遵守Java中对于应用入口的约定的标准方法。我们的main方法通过调用**run**委托给了Spring Boot的SpringApplication类。SpringApplication将引导我们的应用，依次启动自动装配的Tomcat web服务器来启动Spring。我们需要把**Example.class**作为参数**run**方法来告诉**SpringApplication**哪个是主要的Spring组件。**args**数组也可以传递暴露给任何命令行参数

### 11.4.运行例子程序

此时，应用已经可以运行了。因为我们使用了**spring-boot-starter-parent**的POM，我们有了一个好用的**run**命令来启动应用。在项目根目录键入**mvn spring-boot:run**命令启动应用。

    $ mvn spring-boot:run
      .   ____          _            __ _ _
     /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
     \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
      '  |____| .__|_| |_|_| |_\__, | / / / /
     =========|_|==============|___/=/_/_/_/
     :: Spring Boot ::  (v1.3.1.RELEASE)
    ....... . . .
    ....... . . . (log output here)
    ....... . . .
    ........ Started Example in 2.222 seconds (JVM running for 6.514)

当你浏览器打开[localhost:8080](http://localhost:8080/)你应该能看到如下输出：

    Hello World!

键入**ctrl-c**温雅的退出应用。

## 11.5.创建可执行的jar

让我们通过创建一个可以运行在生产环境的完全自包含的可执行jar来结束我们的例子。可执行的jar（有时被叫做“fat jars”）把你编译的类和代码运行所依赖的jar都归档到一起。

>**可执行的jar与Java**
>Java不提供任何标准方式来加载内嵌的jar文件（例如jar文件本身包含在jar文件中）。如果你想发布一个自包含的应用，可能会遇到些问题。
>为了解决这个问题，开发者使用“uber”jars。一个uber jar简单地将所有jar文件的类打包。这种方式的问题是在你的应用中你很难你使用使用的类库。当不同的jar中相同的文件名被使用的时候（内容不同）它同样有问题。
>Spring Boot使用了[不同的方式](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#executable-jar)允许你直接内嵌jar。

要创建一个可执行的jar我们需要加入**spring-boot-maven-plugin**到**pom.xml**中。在**dependencies**部分的下面输入以下几行：

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

>**spring-boot-starter-parent** POM包含**&lt;executions>**配置来绑定**repackage**命令。如果你不使用parent POM，你可以自己声明这个配置，详情见：[plugin documentation](http://docs.spring.io/spring-boot/docs/1.3.1.RELEASE/maven-plugin/usage.html)。

保存你的**pom.xml**，运行**mvn package**：

    $ mvn package
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------------------------------------------------------------
    [INFO] Building myproject 0.0.1-SNAPSHOT
    [INFO] ------------------------------------------------------------------------
    [INFO] .... ..
    [INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ myproject ---
    [INFO] Building jar: /Users/developer/example/spring-boot-example/target/myproject-0.0.1-SNAPSHOT.jar
    [INFO]
    [INFO] --- spring-boot-maven-plugin:1.3.1.RELEASE:repackage (default) @ myproject ---
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------

查看**target**目录，你会看见**myproject-0.0.1-SNAPSHOT.jar**。这个文件大概10M。如果你想查看内部，你可以使用**jar tvf**：

	$ jar tvf target/myproject-0.0.1-SNAPSHOT.jar

你看看到**target**目录里有很多名字是**myproject-0.0.1-SNAPSHOT.jar.original**的小文件。这是Spring Boot重新打包前Maven创建的原始jar文件。

使用**java -jar**命令运行应用：

    $ java -jar target/myproject-0.0.1-SNAPSHOT.jar
      .   ____          _            __ _ _
     /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
     \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
      '  |____| .__|_| |_|_| |_\__, | / / / /
     =========|_|==============|___/=/_/_/_/
     :: Spring Boot ::  (v1.3.1.RELEASE)
    ....... . . .
    ....... . . . (log output here)
    ....... . . .
    ........ Started Example in 2.536 seconds (JVM running for 2.864)

像之前一样，使用**ctrl-c**温雅的退出应用。

## 12.接下来读什么

希望这部分给你带来了Spring基础知识，让你可以写自己的应用。如果你是一个面向任务型的开发人员，你可能打算跳过[spring.io](http://spring.io/)，迁出一些解决“我该怎么用Spring”问题的[入门指南](http://spring.io/guides/)；我们也提供了Spring Boot的[How-to](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#howto)的参考文档。

[Spring Boot Repository](http://github.com/spring-projects/spring-boot)中有一些可运行的[例子](http://github.com/spring-projects/spring-boot/tree/v1.3.1.RELEASE/spring-boot-samples)。这些例子都是独立的（你不需要构建其他东西来运行或者使用例子）。

另外，下一个逻辑步骤是阅读第三部分，[“使用Spring Boot”](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot)。如果你没有耐性，也可以跳过这些，阅读[Spring boot特性](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features)。


