---
title: Spring中的泛型依赖注入
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-08 17:55:20
id: 8
categories: Spring
---
## 引言：
Spring可以为子类注入子类对应的泛型类型的成员变量的引用
- User.java
- BaseService.java
- UserService.java
- BaseRepository.java
- UserRepository.java
- Main.javva
- beans-generic-di.xml
<!-- more -->

### User.java
```java
package com.yczlab.spring.beans.generic.di;

public class User {

}

```

### BaseService.java
```java
package com.yczlab.spring.beans.generic.di;

import org.springframework.beans.factory.annotation.Autowired;

public class BaseService<T> {

    //该注解会被子类继承
    @Autowired
    protected BaseRepository<T> repository;

    public  void add() {
        System.out.println("add...");
        System.out.println(repository);
    }

}
```

### UserService.java
```java
package com.yczlab.spring.beans.generic.di;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

//父类没有加入Spring的自动管理，子类使用@Service注解加入Spring的自动管理
@Service
public class UserService extends BaseService<User> {
    //继承@AutoWired注解后，会自动配置好BaseRepository<User>类型的实例Bean
    //相当于如下代码：
    /*
    @Autowired
    protected BaseRepository<User> repository;
    **/
}
```

### BaseRepository.java
```java
package com.yczlab.spring.beans.generic.di;

public class BaseRepository<T> {

}
```

### UserRepository.java
```java
package com.yczlab.spring.beans.generic.di;

import org.springframework.stereotype.Repository;

//父类没有加入Spring的自动管理，子类使用@Repository注解加入Spring的自动管理
@Repository
public class UserRepository extends BaseRepository<User> {
}
```

### Main.javva
```java
package com.yczlab.spring.beans.generic.di;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans-generic-di.xml");

        UserService userService = (UserService) context.getBean("userService");
        userService.add();
    }
}
```

### beans-generic-di.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.yczlab.spring.beans.generic.di"/>

</beans>
```