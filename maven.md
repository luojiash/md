# Apache Maven

## 什么是 Apache Maven

Maven 是一个软件项目管理和构建工具，由 Apache 软件基金会开发。

## Maven 如何工作

### 目录结构

Maven 项目的默认目录结构：
| 目录 | 目的 |
| - | - |
|${basedir} | 存放 pom.xml 和所有子目录 |
|${basedir}/src/main/java | Java 源代码 |
|${basedir}/src/main/resources | 资源文件，比如 property 文件 |
|${basedir}/src/test/java | 测试代码 |
|${basedir}/src/test/resources | 测试资源文件 |

Maven 项目会生成 .class 文件，存放在${basedir}/target/classes. 

### pom.xml
Maven 项目使用项目对象模型（Project Object Model, POM）来配置，POM 存储在 pom.xml 文件中，这个文件用于描述项目信息、管理依赖关系、配置插件。

一个 pom.xml 的例子：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.8.2</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

### 依赖范围

Maven 根据依赖关系`<dependencies>`从 maven 库下载程序（比如Jar 包），Windows 的下载路径是%UserProfile%\.m2\repository. 
`<scope>` 决定了依赖关系的适用范围，例子里的`test`表示这个依赖关系只有在执行
compiler:testCompile和surefire:test目标时才会被加到classpath里，在执行compile:compile
目标时是拿不到 junit的。
`<scope>`为`provided`表示JDK 或容器会提供所需的 jar 包，比如web 应用开发中，编译时需要 servlet-api, 但在打包时不需要加到 war 包里，因为 servlet 容器会提供。
`scope`的默认值是`compile`, 即任何时候都会包含在 classpath 里，打包时也会包含进去。

| scope | 编译期 | 测试期 | 运行期 | 说明 |
| - | - | - | - | - |
| compile | V | V | V | 默认 |
| test    |   | V |   | JUnit |
| provided| V | V |   | servlet-api |
| runtime |   | V | V | JDBC驱动实现 |
| system  | V | V |   | 本地的，Maven仓库之外的类库文件 |

> scope:
This element refers to the classpath of the task at hand (compiling and runtime, testing, etc.) as well as how to limit the transitivity of a dependency. There are five scopes available:
>
* compile - this is the default scope, used if none is specified. Compile dependencies are available in all classpaths. Furthermore, those dependencies are propagated to dependent projects.
* provided - this is much like compile, but indicates you expect the JDK or a container to provide it at runtime. It is only available on the compilation and test classpath, and is not transitive.
* runtime - this scope indicates that the dependency is not required for compilation, but is for execution. It is in the runtime and test classpaths, but not the compile classpath.
* test - this scope indicates that the dependency is not required for normal use of the application, and is only available for the test compilation and execution phases.
* system - this scope is similar to provided except that you have to provide the JAR which contains it explicitly. The artifact is always available and is not looked up in a repository.

*依赖范围影响传递性依赖*
A依赖于B，B依赖于C，A对B是第一直接依赖，B对C是第二直接依赖，A对C是传递性依赖。
下表第一列表示第一直接依赖范围，第一行表示第二直接依赖范围，中间表示传递性依赖。

|    -    | compile | test | provided | runtime |
| - | - | - | - | - |
| compile | compile | -    | -        | runtime |
| test    | test    | -    | -        | test |
| provided | provided | -  | provided | provided |
| runtime | runtime | -    | -        | runtime |

### 生命周期和插件目标

Maven有三套相互独立的生命周期，每套生命周期包含不同的阶段。clean生命周期的目的是
清理项目，default生命周期的目的是构建项目，site生命周期的目的是建立项目站点。

> clean: pre-clean, clean, post-clean
> default: validate, initialize, generate-sources, process-sources, generate-resources, process-resources, compile, process-classes,
  generate-test-sources, process-test-sources, generate-test-resources, process-test-resources,
  test-compile, process-test-classes, test, prepare-package, package, pre-integration-test,
  integration-test, post-integration-test, verify, install, deploy
> site: pre-site, site, post-site, site-deploy

### 插件目标

maven-dependency-plugin有十多个目标，每个目标对应一个功能。dependency:analyze，
冒号前面是插件前缀，冒号后面是插件目标。一个插件目标绑定生命周期的一个或多个阶段，
比如maven-clean-plugin:clean绑定clean生命周期的clean阶段。

## 安装Maven
下载Maven 压缩包，解压，设根目录为\${mavendir}, Path 环境变量添加 ${mavendir}\bin. 比如我添加了D:\apache-maven-3.1.1\bin. 
查看版本信息，出现以下结果说明安装成功了。

```
C:\Users\hp>mvn -v
Apache Maven 3.1.1 (0728685237757ffbf44136acec0402957f723d9a; 2013-09-17 23:22:22+0800)
Maven home: D:\apache-maven-3.1.1\bin\..
Java version: 1.7.0_75, vendor: Oracle Corporation
Java home: C:\Program Files\Java\jdk1.7.0_75\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 7", version: "6.1", arch: "amd64", family: "windows"
```

## 使用方法

- 使用eclipse插件
- 命令行

命令行执行Maven任务的主要方式是调用Maven的生命周期阶段。各个生命周期是相互独立的，
而一个生命周期的阶段是有相互依赖关系的。Maven的生命周期阶段和插件目标相互绑定，
由插件完成具体的构建任务。Maven在执行过程中逐步通过每个阶段，会执行该阶段绑定的
所有插件目标。如果多个插件目标绑定到同一个阶段，插件声明顺序决定了目标执行顺序。

$mvn clean install
该命令调用clean生命周期的clean阶段，和default生命周期的install阶段。实际执行的
阶段为clean声明周期的pre-clean、clean阶段，以及default生命周期从validate至install
的所有阶段。

---
参考资料：
[Maven – Maven in 5 Minutes](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)
[Maven – POM Reference](http://maven.apache.org/pom.html)
[Apache Maven 入门篇 ( 上 )](http://www.oracle.com/technetwork/cn/community/java/apache-maven-getting-started-1-406235-zhs.html)
[Apache Maven 入门篇 ( 下 )](http://www.oracle.com/technetwork/cn/community/java/apache-maven-getting-started-2-405568-zhs.html)
