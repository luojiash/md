# 自动装箱和拆箱

自动装箱和拆箱是Java的语法糖，在编程中可以忽略这个过程，但如果不了解其中的原理，不小心就会踩坑了。

## 装箱

`Integer a = 1;`
装箱会将以上代码展开为
`Integer a = Integer.valueof(1);`

```
Integer a = 2; 
Integer b = 2;
Integer c = 200; 
Integer d = 200;
System.out.println(a==b);//true
System.out.println(c==d);//false
```
以上代码中相同的逻辑为什么会返回不同的结果呢？

```
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```
查看`valueof`方法的源码，[IntegerCache.low, IntegerCache.high]区间的整数会返回缓存中的对象，IntegerCache.low预设值是-128，运行期不能修改。IntegerCache.high预设值是127，使用`-Djava.lang.Integer.IntegerCache.high`参数修改。由于2在[-128, 127]区间，所以`a`、`b`是缓存中的同一个对象，`==`比较返回`true`，`c`、`d`都是新创建的对象，内存地址不相同，`==`比较返回`false`。

为了避免引起疑惑，**不要使用`==`、`!=`比较对象值是否相同，应该使用`equals`**，如`a.equals(b)`
Java8可以使用`Objects.equals(a, b)`避免引起`NullPointerException`

## 拆箱

```
Integer i = new Integer(199);
int j = i;
```
以上代码编译时会展开为
```
Integer i = new Integer(199);
int j = i.intValue();
```
所以如果`i`为`null`，会引发`NullPointerException`

做`==`比较时，若一方为基本类型，另一方为对象，也会发生拆箱。
```
Integer a = null;
System.out.println(3==a);
```
因为拆箱将`3==a`展开为`3==a.intValue()`，所以上面的代码会报空指针错误。