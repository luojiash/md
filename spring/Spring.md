# Spring

---
Spring使用声明式事务处理，默认情况下，如果被注解的数据库操作方法中发生了unchecked异常，所有的数据库操作将rollback；如果发生的异常是checked异常，默认情况下数据库操作还是会提交的。
`rollbackFor=Exception.class` 指定要回滚的异常类型
---
spring mvc @responsebody 中文乱码
解决方案：`@RequestMapping`加上`produces="text/plain;charset=UTF-8"`
```
@RequestMapping(value="/denied",produces="text/plain;charset=UTF-8")
```

---
```
conf.properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/onet?useUnicode=true&characterEncoding=UTF-8
jdbc.username=root
jdbc.password=root
jdbc.maxActive=64
jdbc.maxIdle=4
jdbc.validationQuery=select 1;
jdbc.testOnBorrow=true
jdbc.testWhileIdle=true
```
读取数据库配置文件的配置：`<context:property-placeholder location="conf/conf.properties"/>`
报错：
```
Failed to convert property value of type 'java.lang.String' to required type 'int' for property 'maxActive'; nested exception is java.lang.NumberFormatException: For input string: "${jdbc.maxActive}"
```

貌似是mybatis引起的错误
```
<bean id="onetSqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="configLocation" value="classpath:/mybatis/SqlMapConfig.xml"/>
	<property name="dataSource" ref="onetDataSource"></property>
</bean>
```
修改bean id 就可以了。。。原来的id是sqlSessionFactory

---
spring mvc + json 406 Not Acceptable 错误
加上json依赖解决
```
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.2.3</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.2.3</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
    <version>2.2.3</version>
</dependency>
```
或者以下依赖
```
<dependency>
	<groupId>org.codehaus.jackson</groupId>
	<artifactId>jackson-mapper-asl</artifactId>
	<version>1.9.13</version>
</dependency>
<dependency>
	<groupId>org.codehaus.jackson</groupId>
	<artifactId>jackson-core-asl</artifactId>
	<version>1.9.13</version>
</dependency>
```
两个是不同的json库
http://mvnrepository.com/open-source/json-libraries

---
解决乱码
tomcat 安装目录conf/server.xml
```
<Connector URIEncoding="UTF-8" connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443"/>
```

---
log4j 记录日志
```
log4j.properties:
log4j.appender.SURVEY = org.apache.log4j.FileAppender
log4j.appender.SURVEY.File = ${log.home}/logs/web/active.log
log4j.appender.SURVEY.Append = true
log4j.appender.SURVEY.layout = org.apache.log4j.PatternLayout
log4j.appender.SURVEY.layout.ConversionPattern = %d %-5p [%C{1}:%L] %m%n
log4j.logger.SURVEY=INFO,SURVEY
```
示例：
```
private Logger log = Logger.getLogger("SURVEY");
log.info("OK");
```
日志结果：
`2014-11-18 16:43:34,885 INFO  [SurveyController:18] OK`

Appender 为日志输出目的地，Log4j提供的appender有以下几种：
> org.apache.log4j.ConsoleAppender（控制台），
org.apache.log4j.FileAppender（文件），
org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件），
org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件），
org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）

打印参数:Log4J采用类似C语言中的printf函数的打印格式格式化日志信息，如下:
> %m 输出代码中指定的消息
%p 输出优先级，即DEBUG，INFO，WARN，ERROR，FATAL 
%r 输出自应用启动到输出该log信息耗费的毫秒数 
%c 输出所属的类目，通常就是所在类的全名 
%t 输出产生该日志事件的线程名 
%n 输出一个回车换行符，Windows平台为“/r/n”，Unix平台为“/n” 
%d 输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyy MMM dd HH:mm:ss,SSS}，输出类似：2002年10月18日 22:10：28，921
%l 输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数。举例：Testlog4.main(TestLog4.java:10)

---
```
$.ajax({
	url:"//admin.woqu.com/visa-stage/visa/add-submit",
	type: 'POST',
	dataType : 'json',
	contentType:"application/json",
	data: JSON.stringify(data),
	success:function(data){
		
	}
});
public void addSubmit(@RequestBody VisaSubmitForm visaSubmitForm, HttpServletRequest request, HttpServletResponse response)
```