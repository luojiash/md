# Java 内存区域

每个线程都有独立的程序计数器、栈、本地方法栈。

## 堆

对象主要在堆上分配，也可能栈上分配（逃逸分析）。
Java7开始把原本在永久代的字符串常量池（静态变量、运行时常量池）移到堆，因为方法区内存大小是有限制的（-XX:MaxPermSize），容易导致内存溢出。
Eden和Survivor的大小比例是8:1。
`-XX:NewRatio=2`指定老年代（Old Generation）内存大小是新生代（Young Generation）的2倍。

1. 新生代（eden/from survivor/to survivor）
2. 老年代

## 方法区

保存类的相关信息，还保存了代码缓存，比如JIT编译生成的代码。

Java7：称为永久代（PermGen space：Permanent Generation space），属于堆的一部分，和老年代一起进行GC，但GC效果并不好。

Java8：该区域移到Metaspace（-XX:MetaspaceSize），内存分配在本地堆内存（native heap），可以进行单独GC。Metaspace摆脱了java.lang.OutOfMemoryError: PermGen，使这块内存能更灵活地管理。

## 本地方法栈

native方法线程栈。

## Java虚拟机栈

线程执行栈，每个线程都有自己的专属栈。-Xss设置栈大小。

- 局部变量表
- 操作数栈
- 动态链接
- 方法出口

## 程序计数器

记录线程正在执行的虚拟机字节码的地址；如果正在执行native方法，该值为空。每个线程都有独立的程序计数器。

## 直接内存

直接操作操作系统内存，避免Java堆和直接内存的数据复制，比如NIO的DirectByteBuffer。读写操作快，但创建销毁较慢，适用于IO操作，如文件、网络处理等。