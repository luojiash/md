# Spring MVC
tags: spring

---
## Spring MVC 简介

Spring MVC 是Spring Framework 的一部分，是基于Java 的请求驱动（请求-响应模型）的Web 框架，用于创建MVC 架构的Web 应用程序，简化编码工作。

## DispatcherServlet 工作流程

1. 接收到一个HTTP 请求后，DispatcherServlet 使用HandlerMapping 查找请求对应的Controller 对象（和方法）。
2. Controller 调用对应的方法，执行相关的业务逻辑，返回数据模型（model）和视图名（view name）。
3. ViewResolver 通过视图名找到对应的视图文件（jsp），并返回该视图。
4. 使用数据模型（model）渲染视图，返回视图。

##spring mvc 配置

### 1、web.xml

WebContent/WEB-INF/web.xml 配置spring mvc 的 DispatcherServlet。
```xml
<web-app>
    <servlet>
        <servlet-name>example</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>example</servlet-name>
        <url-pattern>/example/*</url-pattern>
    </servlet-mapping>

</web-app>
```
DispatcherServlet 默认从 WebContent/WEB-INF/[servlet-name]-servlet.xml 读取应用配置，例子的配置文件为 example-servlet.xml。也可以使用参数指定配置文件路径：
```xml
<servlet>
	<servlet-name>example</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:spring/dispatcherServlet-servlet.xml</param-value>
	</init-param>
	<load-on-startup>1</load-on-startup>
</servlet>
```

###2、[servlet-name]-servlet.xml

example-servlet.xml 配置示例：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

	<mvc:annotation-driven />
    <context:component-scan base-package="com.tutorialspoint" />
	
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

* `<mvc:annotation-driven />`声明对注解驱动的 MVC 模型的支持，提供`@Controller`, `@RequestMapping` 等注解的支持。
* `<context:component-scan...>`规定Spring 扫描的包，扫描`@Controller`, `@Service`, `@Component` 等注解，并把这些类交给Spring IoC 容器管理。
* `InternalResourceViewResolver` 用于解析视图名称，比如视图名 helllo 对应 /WEB-INF/jsp/hello.jsp


---
参考资料：
[Spring MVC Framework Tutorial](http://www.tutorialspoint.com/spring/spring_web_mvc_framework.htm)
[17. Web MVC framework](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/mvc.html)
