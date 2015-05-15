#Spring IoC Containers
tags: spring

---
##什么是控制反转

控制反转（Inversion of Control，IoC）是Spring bean 容器的核心功能，就是由spring 负责控制对象的生命周期和和对象间的关系。传统的程序开发中，如果一个对象要使用另一个对象，就必须new 一个，使用完后还要销毁对象（比如数据库连接）。现在把对象的创建、销毁的控制权移交给Spring，所以称为控制反转。
控制反转也成为依赖注入（Dependency Injection），要使用一个对象时，由Spring 注入该对象，无需由代码控制。
控制反转的核心思想：***资源不由使用资源的双方管理，而由不使用资源的第三方管理。***这种思想在生活中比比皆是，支付宝就是一个庞大的IoC 容器，作为交易双方的第三方，提供可信赖、灵活的交易方案。


##IoC 配置

Spring bean 配置的基本结构：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->

</beans>
```

在xml 里配置由spring 管理的对象，需要使用时，通过读取配置文件，获取spring context（上下文环境），进而获得该对象。

##为什么使用IoC


* 由Spring 管理对象的生命周期，大大简化了我们的编码工作。
* 资源集中管理，实现资源的可配置和易管理。
* 降低了使用资源双方的依赖程度，即耦合度。


---
参考资料：
[Spring IoC有什么好处呢？ - 知乎](http://www.zhihu.com/question/23277575)
[5. The IoC container](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/beans.html)