---
title: MySQL连接异常记录
date: 2021-04-02 17:08:35
tags:
---

![](https://oss.forestyoung.top/freedom-4737919_1920.jpg)

<!--more-->

应用启动失败，抛出java.sql.SQLException: Access denied for user ''@'localhost' (using password: YES)

```
spring.datasource.url=jdbc:mysql://localhost:3306/multi_tenant
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
#spring.datasource.username=root
spring.datasource.name=root
spring.datasource.password=dy123456
```

发现是数据连接配置错误

```properties
spring.datasource.name=root
```

应该配置为

```properties
spring.datasource.username=root
```

