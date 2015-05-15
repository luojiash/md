#Apache Maven
tags: maven

---
##什么是 Apache Maven
Maven 是一个软件项目管理和构建工具，由 Apache 软件基金会开发。

##Maven 如何工作
###目录结构
Maven 项目的默认目录结构：
| 目录 | 目的 |
| - | - |
|${basedir} | 存放 pom.xml 和所有子目录 |
|${basedir}/src/main/java | Java 源代码 |
|${basedir}/src/main/resources | 资源文件，比如 property 文件 |
|${basedir}/src/test/java | 测试代码 |
|${basedir}/src/test/resources | 测试资源文件 |

Maven 项目会生成 .class 文件，存放在${basedir}/target/classes. 

###pom.xml
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

Maven 根据依赖关系`<dependencies>`从 maven 库下载程序（比如Jar 包），Windows 的下载路径是%UserProfile%\.m2\repository. 
`<scope>` 决定了依赖关系的适用范围，例子里的`test`表示这个依赖关系只有在执行 compiler:testCompile and surefire:test 目标[^f1]时才会被加到 classpath 里，在执行 compile:compile 目标时是拿不到 junit的。
`<scope>`为`provided`表示JDK 或容器会提供所需的 jar 包，比如web 应用开发中，编译时需要 servlet-api, 但在打包时不需要加到 war 包里，因为 servlet 容器会提供。
`scope`的默认值是`compile`, 即任何时候都会包含在 classpath 里，打包时也会包含进去。

| scope | 编译期 | 测试期 | 运行期 | 说明 |
| - | - | - | - | - |
| compile | V | V | V | 默认 |
| test    |   | V |   | |
| provided| V | V |   | |
| runtime |   | V | V | |
| system  | V | V |   | |

> scope:
This element refers to the classpath of the task at hand (compiling and runtime, testing, etc.) as well as how to limit the transitivity of a dependency. There are five scopes available:
>
* compile - this is the default scope, used if none is specified. Compile dependencies are available in all classpaths. Furthermore, those dependencies are propagated to dependent projects.
* provided - this is much like compile, but indicates you expect the JDK or a container to provide it at runtime. It is only available on the compilation and test classpath, and is not transitive.
* runtime - this scope indicates that the dependency is not required for compilation, but is for execution. It is in the runtime and test classpaths, but not the compile classpath.
* test - this scope indicates that the dependency is not required for normal use of the application, and is only available for the test compilation and execution phases.
* system - this scope is similar to provided except that you have to provide the JAR which contains it explicitly. The artifact is always available and is not looked up in a repository.

##安装Maven
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

##如何使用
依靠 eclipse IDE。

---
参考资料：
[Maven – Maven in 5 Minutes](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)
[Maven – POM Reference](http://maven.apache.org/pom.html)
[Apache Maven 入门篇 ( 上 )](http://www.oracle.com/technetwork/cn/community/java/apache-maven-getting-started-1-406235-zhs.html)
[Apache Maven 入门篇 ( 下 )](http://www.oracle.com/technetwork/cn/community/java/apache-maven-getting-started-2-405568-zhs.html)

[^f1]: 不懂，和Maven 生命周期相关。