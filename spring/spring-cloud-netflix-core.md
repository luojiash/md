
使用`@FeignClient`标注服务接口，`FeignClientFactoryBean`生成接口的实现类。

`FeignClientsConfiguration`声明了 bean `Feign.Builder`，`feign.hystrix.enabled` 为true时
执行`HystrixFeign.builder()`创建该bean，支持hystrix特性，否则是普通的`Feign.builder()`。

`FeignRibbonClientAutoConfiguration` 配置了`Client`对象的创建顺序，优先级是apache的`HttpClient`->
`OkHttpClient`->`HttpURLConnection`
```
//Order is important here, last should be the default, first should be optional
// see https://github.com/spring-cloud/spring-cloud-netflix/issues/2086#issuecomment-316281653
@Import({ HttpClientFeignLoadBalancedConfiguration.class,
		OkHttpFeignLoadBalancedConfiguration.class,
		DefaultFeignLoadBalancedConfiguration.class })
```


---
sleuth 会将feign使用的一些对象进行包装，添加sleuth的功能特性，通过实现`BeanPostProcessor`
接口，可以对对象重新封装，比如`FeignContextBeanPostProcessor`将`FeignContext`包装为`TraceFeignContext`，
`FeignBeanPostProcessor`包装了`feign.Client`。
