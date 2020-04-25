# Spring 架构

Spring的核心组件包括beans、core和context，这三个组件构成了Spring的核心运行环境，实现了关键功能控制反转（IoC，Inversion of Control）和依赖注入（DI，Dependency Injection）。其他组件都是在此基础上进行扩展。

beans是典型的工厂模式，由BeanFactory创建对象。

core是创建Spring运行环境所需要的工具集合，比如定义了资源读取的ResourceLoader。

context使用core的工具建立对象的依赖关系，初始化应用运行环境，由此建立了Spring运行环境的基石。

`org.springframework.context.support.AbstractApplicationContext#refresh`描述了加载context的关键步骤，是各种实现类的模板方法。