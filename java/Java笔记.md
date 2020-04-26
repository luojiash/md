# Java笔记

---


## java.lang.UnsupportedOperationException
用`Arrays.asList`将数组转化为列表后，列表不支持修改操作，否则抛出异常。

*解决方案*
```
String[] arr = new String[]{"a","b","c"};  
List<String> list = new ArrayList(Arrays.asList(arr));
list.remove("a");
```


## 初始化列表
```
List<string> list = new ArrayList<string>(){{  
    add("string1");
    add("string2");
    add("stringN");
}}; 
```
这种方法是用了匿名内部类语法，外层大括号建立了ArrayList的一个匿名子类，内层括号是匿名子类的对象构造块。

*使用Java8 stream*
`List<String> list=Stream.of("a","b","c").collect(Collectors.toList());`


## Java获取参数名

Java 8之前编译时没有保留参数名，所以不能获取参数名。
```
public static void main(String[] args) {
	class Car {
		public int drive(int speed, String destination) {
			return 0;
		}
	}

	Method[] methods = Car.class.getDeclaredMethods();

	// 编译时加上 -parameters 选项才会保留参数名，否则只会得到无意义的 arg0 arg1
	for (Parameter parameter : methods[0].getParameters()) {
		System.out.println(parameter.getName());
	}

	// Spring
	LocalVariableTableParameterNameDiscoverer discoverer = new LocalVariableTableParameterNameDiscoverer();
	for (String param : discoverer.getParameterNames(methods[0])) {
		System.out.println(param);
	}
}
```


## 将X时区的时间转换为当前时区的时间

例如：将东九区的时间`2016-06-07 16:53:08`转换为东八区的时间`2016-06-07 15:53:08`
```
public static void parseWithZone() throws ParseException {
	String format = "yyyy-MM-dd HH:mm:ss";
	DateFormat dateFormat = new SimpleDateFormat(format);
	dateFormat.setTimeZone(TimeZone.getTimeZone("GMT+09:00"));
	Date date = dateFormat.parse("2016-06-07 16:53:08");
	System.out.println(DateFormatUtils.format(date, format));
	// 2016-06-07 15:53:08
}
```


## 字符串常量缓存

Java会将字符串常量表达式（如`"计" + "算机"`，`fa + "算机"`）和字符串常量（String literal，如`"计算机"`）缓存在字符串常量池，多个相同的字符串常量引用同一个内存地址的数据。
```
public static void testStringEqual() {
	System.out.println("计算机" == "计" + "算机"); // true,编译期将表达式 "计"+"算机" 合成 "计算机"
	System.out.println("计算机" == "计" + new String("算机")); // false

	String a = "计";
	final String fa = "计";
	System.out.println(a + "算机" == "计算机"); // false
	System.out.println(fa + "算机" == "计算机"); // true,final类型在编译时合并成"计算机"
}
```

### Java char 类型支持unicode，占用2字节

```
public static final char MAX_VALUE = '\uFFFF';
```
