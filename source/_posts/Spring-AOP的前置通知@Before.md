---
title: Spring AOP的前置通知@Before
author: YangChongZhi
tags: [Spring,Java]
date: 2020-03-10 22:36:38
id: 1
categories: Spring
---
## 引言：
Spring AOP可以使用@Before注解来实现前置通知, 在方法开始执行之前执行一段代码（可以用来做日志）
- ArithmeticCalculator.java
- ArithmeticCalculatorImpl.java
- LoggingAspect.java
- Main.java
- applicationContext.xml

<!-- more -->

### ArithmeticCalculator.java
```java
package com.yczlab.spring.aop.impl;

public interface ArithmeticCalculator {
    int add(int i, int j);
    int sub(int i, int j);
    int mul(int i, int j);
    int div(int i, int j);
}
```

### ArithmeticCalculatorImpl.java
```java
package com.yczlab.spring.aop.impl;

import org.springframework.stereotype.Component;

@Component
public class ArithmeticCalculatorImpl implements ArithmeticCalculator {
    @Override
    public int add(int i, int j) {
        int result = i + j;
        return result;
    }

    @Override
    public int sub(int i, int j) {
        int result = i - j;
        return result;
    }

    @Override
    public int mul(int i, int j) {
        int result = i * j;
        return result;
    }

    @Override
    public int div(int i, int j) {
        int result = i / j;
        return result;
    }
}
```

### LoggingAspect.java
```java
package com.yczlab.spring.aop.impl;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

import java.util.Arrays;
import java.util.List;

//把这个类声明为一个切面：需要把该类放入到IOC容器中，再声明为一个切面
@Aspect //声明为一个切面，使用该注解需要“aspectjweaver-xxx.jar”包的支持，"xxx"代表版本
@Component //放入IOC容器中
public class LoggingAspect {

    //声明该方法是一个前置通知：在目标方法开始之前执行
    //如果这样写execution(public int com.yczlab.spring.aop.impl.ArithmeticCalculator.add(int, int))
    //那么只对ArithmeticCalculator.add方法起作用
    @Before("execution(public int com.yczlab.spring.aop.impl.ArithmeticCalculator.*(int, int))")
    public void beforeMethod(JoinPoint joinPoint) {
        //可以通过JoinPoint参数来访问到连接的细节，如方法名称和参数值等
        String methodName = joinPoint.getSignature().getName();
        List<Object> args = Arrays.asList(joinPoint.getArgs());
        System.out.println("The method " + methodName + " begins with " + args);
    }

}
```

### Main.java
```java
package com.yczlab.spring.aop.impl;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {

        //1、创建Spring的IOC容器
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

        //2、从IOC容器中获取bean的实例
        ArithmeticCalculator arithmeticCalculator = context.getBean(ArithmeticCalculator.class);

        //3、使用bean
        int result = arithmeticCalculator.add(3, 6);
        System.out.println("result: " + result);

        System.out.println("*****************");
        result = arithmeticCalculator.div(12, 6);
        System.out.println("result" + result);
    }
}
```

### applicationContext.xml配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--配置自动扫描的包-->
    <context:component-scan base-package="com.yczlab.spring.aop.impl"/>

    <!--使AspectJ注解起作用：自动为匹配的类生成代理对象-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```
