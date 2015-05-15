# Spring MVC
tags: spring

---
## Spring MVC ���

Spring MVC ��Spring Framework ��һ���֣��ǻ���Java ����������������-��Ӧģ�ͣ���Web ��ܣ����ڴ���MVC �ܹ���Web Ӧ�ó��򣬼򻯱��빤����

## DispatcherServlet ��������

1. ���յ�һ��HTTP �����DispatcherServlet ʹ��HandlerMapping ���������Ӧ��Controller ���󣨺ͷ�������
2. Controller ���ö�Ӧ�ķ�����ִ����ص�ҵ���߼�����������ģ�ͣ�model������ͼ����view name����
3. ViewResolver ͨ����ͼ���ҵ���Ӧ����ͼ�ļ���jsp���������ظ���ͼ��
4. ʹ������ģ�ͣ�model����Ⱦ��ͼ��������ͼ��

##spring mvc ����

### 1��web.xml

WebContent/WEB-INF/web.xml ����spring mvc �� DispatcherServlet��
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
DispatcherServlet Ĭ�ϴ� WebContent/WEB-INF/[servlet-name]-servlet.xml ��ȡӦ�����ã����ӵ������ļ�Ϊ example-servlet.xml��Ҳ����ʹ�ò���ָ�������ļ�·����
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

###2��[servlet-name]-servlet.xml

example-servlet.xml ����ʾ����
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

* `<mvc:annotation-driven />`������ע�������� MVC ģ�͵�֧�֣��ṩ`@Controller`, `@RequestMapping` ��ע���֧�֡�
* `<context:component-scan...>`�涨Spring ɨ��İ���ɨ��`@Controller`, `@Service`, `@Component` ��ע�⣬������Щ�ཻ��Spring IoC ��������
* `InternalResourceViewResolver` ���ڽ�����ͼ���ƣ�������ͼ�� helllo ��Ӧ /WEB-INF/jsp/hello.jsp


---
�ο����ϣ�
[Spring MVC Framework Tutorial](http://www.tutorialspoint.com/spring/spring_web_mvc_framework.htm)
[17. Web MVC framework](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/mvc.html)
