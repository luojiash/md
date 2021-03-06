# Java 类加载

## 类加载过程

1. 加载：读取二进制字节流（文件、网络、动态代理），将字节流转化为方法区的运行时数据结构，生成Class对象。
2. 验证：校验字节流合法性。
3. 准备：为类变量分配内存并设置初始值。
4. 解析：将符号引用替换为直接引用。
5. 初始化：Java代码级别初始化，执行类构造器 `<clinit>()`方法。
6. 使用
7. 卸载

**Class对象分配在哪块内存区域？**

- [类加载的过程][1]
- [Class实例在堆中还是方法区中？ - 小菜变大鸟 - 博客园][2]
- [Class 对象 - Java 类型信息 · Thinking in Java][3]
- [JDK 1.8 下的 java.lang.Class 对象和 static 成员变量在堆还是方法区？_Xu_JL1997的博客-CSDN博客_class对象在堆还是方法区][4]

参考以上文章，JDK1.6 Class对象分配在方法区，JDK1.7、JDK1.8改为分配在堆。

[Java static变量保存在哪？_晚晴小筑-CSDN博客_java 静态变量存储在哪里][5]，这篇说明了JDK1.8静态变量包含在Class对象里，即也在堆上。

## 类加载器

1. 启动类加载器（Bootstrap ClassLoader）
2. 扩展类加载器（Extension ClassLoader）
3. 应用程序类加载器（Application ClassLoader）
4. 自定义类加载器（User ClassLoader）

双亲委派机制：下层类加载器先委托上层类加载器加载类，上层加载失败后再执行自己的类加载逻辑。一个类由加载它的类加载器和它本身确定唯一性，该机制保证了一个类的唯一性。

反例：

1. 热部署，类加载委托关系更复杂，类似网状。
2. JNDI/JDBC由启动类加载器加载，但启动类加载器无法加载部署在应用程序classpath下的实现代码，因而引入了线程上下文类加载器，该加载器持有子类加载器的引用，可以加载具体实现代码。

[1]: https://doocs.gitee.io/jvm/#/docs/09-load-class-process
[2]: https://www.cnblogs.com/xy-nb/p/6773051.html
[3]: https://jverson.com/thinking-in-java/jvm/java-reflection-class.html#hotpot-jvm-class-%E5%AF%B9%E8%B1%A1%E6%98%AF%E5%9C%A8%E6%96%B9%E6%B3%95%E5%8C%BA%E8%BF%98%E6%98%AF%E5%A0%86%E4%B8%AD
[4]: https://blog.csdn.net/Xu_JL1997/article/details/89433916
[5]: https://blog.csdn.net/x_iya/article/details/81260154