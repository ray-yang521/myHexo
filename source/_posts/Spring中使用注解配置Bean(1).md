---
title: Spring中使用注解配置Bean(1)
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-08 17:06:27
id: 6
categories: Spring
---
## 引言：
在Spring中通过添加注解的方式来配置Bean，Spring容器会自动管理所扫描包(可以自行配置所要扫描的包的路径)下的标有@Component、@Controller、@Service、@Repository注解的类
- TestObject.java
- UserController.java
- UserService.java
- UserRepository.java
- UserRepositoryImpl.java
- Main.java
- beans-annotation.xml
<!-- more -->

### TestObject.java
```java
package com.yczlab.spring.beans.annotation;

import org.springframework.stereotype.Component;

@Component
public class TestObject {

}
```

### UserController.java
```java
package com.yczlab.spring.beans.annotation.controller;

import org.springframework.stereotype.Controller;

@Controller
public class UserController {
    public void execute() {
        System.out.println("UserController execute...");

    }
}
```

### UserService.java
```java
package com.yczlab.spring.beans.annotation.service;

import org.springframework.stereotype.Service;

@Service
public class UserService {
    public void add() {
        System.out.println("UserService add...");
    }
}
```

### UserRepository.java
```java
package com.yczlab.spring.beans.annotation.repository;

public interface UserRepository {

    void save();

}
```

### UserRepositoryImpl.java
```java
package com.yczlab.spring.beans.annotation.repository;

import org.springframework.stereotype.Repository;

//添加注解并设置bean在IOC容器中的名字，即id
@Repository("userRepository")
public class UserRepositoryImpl implements UserRepository {
    @Override
    public void save() {
        System.out.println("UserRepository Save...");
    }
}
```

### Main.java
```java
package com.yczlab.spring.beans.annotation;

import com.yczlab.spring.beans.annotation.controller.UserController;
import com.yczlab.spring.beans.annotation.repository.UserRepository;
import com.yczlab.spring.beans.annotation.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans-annotation.xml");

        TestObject testObject = (TestObject) context.getBean("testObject");
        System.out.println(testObject);

        UserController userController = (UserController) context.getBean("userController");
        System.out.println(userController);

        UserService userService = (UserService) context.getBean("userService");
        System.out.println(userService);

        UserRepository userRepository = (UserRepository) context.getBean("userRepository");
        System.out.println(userRepository);

    }
}
```

### beans-annotation.xml配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--指定Spring Ioc容器扫描的包-->
    <!--
        <context:component-scan base-package="com.yczlab.spring.beans.annotation"/>
    -->


    <!--可以通过resource-pattern指定扫描的资源-->
    <!--
        <context:component-scan
                base-package="com.yczlab.spring.beans.annotation"
                resource-pattern="repository/*.class">
        </context:component-scan>
    -->

    <!-- context:exclude-filter 子节点指定排除哪些指定表达式的组件 -->
    <!--
        <context:component-scan base-package="com.yczlab.spring.beans.annotation">
            <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
        </context:component-scan>
    -->


    <!-- context:include-filter 子节点指定包含哪些指定表达式的组件，该子节点需要与use-default-filters="false"配合使用 -->
    <!--
        <context:component-scan base-package="com.yczlab.spring.beans.annotation" use-default-filters="false">
            <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        </context:component-scan>
    -->

    <!--type="assignable"的context:exclude-filter子节点-->
    <!--
        <context:component-scan base-package="com.yczlab.spring.beans.annotation">
            <context:exclude-filter type="assignable" expression="com.yczlab.spring.beans.annotation.repository.UserRepository"/>
        </context:component-scan>
    -->

    <!--type="assignable"的context:include-filter子节点，该子节点需要与use-default-filters="false"配合使用-->
    <context:component-scan base-package="com.yczlab.spring.beans.annotation" use-default-filters="false">
        <context:include-filter type="assignable" expression="com.yczlab.spring.beans.annotation.repository.UserRepository"/>
    </context:component-scan>

</beans>
```