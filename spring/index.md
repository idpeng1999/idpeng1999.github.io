# Spring

## Spring、SpringMVC、SpringBoot区别是什么？

### SpringBoot 到 SpringMVC 的改进是：

1. 自动化配置: SpringMVC通过xml配置，手动指定。SpringBoot通过注解或者自动化扫描当前项目中存在哪些第三方包。
2. SpringBoot自带Servlet容器，可以直接启动。SpringMVC不自带，需要额外的配置，例如TomCat
3. SpringBoot能直接打成jar包，可直接启动

### 区别

* Spring 就像一个大家族，有众多衍生产品例如 Boot，Security，JPA等等。但他们的基础都是Spring 的 IOC 和 AOP，IOC提供了依赖注入的容器， 而AOP解决了面向切面的编程 
* Spring MVC是基于 Servlet 的一个 MVC 框架，主要解决 WEB 开发的问题， 
* 为了简化开发者的使用，Spring社区创造性地推出了Spring Boot，它遵循约定优于配置， 
* Spring MVC和Spring Boot都属于Spring，Spring MVC 是基于Spring的一个 MVC 框架，而Spring Boot 是基于Spring的一套快速开发整合包

## Bean生命周期

![Bean生命周期](/img/spring/1.png)


## Spring容器

![spring容器](/img/spring/spring容器.png)


