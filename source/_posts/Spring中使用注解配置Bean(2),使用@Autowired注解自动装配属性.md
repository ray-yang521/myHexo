---
title: 'Spring中使用注解配置Bean(2),使用@Autowired注解自动装配属性'
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-08 17:38:37
id: 7
categories: Spring
---
## 引言：
在Spring的配置文件中使用```<context:component-scan>```元素时，会自动注册 AutowireAnnotationBeanPostProcesser 实例，该实例会自动装配具有 @Autowired 和 @Resource、@Inject 注解标注的属性
- TestObject.java
- UserController.java
- UserService.java
- UserRepository.java
- UserRepositoryImpl.java
- UserJdbcRepositoryImpl.java
- Main.java
- beans-annotation.xml
<!-- more -->

### TestObject.java
```java
package com.yczlab.spring.beans.annotation;

import org.springframework.stereotype.Component;

public class TestObject {

}
```

### UserController.java
```java
package com.yczlab.spring.beans.annotation.controller;

import com.yczlab.spring.beans.annotation.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Controller;

@Controller
public class UserController {

    //@Autowired注解可以放在属性上
    @Autowired
    private UserService userService;

    public void execute() {
        System.out.println("UserController execute...");
        userService.add();
    }
}
```

### UserService.java
```java
package com.yczlab.spring.beans.annotation.service;

import com.yczlab.spring.beans.annotation.repository.UserJdbcRepositoryImpl;
import com.yczlab.spring.beans.annotation.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    //@Autowired注解直接放在属性上
    @Autowired
    @Qualifier("userRepositoryImpl")//特别指定IOC容器中的哪个bean实例装配到该属性
    private UserRepository userRepository;

    @Autowired
    @Qualifier("userJdbcRepositoryImpl")
    private UserRepository userJdbcRepository;

    //@Autowired注解也可以放在方法上
    /*
    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    */

    //@Autowired注解也可以放在形参前面
    /*
    @Autowired
    public void setUserRepository(@Autowired UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    */

    public void add() {
        System.out.println("UserService add...");
        userRepository.save();
        userJdbcRepository.save();
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

import com.yczlab.spring.beans.annotation.TestObject;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;

//添加注解并设置bean在IOC容器中的名字，即id。也可以使用默认名字userRepositoryImpl
//@Repository("userRepository")
@Repository
public class UserRepositoryImpl implements UserRepository {

    //设置@Autowired注解的required属性为false，当容器中没有testObject的Bean实例时也不会报错，不然会报错
    @Autowired(required = false)
    private TestObject testObject;

    @Override
    public void save() {
        System.out.println("UserRepository Save...");
        System.out.println("testObject: " + testObject);
    }
}
```

### UserJdbcRepositoryImpl.java
```java
package com.yczlab.spring.beans.annotation.repository;

import org.springframework.stereotype.Repository;

@Repository
public class UserJdbcRepositoryImpl implements UserRepository {
    @Override
    public void save() {
        System.out.println("UserJdbcRepository save...");
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

        /*//通过注解的方式配置bean（1）
        TestObject testObject = (TestObject) context.getBean("testObject");
        System.out.println(testObject);

        UserController userController = (UserController) context.getBean("userController");
        System.out.println(userController);

        UserService userService = (UserService) context.getBean("userService");
        System.out.println(userService);

        UserRepository userRepository = (UserRepository) context.getBean("userRepository");
        System.out.println(userRepository);
        */

        //通过注解的方式配置bean（2），主要是bean之间的关联关系
        UserController userController = (UserController) context.getBean("userController");
        System.out.println(userController);
        userController.execute();
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

    <!--
        指定Spring Ioc容器扫描的包
        Spring容器会自动管理所扫描包下的标有@Component、@Controller、@Service、@Repository注解的类
    -->
    <context:component-scan base-package="com.yczlab.spring.beans.annotation"/>


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
    <!--
        <context:component-scan base-package="com.yczlab.spring.beans.annotation" use-default-filters="false">
            <context:include-filter type="assignable" expression="com.yczlab.spring.beans.annotation.repository.UserRepository"/>
        </context:component-scan>
    -->


    <!--组件装配
        <context:component-scan>元素还会自动注册 AutowireAnnotationBeanPostProcesser 实例，
        该实例会自动装配具有 @Autowired 和 @Resource、@Inject 注解的属性

        @Autowired注解：自动装配具有兼容类型的单个Bean属性
        - 构造器，普通字段（即使是非public），一切具有参数的方法都可以应用@Autowired注解
        - 默认情况下，所有使用@Autowired注解的属性都需要被设置。当Spring找不到匹配的Bean装配属性时，会抛出异常。
          若某一属性允许不被设置，可以设置@Autowired注解的required属性为false
        - 默认情况下, 当 IOC 容器里存在多个类型兼容的 Bean 时, 通过类型的自动装配将无法工作.
          此时可以在 @Qualifier 注解里提供 Bean 的名称. Spring 允许对方法的入参标注 @Qualifiter 已指定注入 Bean 的名称
        - @Authwired 注解也可以应用在数组类型的属性上, 此时 Spring 将会把所有匹配的Bean进行自动装配.
        - @Authwired 注解也可以应用在集合属性上, 此时Spring读取该集合的类型信息, 然后自动装配所有与之兼容的 Bean.
        - @Authwired 注解用在java.util.Map上时, 若该Map的键值为String,那么Spring将自动装配与之Map值类型兼容的Bean
          此时Bean的名称作为键(key)，Bean实例作为值(value)

        Spring 还支持 @Resource 和 @Inject 注解，这两个注解和 @Autowired 注解的功用类似

        @Resource注解：要求提供一个 Bean名称的属性，若该属性为空，则自动采用标注处的变量或方法名作为Bean的名称
        @Inject注解：和@Autowired注解一样也是按类型匹配注入的 Bean，但没有reqired属性
        建议使用 @Autowired 注解
    -->

</beans>
```