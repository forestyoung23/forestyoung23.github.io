---

title: Cache使用
date: 2020-09-08 01:00:25
categories: spring
tags: 缓存
---

![](https://oss.forestyoung.top/huangpu-river-5501210_1920.jpg)

<!--more-->

### 相关依赖

在 Spring 3.1 中引入了多 Cache 的支持，在 spring-context 包中已包含，所以只用引入spring-context包即可

### 使用注意

需要在启动类加上**@EnableCaching**注解

被缓存对象需要实现Serializable接口

### 参考博文

[Spring Boot 2.X(七)：Spring Cache 使用](https://juejin.im/post/6844903966615011335)

[Spring缓存注解@Cacheable、@CacheEvict、@CachePut使用](https://www.cnblogs.com/fashflying/p/6908028.html)

