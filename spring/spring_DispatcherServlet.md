基于 spring-webmvc 4.3.11.RELEASE

org.springframework.web.servlet.DispatcherServlet#doDispatch 执行请求分发操作，
大致分为以下几个步骤：
1. 找到处理请求的 handler（getHandler）
2. 找到 handler adapter（getHandlerAdapter）
3. 执行 handler 的前置操作，失败的话马上返回（mappedHandler.applyPreHandle）
4. 调用 handler 执行真正的业务操作（ha.handle）
5. 执行 handler 的后置操作（mappedHandler.applyPostHandle）

DispatcherServlet 在启动时会执行一系列初始化操作，其中包括 HandlerMapping 列表
和 HandlerAdapter 列表的初始化。HandlerMapping 包含各类 handler 的映射信息，从
这个列表中找到处理请求的 handler，注意每个 HandlerMapping 有优先级顺序，会先从
优先级高的 HandlerMapping 查找。最常见的 handler 就是 controller 被 @RequestMapping 
标注的方法。

handler 的前置和后置操作实际上就是调用一系列拦截器（HandlerInterceptor），这是
典型的过滤器模式。拦截器在步骤1和 handler 一起封装成对象（HandlerExecutionChain）
返回。

handler 为什么需要一个 adapter 呢？因为 handler 是 Object 类型，通过 handler adapter
调用 handler，DispatcherServlet 就不需要依赖具体的 handler 类型。这种设计使不同框架
的 handler 都能工作，HandlerAdapter 不是为应用开发者准备的，而是为框架开发者准备的。
