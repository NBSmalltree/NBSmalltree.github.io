---
layout: post
title: Maven基础知识
data: 2021-04-13
tags: Java
---

# Maven基础知识

## 一、介绍

### 1、定义

Maven是一个Java项目管理和构建工具，它可以定义项目结构、项目依赖，并使用统一的方式进行自动化构建。

> 一款JAVA的管理和构建工具
>
> 1、提供了一套标准化的项目结构；
>
> 2、提供了一套标准化的构建流程（编译，测试，打包，发布……）；
>
> 3、提供了一套依赖管理机制。

### 2、Maven项目结构

```ascii
a-maven-project 项目名
├── pom.xml 项目描述文件
├── src
│   ├── main
│   │   ├── java 源码
│   │   └── resources 资源文件
│   └── test
│       ├── java 测试源码
│       └── resources 测试资源
└── target 编译打包目录
```

### 3、pom文件

```xml
<project ...>
	<modelVersion>4.0.0</modelVersion>
	<groupId>cn.nbcb.maven</groupId>  包名，通常是公司或组织名称
	<artifactId>maven_study</artifactId>  Java类名，通常是项目名称
	<version>0.0.1-SNAPSHOT</version>  版本号
	<packaging>jar</packaging>
  <name>第一个maven工程</name>
  <description>第一个maven工程</description>
	<properties>
        ...
	</properties>
  <!-- 依赖包 -->
	<dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.0</version>
        </dependency>
	</dependencies>
  <build>
    <!-- 插件配置 -->
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
          <source>1.7</source>
          <target>1.7</target>
          <encoding>UTF-8</encoding>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

一个Maven工程就是由`groupId`，`artifactId`和`version`作为唯一标识。我们在引用其他第三方库的时候，也是通过这3个变量确定。

使用`<dependency>`声明一个依赖后，Maven就会自动下载这个依赖包并把它放到classpath中。

### 4、安装maven

一种去maven官网下载最新的maven，最新版本3.8.1

另一种直接使用idea自带maven，行内idea版本为2018.2.2，其maven版本为3.3.9

Windows下环境变量配置

```
MAVEN_HOME=IDEA目录\plugins\maven\lib\maven3
PATH=$PATH:$MAVEN_HOME\bin
```

运行：`maven -version`检查版本

## 二、依赖管理

### 1、解决依赖传递引入

针对于传递依赖的问题，maven能自动帮我们解决

```ascii
┌──────────────┐
│Sample Project│
└──────────────┘
        │
        ▼
┌──────────────┐
│     abc      │
└──────────────┘
        │
        ▼
┌──────────────┐
│     xyz      │
└──────────────┘
```

### 2、依赖关系

Maven定义了几种依赖关系，分别是`compile`、`test`、`runtime`和`provided`：

| scope    | 说明                                          | 示例            |
| :------- | :-------------------------------------------- | :-------------- |
| compile  | 编译时需要用到该jar包（默认）                 | commons-logging |
| test     | 编译Test时需要用到该jar包                     | junit           |
| runtime  | 编译时不需要，但运行时需要用到                | mysql           |
| provided | 编译时需要用到，但运行时由JDK或某个服务器提供 | servlet-api     |

其中，默认的`compile`是最常用的，Maven会把这种类型的依赖直接放入classpath。

`test`依赖表示仅在测试时使用，正常运行时并不需要。

`runtime`依赖表示编译时不需要，但运行时需要。

`provided`依赖表示编译时需要，但运行时不需要。

### 3、中央仓库

Maven下载包的地方，外网是repo1.maven.org

Maven并不会每次都从中央仓库下载jar包。一个jar包一旦被下载过，就会被Maven自动缓存在本地目录（用户主目录的`.m2`目录），所以，除了第一次编译时因为下载需要时间会比较慢，后续过程因为有本地缓存，并不会重复下载相同的jar包。

注：只有以`-SNAPSHOT`结尾的版本号会被Maven视为开发版本，开发版本每次都会重复下载，这种SNAPSHOT版本只能用于内部私有的Maven repo，公开发布的版本不允许出现SNAPSHOT。

### 4、配置镜像

在用户主目录下进入`.m2`目录，创建一个`settings.xml`配置文件，内容如下：

```xml
【外网】
<settings>
    <mirrors>
        <mirror>
            <id>aliyun</id>
            <name>aliyun</name>
            <mirrorOf>central</mirrorOf>
            <!-- 国内推荐阿里云的Maven镜像 -->
            <url>https://maven.aliyun.com/repository/central</url>
        </mirror>
    </mirrors>
</settings>
【内网】详见开发者门户maven源部分
<mirrors>
    <mirror>
        <id>central</id>
        <name>central</name>
        <mirrorOf>*</mirrorOf>
        <url>https://repo.nbcb.com/artifactory/virtual-maven</url>
    </mirror>
