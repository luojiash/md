
## @Autowired

默认byType注入, 可以使用在setter, constructor, method. 
方法由对象主动调用，肯定会传参数，使用@Autowired会使函数执行两次，错误的用法。除非用在不是由对象主动调用的函数上，比如已经加上 @PreDestroy或 @PostConstruct 的函数。
@Autowired不常用，它按类型装配，容易产生错误，使用 @Resource代替。

## @Resource

默认byName, 使用在setter
@Resource装配顺序
1. 如果同时指定了name和type，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常
2. 如果指定了name，则从上下文中查找名称（id）匹配的bean进行装配，找不到则抛出异常
3. 如果指定了type，则从上下文中找到类型匹配的唯一bean进行装配，找不到或者找到多个，都会抛出异常
4. 如果既没有指定name，又没有指定type，则自动按照byName方式进行装配；如果没有匹配，则回退为一个原始类型进行匹配(by-type)，如果匹配则自动装配；




