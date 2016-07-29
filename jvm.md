# 深入理解Java虚拟机

每个Class文件的头a个宁节称为魔数（Magic Number），它的唯一作用是确定这个文件
足否为一个能被虚拟机接受的Class文件。很多文件存储标准中都使用魔数来进行身份识别
譬如图片格式，如gif或者Jpeg等在文件头中都存有魔数。使用魔数而不是扩展名来进行识
别主要是基于安全方面的考虑，因为文件扩展名可以随意地改动。文件格式的制定者可以自
由地选择魔数值，只要这个魔数值还没有被广泛采用过同时又不会引起混淆即可。Class文
件的魔数值为：OxCAFEBABE。

紧接着魔数的4个字节存储的是Class文件的版本号；第s和第6个字节是次版本号
（Minor Version）.第7和第8个字节是主版本号（Major Version）。


## JVM内存结构

- 程序计数器
- Java虚拟机栈
- 本地方法栈（Native Method Stack）
- 堆
- 方法区
- 直接内存

本地方法栈和Java虚拟机栈非常相似，区别是虚拟机栈为执行Java方法服务，本地方法栈为
执行Native方法服务。HotSpot将本地方法栈和虚拟机栈合二为一。

Java虚拟机永久代（Permanent Generation）
: 在HotSpot虚拟机中，使用永久代实现*方法区*，这将GC扩展至方法区，省去专门为方法区
  编写内存管理代码的工作。`-XX:MaxPermSize`设置方法区的最大空间。Java8移除了永久代，
  由MetaspaceSize代替，使用`-XX:MaxMetaspaceSize`设置最大空间。

字符串常量池
: All literal strings and string-valued constant expressions are interned.
  类被使用/方法被调用时，其中的字符串字面量才会被放到常量池。JDK 1.6及之前的版本中，
  常量池分配在永久代，而永久代的大小是固定的，所以很容易引发OutOfMemoryException。
  JDK 1.7之后常量池分配到堆中。