</mirrors>
```

配置镜像仓库后，Maven的下载速度就会非常快。

### 5、搜索第三方组件

外网方式：[search.maven.org](https://search.maven.org)

内网方式：.m2种setting.xml文件配置的网址http://repo.nbcb.com/artifactory/virtual-maven，树状图查看或搜索，没有更好的方法了？

### 6、命令行编译

在命令中，进入到`pom.xml`所在目录，输入以下命令：

```
$ mvn clean package
```

如果一切顺利，即可在`target`目录下获得编译后自动打包的jar。

## 三、构建流程

Maven通过lifecycle、phase和goal来提供标准的构建流程。

### 1、lifecycle 生命周期 & phase 阶段

Maven的生命周期由一系列阶段（phase）构成，以内置的生命周期`default`为例，它包含以下phase：

- validate
- initialize
- generate-sources
- process-sources
- generate-resources
- process-resources
- compile
- process-classes
- generate-test-sources
- process-test-sources
- generate-test-resources
- process-test-resources
- test-compile
- process-test-classes
- test
- prepare-package
- package
- pre-integration-test
- integration-test
- post-integration-test
- verify
- install
- deploy

如果我们运行`mvn package`，Maven就会执行`default`生命周期，它会从开始一直运行到`package`这个phase为止：

- validate
- ...
- package

Maven另一个常用的生命周期是`clean`，它会执行3个phase：

- pre-clean
- clean （注意这个clean不是lifecycle而是phase）
- post-clean

### 2、goal 目标

执行一个phase又会触发一个或多个goal：

| 执行的Phase | 对应执行的Goal                      |
| :---------- | :---------------------------------- |
| compile     | compiler:compile                    |
| test        | compiler:testCompile  surefire:test |

goal的命名总是`abc:xyz`这种形式。

- lifecycle相当于Java的package，它包含一个或多个phase；
- phase相当于Java的class，它包含一个或多个goal；
- goal相当于class的method，它其实才是真正干活的。

大多数情况，我们只要指定phase，就默认执行这些phase默认绑定的goal，只有少数情况，我们可以直接指定运行一个goal，例如，启动Tomcat服务器：

```
mvn tomcat:run
```

### 3、常用命令

最常用的构建命令是指定phase，然后让Maven执行到指定的phase：

- mvn clean
- mvn clean compile
- mvn clean test
- mvn clean package

通常情况，我们总是执行phase默认绑定的goal，因此不必指定goal。

## 四、使用插件

实际上，执行每个phase，都是通过某个插件（plugin）来执行的，Maven本身其实并不知道如何执行`compile`，它只是负责找到对应的`compiler`插件，然后执行默认的`compiler:compile`这个goal来完成编译。

所以，使用Maven，实际上就是配置好需要使用的插件，然后通过phase调用它们。

Maven已经内置了一些常用的标准插件：

| 插件名称 | 对应执行的phase |
| :------- | :-------------- |
| clean    | clean           |
| compiler | compile         |
| surefire | test            |
| jar      | package         |

## 五、模块管理

模块化管理，降低工程复杂度，下面通过一个例子来介绍

模块A：

```xml
<project...>
  <groupId>com.nbcb</groupId>
  <artifactId>module-a</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>
  
  <name>module-a</name>
  
  <properties>...</properties>
  
  <dependencies>...</dependencies>
</project>
```

模块B：

```xml
<project...>
  <groupId>com.nbcb</groupId>
  <artifactId>module-b</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>
  
  <name>module-b</name>
  
  <properties>...</properties>
  
  <dependencies>...</dependencies>
</project>
```

可以抽提出模块A和B的共同部分作为`parent`：

**主要是packaging字段从jar改成了pom**

```xml
<project...>
  <groupId>com.nbcb</groupId>
  <artifactId>parent</artifactId>
  <version>1.0</version>
  <packaging>pom</packaging>
  
  <name>parent</name>
  
  <properties>...</properties>
  
  <dependencies>...</dependencies>
</project>
```

此时模块A就可以简化为：

```xml
<project...>
  <artifactId>module-a</artifactId>
  <packaging>jar</packaging>
  <name>module-a</name>
  
  <parent>
    <groupId>com.nbcb</groupId>
    <artifactId>parent</artifactId>
    <version>1.0</version>
    <relativePath>../parent/pom.xml</relativePath>
  </parent>
</project>
```

如果模块A依赖模块B，则模块A需要模块B的jar包才能正常编译，我们需要在模块A中引入模块B：

```xml
    ...
    <dependencies>
        <dependency>
            <groupId>com.itranswarp.learnjava</groupId>
            <artifactId>module-b</artifactId>
            <version>1.0</version>
        </dependency>
    </dependencies>
```

最后，在编译的时候，需要在根目录创建一个`pom.xml`统一编译：

**可以通过`<modules>`编译多个模块**

```xml
<project ...>

    <modelVersion>4.0.0</modelVersion>
    <groupId>com.nbcb</groupId>
    <artifactId>build</artifactId>
    <version>1.0</version>
    <packaging>pom</packaging>
    <name>build</name>

    <modules>
        <module>parent</module>
        <module>module-a</module>
        <module>module-b</module>
        <module>module-c</module>
    </modules>
</project>
```

这样，在根目录执行`mvn clean package`时，Maven根据根目录的`pom.xml`找到包括`parent`在内的共4个`<module>`，一次性全部编译。

此时工程目录结构为：

```ascii
multiple-project
├── pom.xml
├── parent
│   └── pom.xml
├── module-a
│   ├── pom.xml
│   └── src
├── module-b
│   ├── pom.xml
│   └── src
└── module-c
    ├── pom.xml
    └── src
```

