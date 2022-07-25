## Spring

### 模块

#### Spring Core

核心模块， Spring 其他所有的功能基本都需要依赖于该类库，主要提供 IoC 依赖注入功能的支持

#### Spring Aspects

为与 AspectJ 的集成提供支持

#### Spring AOP

提供了面向切面的编程实现

#### Spring Data Access/Integration 

###### spring-jdbc

提供了对数据库访问的抽象 JDBC

###### spring-tx

提供对事务的支持

###### spring-orm

提供对 Hibernate 等 ORM 框架的支持

###### spring-oxm

提供对 Castor 等 OXM 框架的支持

###### spring-jms

Java 消息服务

#### Spring Web

###### spring-web

对 Web 功能的实现提供一些最基础的支持

###### spring-webmvc

 提供对 Spring MVC 的实现

###### spring-websocket

提供了对 WebSocket 的支持，WebSocket 可以让客户端和服务端进行双向通信

###### spring-webflux

提供对 WebFlux 的支持,新的响应式框架

#### Spring Test

### IoC（Inverse of Control:控制反转）

将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理

将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。

### AOP(Aspect-Oriented Programming:面向切面编程)

#### Spring AOP

基于动态代理,运行时增强

###### 实现了某个接口

使用 JDK Proxy，去创建代理对象

###### 没有实现接口

使用 Cglib 生成一个被代理对象的子类来作为代理

#### AspectJ AOP

编译时增强

### Spring bean

bean 代指的就是那些被 IoC 容器所管理的对象

#### 作用域

###### singleton

唯一 bean 实例，Spring 中的 bean 默认都是单例的，对单例设计模式的应用

###### prototype

每次请求都会创建一个新的 bean 实例

###### request

每一次 HTTP 请求产生，仅在当前 HTTP request 内有效

###### session

每一次来自新 session 的 HTTP 请求都会产生，仅在当前 HTTP session 内有效

###### global-session

全局 session 作用域，仅仅在基于 portlet 的 web 应用中才有意义，Spring5 已经没有了

### 注解

#### @Component 和 @Bean 

@Component 注解作用于类，而@Bean注解作用于方法

@Component通常是通过类路径扫描来自动侦测以及自动装配到 Spring 容器中，@Bean告诉了 Spring 这是某个类的实例，当我需要用它的时候还给我

@Bean 注解比 @Component 注解的自定义性更强，当我们引用第三方库中的类需要装配到 Spring容器时只能通过 @Bean

## SpringMVC



## SpringBoot



## Mybatis



## SpringCloud

### Eureka 服务发现框架

### Ribbon 进程内负载均衡器

### Open Feign 服务调用映射

### Hystrix 服务降级熔断器

### Zuul 微服务网关

### Config 微服务统一配置中心

### Bus 消息总线

## 服务器

### Nginx

### Tomcat



